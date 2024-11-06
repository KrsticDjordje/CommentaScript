<template>
  <q-layout container style="height: 100vh" class="shadow-2 border-none">
    <!-- Levi meni -->
    <q-drawer v-model="drawer" show-if-above :mini="miniState" @mouseenter="miniState = false"
      @mouseleave="miniState = true" mini-to-overlay :width="200" :breakpoint="500" bordered
      :class="$q.dark.isActive ? 'bg-grey-9' : 'bg-grey-3'">
      <q-scroll-area class="fit" :horizontal-thumb-style="{ opacity: 0 }">
        <div class="logo-container">
          <img src="../assets/Logo Verbum - znak.png" alt="Logo" class="logo" />
        </div>
        <q-list padding>
          <q-item clickable v-ripple>
            <q-item-section avatar>
              <q-icon name="stars" class="icon-white" />
            </q-item-section>
            <q-item-section class="text-menu">
              Highlights
            </q-item-section>
          </q-item>

          <q-item active clickable v-ripple class="active-item">
            <q-item-section avatar>
              <q-icon name="live_tv" class="icon-active" />
            </q-item-section>
            <q-item-section class="text-menu">
              Live text
            </q-item-section>
          </q-item>

          <q-item clickable v-ripple>
            <q-item-section avatar>
              <q-icon name="summarize" class="icon-white" />
            </q-item-section>
            <q-item-section class="text-menu">
              Sumarize
            </q-item-section>
          </q-item>

          <q-separator />

          <q-item clickable v-ripple>
            <q-item-section avatar>
              <q-icon name="audiotrack" class="icon-white" />
            </q-item-section>
            <q-item-section class="text-menu">
              Audio
            </q-item-section>
          </q-item>
        </q-list>
      </q-scroll-area>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { ref } from 'vue'

const drawer = ref(false)
const miniState = ref(true)
</script>

<style scoped>
.q-scrollarea.fit {
  background: #352B3D;
}

.icon-white,
.text-menu {
  color: white;
}

.icon-active {
  color: #26A69A;
}

.active-item {
  background-color: rgba(0, 128, 0, 0.1);
}

.logo-container {
  display: flex;
  justify-content: center;
  padding: 10px 0;
}

.logo {
  width: 35px;
}
</style>
