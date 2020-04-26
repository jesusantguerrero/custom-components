<template>
  <div class="custom-audio" ref="localAudio">
    <audio :id="id" controls :src="src" type="audio/mpeg"> </audio>
    <div class="icon" @click="togglePlay">
      <i class="fa" :class="icon"></i>
    </div>
    <div class="audio-body">
      <h5 class="subtitle">{{ subtitle }}</h5>
      <h4 class="title"> {{ title }} </h4>
      <div class="duration">
        <span>{{ times.currentMins}}:{{ times.currentSecs }} / {{ times.durationMins }}:{{ times.durationSecs }}</span>
      </div>
       <div class="progress" ref="progress">
        <div class="progress__filled"></div>
       </div>
    </div>
  </div>
</template>

<script>

export default {
  props: {
    src: {
      type: String,
      default: ''
    },
    title: {
      type: String,
      default: ''
    },
    subtitle: {
      type: String,
      default: ''
    }
  },
  data: () => ({ 
    id: null,
    elAudio: {},
    loaded: false,
    times: {
      currentMins: 0,
      currentSecs: 0,
      durationMins: 0,
      durationSecs: 0
    },
    isPlaying: false 
  }),
  created() {
    this.id = this.uuid();
  },
  mounted() {
    const component = this.$refs.localAudio
    this.elAudio = component.querySelector('audio');
    this.loaded = true;
    this.elAudio.addEventListener('timeupdate', this.updateProgress)
    this.controls = {
      progress: component.querySelector('.progress')
    }
    this.controls.progress.addEventListener('click', this.setCurrentTime)
    this.elAudio.oncanplay = () => {
      this.getDuration();
    }
  },
  computed: {
    icon() {
      return this.isPlaying ? 'fa-pause' : 'fa-play'
    }
  },
  methods: {
    uuid() {
      // from broofas answer
      // https://stackoverflow.com/questions/105034/how-to-create-guid-uuid
      return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
        var r = Math.random()*16|0, v = c == 'x' ? r : (r&0x3|0x8);
        return v.toString(16);
      })
    },
    togglePlay() {
      !this.elAudio.paused ? this.pause() : this.play()
      this.isPlaying = !this.elAudio.paused
    },

    play() {
      this.elAudio.play()
    },

    pause() {
      this.elAudio.pause()
    },

    stop() {
      this.elAudio.stop()
    },

    updateProgress() {
      const currentProgress = this.controls.progress.querySelector('.progress__filled')
      const width = this.elAudio.currentTime / this.elAudio.duration * 100
      currentProgress.style.flexBasis = `${width}%`
      this.getCurrentTime();
    },

    getCurrentTime() {
      this.times.currentMins = Math.floor(this.elAudio.currentTime / 60)
      this.times.currentSecs = Math.floor(this.elAudio.currentTime - this.times.currentMins * 60)
      this.setZeros(['currentMins', 'currentSecs'])
    },

    getDuration() {
      this.times.durationMins = Math.floor(this.elAudio.duration / 60)
      this.times.durationSecs = Math.floor(this.elAudio.duration - this.times.durationMins * 60)
      this.setZeros()
    },

    setZeros(timeKeys) {
        timeKeys = timeKeys || Object.keys(this.times)
        timeKeys.forEach( key => {
          const value = this.times[key];
          this.times[key] = value < 10 ? `0${value}` : value; 
        })
    },

    setCurrentTime(e) {
      const currentTime = (e.offsetX / this.controls.progress.offsetWidth ) * this.elAudio.duration
        this.elAudio.currentTime = parseInt(currentTime);
        console.log(currentTime)  
    }
  }
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

.custom-audio {
  background: #EAEAEA;
  color: #444;
  padding: 15px;
  display: flex;
}

.custom-audio .icon {
  background: silver;
  width: 70px;
  height: 70px;
  min-width: 70px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}

.custom-audio .audio-body {
  width: 100%;
  padding: 0 15px;
}

.audio-body .title, .subtitle {
  margin-top: 0;
}
.audio-body .subtitle {
  margin-bottom: 2px;
}

.audio-body .title {
  margin-bottom: 5px;
}
.progress:hover {
  height:10px;
}

.progress {
  flex:10;
  position: relative;
  display:flex;
  flex-basis:100%;
  height:5px;
  transition:height 0.3s;
  background: #aaa;
  cursor:ew-resize;
}

.progress__filled {
  width:50%;
  background:#666;
  flex:0;
  flex-basis:0%;
}

.duration {
  width: 100%;
  text-align: right;
}
</style>
