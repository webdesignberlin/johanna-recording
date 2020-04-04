<template>
  <div class="record">
    <button
      @click="record"
      class="record-btn"
      :class="{ active: recording }">
      <span class="record-btn__icon-part-1">
        <span class="record-btn__icon-part-2"></span>
      </span>
    </button>
    <audio
      class="play"
      :class="{ active: playing }"
      :src="recordedAudio.src"
      :controls="true"
      :autoplay="true"
      @click="play"
      ref="player"
    ></audio>
  </div>
</template>

<script>
import AudioRecorder from 'audio-recorder-polyfill';

export default {
  name: 'Record',
  props: {
    msg: String,
  },
  data() {
    return {
      rec: null,
      audioChunks: [],
      recordedAudio: {
        src: null,
      },
      recording: false,
      playing: false,
    };
  },
  mounted() {
    this.init();
    this.$refs.player.addEventListener('play', () => { this.playing = true; });
    this.$refs.player.addEventListener('stop', () => { this.playing = false; });
    this.$refs.player.addEventListener('pause', () => { this.playing = false; });
  },
  methods: {
    init() {
      if (!window.MediaRecorder) {
        window.MediaRecorder = AudioRecorder;
      }
    },
    record() {
      if (this.recording) {
        this.rec.stop();
        this.rec.stream.getTracks().forEach((i) => i.stop());
      } else {
        navigator.mediaDevices.getUserMedia({ audio: true }).then((stream) => {
          this.rec = new MediaRecorder(stream);

          // Set record to <audio> when recording will be finished
          this.rec.addEventListener('dataavailable', (ev) => {
            console.log('src', ev);
            this.recordedAudio.src = URL.createObjectURL(ev.data);
          });

          // Start recording
          this.rec.start();
        });
      }
      this.recording = !this.recording;
    },
    play() {
      this.$refs.player.play();
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.record {
  --btn-size: 50vmin;

  display: grid;
  grid-gap: 1rem;
  grid-template-columns: 1fr;
  grid-template-rows: 1fr 1fr;
  height: 100vh;
  justify-items: center;
  align-items: center;

  @media (min-width: 600px) {
    --btn-size: 40vmin;

    grid-template-columns: 1fr 1fr;
    grid-template-rows: 1fr;
  }
}

.play {
  position: relative;
  background-color: #4DB6AC;
  border-radius: 50%;
  width: var(--btn-size);
  height: var(--btn-size);
  display: inline-block;
  cursor: pointer;
  box-shadow: 0 0 0 0 rgba(232, 76, 61, 0.7);

  &::-webkit-media-controls,
  &::-webkit-media-controls-panel,
  &::-webkit-media-controls-enclosure {
    display: none !important;
    width: 1px;
    height: 1px;
    opacity: 0;
  }

  &.active {
    background-color: aquamarine;
    animation: pulse 2s infinite;
  }
}

.record-btn {
  position: relative;
  background-color: lightcoral;
  border-radius: 50%;
  width: var(--btn-size);
  height: var(--btn-size);
  display: inline-block;
  cursor: pointer;
  box-shadow: 0 0 0 0 rgba(232, 76, 61, 0.7);
  &:hover {
    background-color: coral;
  }
  &.active {
    background-color: coral;
    animation: pulse 2s infinite;
  }
  &__icon-part-1 {
    &:before,
    &:after {
      content: '';
      position: absolute;
      background-color: #fff;
    }
    &:after {
      top: 30%;
      left: 43%;
      height: 15%;
      width: 14%;
      border-top-left-radius: 50%;
      border-top-right-radius: 50%;
    }
    &:before {
      top: 40%;
      left: 43%;
      height: 15%;
      width: 14%;
      border-bottom-left-radius: 50%;
      border-bottom-right-radius: 50%;
    }
  }
  &__icon-part-2 {
    position: absolute;
    top: 50%;
    left: 36%;
    height: 24%;
    width: 28%;
    overflow: hidden;
    &:before,
    &:after {
      content: '';
      position: absolute;
      background-color: #fff;
    }
    &:before {
      bottom: 50%;
      width: 100%;
      height: 100%;
      box-sizing: border-box;
      border-radius: 50%;
      border: 0.125em solid #fff;
      background: none;
      left: 0;
    }
    &:after {
      top: 50%;
      left: 40%;
      width: 20%;
      height: 25%;
    }
  }
}
@keyframes pulse {
  0% {
    transform: scale(0.95);
    box-shadow: 0 0 0 0 rgba(0, 0, 0, 0.7);
  }

  70% {
    transform: scale(1);
    box-shadow: 0 0 0 10px rgba(0, 0, 0, 0);
  }

  100% {
    transform: scale(0.95);
    box-shadow: 0 0 0 0 rgba(0, 0, 0, 0);
  }
}
</style>
