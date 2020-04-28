<template>
  <div class="custom-audio" ref="localAudio">
    <!-- <audio :id="id" controls :src="src"> </audio> -->
    <div class="icon" @click="togglePlay">
      <i class="fa" :class="icon"></i>
    </div>
    <div class="audio-body">
      <h5 class="subtitle">{{ subtitle }}</h5>
      <h3 class="title"> {{ title }} </h3>
      <div class="duration">
        <span>{{ currentTime }} / {{ times.durationMins }}:{{ times.durationSecs }}</span>
      </div>
      <div class="low-controls">
       <div class="progress" ref="Progress" @click="setCurrentTime">
        <div class="progress__filled" ref="ProgressFilled" :title="currentTime"></div>
       </div>

        <div class="volume-controls">
          <div class="volume__icon" @click="toggleMute">
            <i :class="iconMuted"></i>
          </div>
          <div class="volume">
            <input type="range" min="0" step="0.1" max="1" @change="setVolume">
          </div>
        </div>
      </div>
    </div>

    <!-- <div class="volume">
      <input type="range" max="10" min="0" step="1">
    </div> -->
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
    isPlaying: false,
    isMuted: false
  }),
  created() {
    this.id = this.uuid();
  },
  mounted() {
    const component = this.$refs.localAudio
    this.elAudio = new Audio(this.src);
    this.loaded = true;
    this.elAudio.addEventListener('timeupdate', this.updateProgress)
    this.elAudio.oncanplay = () => {
      this.getDuration();
    }
  },
  computed: {
    icon() {
      return this.isPlaying ? 'fa-pause' : 'fa-play'
    },

    iconMuted() {
      return this.isMuted ? 'fa fa-volume-off' : 'fa fa-volume-up';
    },

    currentTime() {
      return `${this.times.currentMins}:${this.times.currentSecs}` 
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

    toggleMute() {
      this.elAudio.muted = !this.isMuted;  
      this.isMuted = this.elAudio.muted
    },

    play() {
      this.elAudio.play()
    },

    pause() {
      this.elAudio.pause()
    },

    stop() {
      this.isPlaying = false;
      this.elAudio.pause()
      this.elAudio.currentTime = 0
    },

    updateProgress() {
      const currentProgress = this.$refs.ProgressFilled
      const width = this.elAudio.currentTime / this.elAudio.duration * 100
      currentProgress.style.flexBasis = `${width}%`
      if (width == 100) {
        this.stop()
      }
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
          this.times[key] = value < 10 ? `0${+value}` : value; 
        })
    },

    setVolume(e) {
      this.elAudio.volume = e.target.value;
    },

    setCurrentTime(e) {
      if (this.$refs.ProgressFilled) {
        const currentTime = (e.offsetX / this.$refs.Progress.offsetWidth ) * this.elAudio.duration
          this.elAudio.currentTime = currentTime;
      }
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
  width: 80px;
  height: 80px;
  min-width: 80px;
  display: flex;
  font-size: 30px;
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
  margin-bottom: 2px;
}
.progress:hover {
  height:10px;
}

.low-controls {
  display: flex;
  align-items: center;
  /* height: 30px; */
  overflow: hidden;
}

.volume-controls {
  display: flex;
  width: fit-content;
  cursor: pointer;
  transition: all ease .3s;
  display: flex;
  align-items: center;
  border-radius: 5px;
  height: 30px;
  border: 2px solid transparent;
  padding: 2px;
  border-radius: 10px;
  margin-left: 5px;
}
.volume {
  width: 0;
  transition: all ease .50s;
}

.volume-controls:hover {
    overflow: hidden;
    background: #ccc;
    border: 2px solid #ddd;
    border-radius: 10px;
    margin-left: 5px;
}

.volume-controls:hover .volume {
  width: 200px;
}

.volume__icon {
  padding-top: 1px;
  margin: 0 10px;
}

.progress {
  flex:10;
  position: relative;
  display:flex;
  flex-basis:100%;
  height:5px;
  transition:height 0.3s;
  background: #aaa;
  cursor: pointer
}



.progress__filled {
  width:50%;
  background:#666;
  flex:0;
  flex-basis:0%;
  position: relative;
}

.progress:hover .progress__filled::after {
  content: '';
  width: 10px;
  height: 100%;
  display: block;
  position: absolute;
  background: black;
  right: 0;
}

.duration {
  width: 100%;
  text-align: right;
}

input[type="range"]
{
     cursor: pointer;
     -webkit-appearance: none;
     background-color: #e6e6e6;
}
input[type="range"]:active, input[type="range"]:focus
{
  outline: none;
}

input[type="range"]::-webkit-slider-thumb
{
     -webkit-appearance: none;
     width: 10px;
     height: 10px;
     background: #555;
}
</style>
