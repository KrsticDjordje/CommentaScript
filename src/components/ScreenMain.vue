<template>
    <div class="screen-recorder-wrap">

        <div v-if="!isRecording && !isRecordingStarted" class="q-pa-md">
            <h6 class="text-h6 q-my-md text-white">Enter Match Details</h6>

            <q-form @submit.prevent="startRecording">

                <q-input v-model="matchTitle" label="Match Title" filled :rules="[val => !!val || 'Title is required']"
                    class="q-mb-md" />

                <q-input v-model="startTime" label="Start Time" type="time" filled
                    :rules="[val => !!val || 'Start time is required']" class="q-mb-md" />

                <q-select v-model="selectedSport" :options="sports" label="Select Sport" filled
                    :rules="[val => !!val || 'Sport is required']" class="q-mb-md" />

                <q-btn label="Start Recording" color="secondary" type="submit" class=" q-mt-md" />
            </q-form>
        </div>

        <div v-else>
            <video class="screen-player" ref="videoRef" autoplay playsinline muted></video>
            <div>
                <h5 class="time q-my-md">{{ formattedTime }}</h5>
                <q-btn v-if="isRecording" label="Stop Recording" color="negative" @click="stopRecording" class="" />
                <q-btn v-if="!isRecording" label="Start Recording" color="secondary" @click="startRecording"
                    class="q-mt-md" />
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import axios from 'axios';

const mediaStream = ref(null);
const videoRef = ref(null);
const isRecording = ref(false);
const isRecordingStarted = ref(false);
const elapsedTime = ref(0);
let interval = null;
let recorder = null;


const matchTitle = ref('');
const startTime = ref('');
const selectedSport = ref('');
const sports = ref([
    { label: 'Football', value: 'fudbal' },
    { label: 'Basketball', value: 'basket' },
    { label: 'Tennis', value: 'tenis' },
]);


const startRecording = async () => {
    if (!matchTitle.value || !startTime.value || !selectedSport.value) {
        alert('Please fill in all fields.');
        return;
    }

    isRecordingStarted.value = true;
    isRecording.value = true;
    elapsedTime.value = 0;


    mediaStream.value = await navigator.mediaDevices.getDisplayMedia({
        video: true,
        audio: true,
    });

    if (videoRef.value) {
        videoRef.value.srcObject = mediaStream.value;
    }

    createNewRecorder();

    interval = setInterval(() => {
        elapsedTime.value++;
    }, 1000);


    sendMatchDetailsToAPI();
};


const stopRecording = () => {
    isRecording.value = false;
    clearInterval(interval);

    if (recorder) {
        recorder.stop();
    }

    if (mediaStream.value) {
        mediaStream.value.getTracks().forEach((track) => track.stop());
    }

    if (videoRef.value) {
        videoRef.value.srcObject = null;
    }
};


const createNewRecorder = () => {
    if (recorder) {
        recorder.ondataavailable = null;
        recorder.stop();
    }

    recorder = new MediaRecorder(mediaStream.value);

    recorder.ondataavailable = (event) => {
        if (event.data.size > 0) {
            processVideoChunk(event.data);
        }
    };

    recorder.start(15000); // Vreme slanja videa na API
};

const processVideoChunk = (data) => {
    const blob = new Blob([data], { type: 'video/webm' });

    // saveVideoLocally(blob);

    uploadVideo(blob);

    if (isRecording.value) {
        createNewRecorder();
    }
};

const saveVideoLocally = (blob) => {
    const videoURL = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = videoURL;
    link.download = `recorded-video-${Date.now()}.mp4`;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
};

const uploadVideo = async (blob) => {
    const formData = new FormData();
    formData.append('video', blob);
    formData.append('title', matchTitle.value);
    formData.append('start_time', startTime.value);
    formData.append('sport', selectedSport.value);

    try {
        const response = await axios.post('https://verbumscript.app/v1/postVideo', formData, {
            headers: {
                'Content-Type': 'multipart/form-data',
            },
        });

        const data = response.data;
        console.log('Message from API:', data);


        saveToLocalStorage(data);
    } catch (error) {
        console.error('Error uploading video chunk:', error);
    }
};


const saveToLocalStorage = (newData) => {
    const storedData = JSON.parse(localStorage.getItem('playByPlayData')) || [];
    storedData.push(newData);
    localStorage.setItem('playByPlayData', JSON.stringify(storedData));
};

const formatTime = (seconds) => {
    const hours = String(Math.floor(seconds / 3600)).padStart(2, '0');
    const minutes = String(Math.floor((seconds % 3600) / 60)).padStart(2, '0');
    const secs = String(seconds % 60).padStart(2, '0');
    return `${hours}:${minutes}:${secs}`;
};

const formattedTime = computed(() => formatTime(elapsedTime.value));

onMounted(() => {
    if (!navigator.mediaDevices || !navigator.mediaDevices.getDisplayMedia) {
        alert('Vaš pretraživač ne podržava ovu funkcionalnost.');
    }
});
</script>

<style scoped>
.screen-recorder-wrap {
    align-self: center;
    text-align: -webkit-center;
}

.screen-player {
    width: auto;
    height: 250px;
    border: 1px solid rgb(214 214 214 / 18%);
    background: #352b3d;
}

.time {
    color: white;
}

.q-field--filled .q-field__control {
    background: #ffffff79 !important;
}
</style>