<template>
    <div class="swipe-container highlights-box">
        <div v-for="(item, index) in items" :key="index" class="swipe-item" @touchstart="startSwipe"
            @touchend="endSwipe">
            <div class="image-container">
                <img :src="item.image" alt="Swipe Image" class="swipe-image" />
                <div class="overlay">
                    <q-btn icon="play_arrow" color="secondary" class="action-button play-button"
                        @click.stop="playVideo(item)" round dense />
                    <q-btn icon="cloud_download" color="primary" class="action-button download-button"
                        @click.stop="downloadVideo(item)" round dense />
                </div>
            </div>
            <p class="swipe-text">{{ item.text }}</p>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';

const items = ref([
    { image: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQK1QGIDyW2WNcI9o9ON9dw0nqBRhf9pOI4Lg&s', text: "12' - GOOOOL! Tim A vodi 1:0! 🎉 Napadač A se oslobodio čuvara i šalje loptu u mrežu!" },
    { image: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT3ziicpknGEuCOWChsFMZsft78xQFeX98322DEhAkMwHBJon3PN2VJVJ-UJB42_6lenDU&usqp=CAU', text: "23' - Žuti karton za igrača B! Snažan start u sredini terena." },
    { image: 'https://akcdn.detik.net.id/community/media/visual/2024/10/27/real-madrid-vs-barcelona-5_169.jpeg?w=600&q=90', text: "45'+2 - Kraj prvog poluvremena! Tim A vodi 1:0. Tim B ima još šansu da se vrati!" },
    { image: 'https://estaticos-cdn.prensaiberica.es/clip/b9ce542b-d599-4c75-baa5-240320000629_alta-libre-aspect-ratio_default_0.jpg', text: "60’ - GOOOOL! Tim A ponovo vodi! 2:1! 🎊 Kontra napad i precizan udarac!" },
    { image: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQrCd7O1p32nkkLFUWaBOPRpJ86bHtOpxvrOcLNllZQ3X7g5tbeaCs-5BiB0OjAs94YOoA&usqp=CAU', text: "75' - Crveni karton! Igrač C je isključen nakon prekršaja." },
    { image: 'https://cdn.vox-cdn.com/thumbor/s0tof_C9Ylji9zdriuwmeAIB7fE=/0x0:4282x2855/1200x800/filters:focal(2173x112:2857x796)/cdn.vox-cdn.com/uploads/chorus_image/image/73680215/2180481010.0.jpg', text: "90'+4 - Kraj meča! Tim A odnosi pobedu rezultatom 2:1." },
]);

let startX = ref(0);

const startSwipe = (event) => {
    startX.value = event.touches[0].clientX;
};

const endSwipe = (event) => {
    const endX = event.changedTouches[0].clientX;
    const diff = startX.value - endX;
    if (diff > 50) {
        console.log('Swiped left');
        // Logika za swipe levo
    } else if (diff < -50) {
        console.log('Swiped right');
        // Logika za swipe desno
    }
};

// Dummy functions for button actions
const playVideo = (item) => {
    console.log('Playing video:', item);
};

const downloadVideo = (item) => {
    console.log('Downloading video:', item);
};
</script>

<style scoped>
.highlights-box {
    background: #1D1920;
}

.swipe-container {
    display: flex;
    overflow-x: scroll;
    scroll-snap-type: x mandatory;
    border-top: 1px solid #757575;
}

.swipe-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    min-width: 200px;
    margin: 10px;
    scroll-snap-align: center;
    align-self: center;
}

.image-container {
    position: relative;
    /* Za overlay */
}

.swipe-image {
    width: 200px;
    height: 100px;
    object-fit: cover;
    border-radius: 4px;
}

.swipe-text {
    margin-top: 5px;
    text-align: center;
    color: white;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
}

/* Overlay za dugmad */
.overlay {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    opacity: 0;
    transition: opacity 0.3s;
}

.image-container:hover .overlay {
    opacity: 1;
    /* Prikazivanje overlay-a na hover */
}

.action-button {
    margin: 0 5px;
}

.play-button {
    background-color: transparent;
    /* Prozirna pozadina */
    color: #4CAF50;
    /* Zeleni */
}

.download-button {
    background-color: transparent;
    /* Prozirna pozadina */
    color: #2196F3;
    /* Plavi */
}

/* width */
::-webkit-scrollbar {
    width: 5px;
    height: 10px;
}

/* Track */
::-webkit-scrollbar-track {
    background: #1D1920;
}

/* Handle */
::-webkit-scrollbar-thumb {
    background: #322d36;
    border-radius: 5px;
    overflow: hidden;
    cursor: grab;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
    background: #322d36;
}
</style>