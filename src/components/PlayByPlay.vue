<template>
    <div class="play-by-play">
        <ul>
            <li v-for="(event, index) in events" :key="index" @click="startEditing(index)">
                <div class="event-content">
                    <span class="event-time">{{ event.time }}'</span> -
                    <span v-if="editingIndex === index">
                        <input v-model="event.description" @blur="finishEditing" @keyup.enter="finishEditing"
                            class="edit-input" />
                    </span>
                    <span v-else>
                        {{ event.description }}
                    </span>
                    <!-- Prikaz videa ako postoji -->
                    <div v-if="event.video" class="video-container">
                        <video controls :src="event.video" class="event-video"></video>
                    </div>
                </div>
            </li>
        </ul>
    </div>
</template>

<script setup>
import { ref } from 'vue'

// Reaktivna lista događaja sa minutazom i video linkovima
const events = ref([
    { time: 5, description: '⚽ Igrač 1 je postigao spektakularan gol! Oduševio je sve prisutne!', video: 'https://videos.pexels.com/video-files/15448993/15448993-hd_1920_1080_60fps.mp4' },
    { time: 12, description: '🚨 Igrač 2 je izveo ključni prekršaj! Sudija pokazuje žuti karton.' },
    { time: 25, description: '🤝 Igrač 3 je napravio fantastičnu asistenciju! Pronašao je svog suigrača savršenim pasom.', video: 'https://videos.pexels.com/video-files/15448993/15448993-hd_1920_1080_60fps.mp4' },
    { time: 30, description: '🎉 Tim je proslavio pobedu! Navijači su u transu!' },
    { time: 45, description: '🔥 Gledatelji su oduševljeni! Atmosfera na stadionu je neverovatna!' },
    { time: 60, description: '🌟 Igrač 4 dolazi na teren i odmah pravi razliku! Publika ga pozdravlja ovacijama.' },
    { time: 70, description: '💪 Igrač 6 je izveo sjajan dribling! Protivnici su nemoćni.' },
    { time: 75, description: '⚡ Nevjerojatna odbrana golmana! Tim ostaje u prednosti, publika aplaudira.' },
    { time: 80, description: '🎯 Igrač 5 je još jednom zapucao prema golu! Ova utakmica je puna uzbuđenja!' },
    { time: 85, description: '🔔 Poslednja petina! Svaki trenutak može promeniti tok utakmice.' },
    { time: 90, description: '⏰ Utakmica se bliži kraju, napetost raste! Svi očajnički čekaju poslednji zvižduk.' },
    { time: 92, description: '🌪️ Dramatičan trenutak! Sudija pokazuje na penal za protivnički tim!' },
    { time: 93, description: '🚀 Poslednji udarac! Da li će se izjednačiti? Navijači zadržavaju dah!' },
    { time: 94, description: '🎊 Utakmica je gotova! Tim je odneo pobedu u dramatičnom završetku!' },
])

// Praćenje indexa opisa koji je u režimu uređivanja
const editingIndex = ref(null)

// Početak uređivanja
const startEditing = (index) => {
    editingIndex.value = index
}

// Završetak uređivanja
const finishEditing = () => {
    editingIndex.value = null
}
</script>

<style scoped>
.play-by-play {
    background: #413946;
    padding: 20px;
    overflow-y: auto;
}

ul {
    list-style-type: none;
    padding: 0;
}

li {
    margin: 5px 0;
    background: #352B3D;
    padding: 10px;
    border-radius: 4px;
    transition: background-color 0.3s;
    color: white;
    cursor: pointer;
}

li:hover {
    background-color: #3d3445;
}

/* Stil za input polje u režimu uređivanja */
.edit-input {
    background: transparent;
    border: none;
    border-bottom: 1px solid white;
    color: white;
    width: 100%;
    outline: none;
}

.play-by-play::-webkit-scrollbar {
    display: none;
}

/* Stilovi za video */
.video-container {
    margin-top: 10px;
    justify-self: center;
}

.event-video {
    width: auto;
    max-height: 150px;
    border-radius: 4px;

}
</style>
