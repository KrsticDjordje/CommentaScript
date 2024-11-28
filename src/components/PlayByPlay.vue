<template>
    <div class="play-by-play">
        <!-- <h6 class="text-white">Play by Play</h6> -->
        <q-btn v-if="filteredEvents.length > 0" label="+ New Match" color="secondary" @click="deleteAll"
            class="q-my-md" />
        <p v-else class="text-white">You are ready for a new match, fill in the details about the match and start
            recording!</p>
        <ul>
            <li v-for="(event, index) in filteredEvents" :key="index">
                <div class="event-content">
                    <span class="event-time">{{ event.timestamp }}'</span> -
                    <span>{{ event.message }}</span>
                </div>
            </li>
        </ul>
    </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue';

const events = ref([]);
let fetchInterval = null;


const loadFromLocalStorage = () => {
    const storedData = localStorage.getItem('playByPlayData');
    if (storedData) {
        events.value = JSON.parse(storedData);
    } else {
        events.value = [];
    }
};


const deleteAll = () => {
    localStorage.removeItem('playByPlayData');
    events.value = [];
};


const startFetchingData = () => {
    fetchInterval = setInterval(() => {
        loadFromLocalStorage();
    }, 1000);
};


const stopFetchingData = () => {
    if (fetchInterval) {
        clearInterval(fetchInterval);
    }
};


onMounted(() => {
    loadFromLocalStorage();
    startFetchingData();
});


onUnmounted(() => {
    stopFetchingData();
});

const filteredEvents = computed(() =>
    events.value.filter((event) => event.message !== "BORING")
);
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

.play-by-play::-webkit-scrollbar {
    display: none;
}
</style>