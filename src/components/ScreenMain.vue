<template>
    <div class="screen-recorder-wrap">
        <h6 class="time q-my-md">Barcelona - Real Madrid 24.10.2024. </h6>
        <video class="screen-player" ref="video" :srcObject="mediaStream" autoplay playsinline></video>
        <div>
            <h5 class="time q-my-md">{{ formattedTime }}</h5>
            <q-btn v-if="!isRecording" label="Start Recording" color="secondary" @click="startRecording" />
            <q-btn v-if="isRecording" label="Stop Recording" color="negative" @click="stopRecording" />
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted, watch, computed } from 'vue';
import { QBtn } from 'quasar';

const mediaStream = ref(null);
const isRecording = ref(false);
const elapsedTime = ref(0);
let interval = null;
let recorder = null;

const startRecording = async () => {
    isRecording.value = true;
    elapsedTime.value = 0;

    // Inicijalizuj snimanje
    mediaStream.value = await navigator.mediaDevices.getDisplayMedia({
        video: true,
        audio: true
    });

    // Kreiraj MediaRecorder
    recorder = new MediaRecorder(mediaStream.value);

    // Dodaj događaj za prikupljanje i slanje podataka svakih 5 sekundi
    recorder.ondataavailable = (event) => {
        if (event.data.size > 0) {
            uploadVideo([event.data]);
        }
    };

    // Pokreni snimanje sa timeslice parametrom od 5000 ms (5 sekundi)
    recorder.start(5000);

    // Interval za prikaz vremena
    interval = setInterval(() => {
        elapsedTime.value++;
    }, 1000);
};

const stopRecording = async () => {
    isRecording.value = false;
    clearInterval(interval);

    // Zaustavi snimanje i pošalji poslednji deo videa
    recorder.stop();

    // Zaustavi sve streamove
    mediaStream.value.getTracks().forEach(track => track.stop());
};

const uploadVideo = async (recordedChunks) => {
    const blob = new Blob(recordedChunks, { type: 'video/webm' });
    const formData = new FormData();
    formData.append('video', blob, 'recorded-video.webm');

    console.log('Sending video chunk to API:', [...formData.entries()]);

    try {
        const response = await fetch('YOUR_API_ENDPOINT_HERE', {
            method: 'POST',
            body: formData
        });
        const data = await response.json();
        console.log('Video chunk uploaded successfully:', data);
    } catch (error) {
        console.error('Error uploading video chunk:', error);
    }
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
        alert("Vaš pretraživač ne podržava ovu funkcionalnost.");
    }
});

// Očisti interval kada se komponenta uništi
watch(isRecording, (newVal) => {
    if (!newVal) {
        clearInterval(interval);
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
</style>
