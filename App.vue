<template>
<main>
  <h1>Custom Components with Vite + Vue 3!</h1>
  
  <h2>Custom Audio</h2>
  <hi-audio
    v-for="song in playList" 
    :key="song.src" 
    :src="song.src" 
    :title="song.title" 
    :subtitle="song.subtitle" 
    class="audio-item">
  </hi-audio>
</main>
</template>

<script>
import HiAudio from "./components/hi-audio.vue";

export default {
  components: {
    HiAudio
  },
  data: () => ({ 
    playList: [
      {
        src: './music/sexcandy.mp3',
        title: 'Sex and Candy',
        subtitle: 'Maroon V'
      },
      {
        src: './music/maps.mp3',
        title: 'Maps',
        subtitle: 'Maroon V'
      },
      {
        src: ' http://upload.wikimedia.org/wikipedia/commons/f/f5/Good_Morning_to_All.ogg',
        title: 'Wiki',
        subtitle: 'Pedia'
      }
    ]
  })
}
</script>

<style scoped>
main {
  padding: 50px;
}
h1 {
  color: #4fc08d;
}

h1, p {
  font-family: Arial, Helvetica, sans-serif;
}

.custom-audio audio {
  display: none;
}

.audio-item {
  margin: 15px 0;
}
</style>
