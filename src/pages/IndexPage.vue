<template>
  <q-page class="flex flex-center">
    <div class="grid-layout">
      <PlayByPlay class="grid-item-a" />
      <div class="grid-item-b-c">
        <ScreenMain class="grid-item-b" />
        <Highlights class="grid-item-c" />
      </div>
    </div>
  </q-page>
</template>

<script>
import PlayByPlay from 'src/components/PlayByPlay.vue'
import ScreenMain from 'src/components/ScreenMain.vue'
import Highlights from 'src/components/HighlightsBox.vue'

export default {
  components: {
    PlayByPlay,
    ScreenMain,
    Highlights
  }
}
</script>

<style scoped>
main {
  background: #1D1920;
}

.grid-layout {
  display: grid;
  grid-template-rows: 1fr 2fr;
  /* Prvi red manji, drugi veći */
  grid-template-columns: 1fr 2fr;
  /* Druga kolona je šira */
  height: 100vh;
  width: 100%;
  background: #1D1920;
}

.grid-item-a {
  grid-row: 1 / 3;
  /* Ova komponenta zauzima celu prvu kolonu */
  grid-column: 1;
}

.grid-item-b-c {
  grid-row: 1 / 3;
  /* Ova div se prostire kroz dva reda */
  grid-column: 2;
  /* Druga kolona */
  display: grid;
  grid-template-rows: 2fr 1fr;
  /* Prva komponenta veća od druge */
}

.grid-item-b {
  grid-row: 1;
  /* Prva komponenta u drugom redu */
}

.grid-item-c {
  grid-row: 2;
  /* Druga komponenta u drugom redu */
}
</style>
