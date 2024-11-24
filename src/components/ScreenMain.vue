<template>
    <div class="screen-recorder-wrap">
        <h6 class="time q-my-md">Barcelona - Real Madrid 24.10.2024.</h6>
        <video class="screen-player" ref="videoRef" autoplay playsinline muted></video>
        <div>
            <h5 class="time q-my-md">{{ formattedTime }}</h5>
            <q-btn v-if="!isRecording" label="Start Recording" color="secondary" @click="startRecording" />
            <q-btn v-if="isRecording" label="Stop Recording" color="negative" @click="stopRecording" />
        </div>
    </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { QBtn } from "quasar";
import axios from "axios";

const mediaStream = ref(null);
const videoRef = ref(null); // Referenca na video element
const isRecording = ref(false);
const elapsedTime = ref(0);
let interval = null;
let recorder = null;

const startRecording = async () => {
    isRecording.value = true;
    elapsedTime.value = 0;

    // Pokretanje snimanja ekrana
    mediaStream.value = await navigator.mediaDevices.getDisplayMedia({
        video: true,
        audio: true,
    });

    // Poveži video element sa streamom
    if (videoRef.value) {
        videoRef.value.srcObject = mediaStream.value;
    }

    createNewRecorder();

    // Interval za merenje vremena
    interval = setInterval(() => {
        elapsedTime.value++;
    }, 1000);
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

    // Resetuj video element
    if (videoRef.value) {
        videoRef.value.srcObject = null;
    }
};

// Kreiranje novog MediaRecorder-a za svaki segment
const createNewRecorder = () => {
    if (recorder) {
        recorder.ondataavailable = null; // Ukloni prethodni listener
        recorder.stop();
    }

    recorder = new MediaRecorder(mediaStream.value);

    recorder.ondataavailable = (event) => {
        if (event.data.size > 0) {
            processVideoChunk(event.data);
        }
    };

    recorder.start(15000); // Snimanje segmenta svakih 5 sekundi
};

const processVideoChunk = (data) => {
    const blob = new Blob([data], { type: "video/webm" });

    // Prvo sačuvaj video lokalno
    saveVideoLocally(blob);

    // Zatim pošaljite na API
    uploadVideo(blob);

    // Kreiraj novi MediaRecorder za sledeći segment
    if (isRecording.value) {
        createNewRecorder();
    }
};

const saveVideoLocally = (blob) => {
    const videoURL = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = videoURL;
    link.download = `recorded-video-${Date.now()}.mp4`; // Da se sačuva sa jedinstvenim imenom
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link); // Čisti DOM od linka nakon preuzimanja
};

const uploadVideo = async (blob) => {
    const formData = new FormData();
    formData.append("video", blob);
    // formData.append("title", blob);
    // formData.append("start_time", blob);
    // formData.append("sport", blob);
    console.log([...formData.entries()]);

    try {
        const response = await axios.post("https://verbumscript.app/v1/postVideo", formData, {
            headers: {
                "Content-Type": "multipart/form-data",
            },
        });

        const data = await response.data;
        console.log("Message from API:", data);
    } catch (error) {
        console.error("Error uploading video chunk:", error);
    }
};

const formatTime = (seconds) => {
    const hours = String(Math.floor(seconds / 3600)).padStart(2, "0");
    const minutes = String(Math.floor((seconds % 3600) / 60)).padStart(2, "0");
    const secs = String(seconds % 60).padStart(2, "0");
    return `${hours}:${minutes}:${secs}`;
};

const formattedTime = computed(() => formatTime(elapsedTime.value));

onMounted(() => {
    if (!navigator.mediaDevices || !navigator.mediaDevices.getDisplayMedia) {
        alert("Vaš pretraživač ne podržava ovu funkcionalnost.");
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
