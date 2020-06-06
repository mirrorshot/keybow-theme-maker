<template>
  <div class="animated">
    <div>
      <label for="animationSpeed">Frame time(ms):</label>
      <input
        value="100"
        @change="updateSpeed"
        type="number"
        step="5"
        id="animationSpeed"
        ref="animationSpeed"
        min="10"
        max="100000"
        size="6"
      />
    </div>
    <keybow :frame="frames[currentFrame]"></keybow>
    <div>
      <button @click="stopAnimation" :disabled="!animationRunning">Pause</button>
      <button @click="continueAnimation" :disabled="animationRunning">Continue</button>
      <button @click="restartAnimation">Restart</button>
    </div>
    <div>
      <button @click="animateBack" :disabled="animationRunning">Previous</button>
      <button @click="animate" :disabled="animationRunning">Next</button>
    </div>
  </div>
</template>

<script>
import Keybow from "./Keybow.vue";

export default {
  name: "Animated",
  components: {
    Keybow
  },
  props: {
    frames: {
      type: Array,
      required: true
    },
    speed: {
      type: Number,
      default: 25
    }
  },
  data() {
    return {
      currentFrame: 0,
      animationInterval: null
    };
  },
  mounted() {
    this.$refs.animationSpeed.value = this.speed;
    this.animationInterval = setInterval(() => {
      this.currentFrame++;
      if (this.currentFrame == this.frames.length) this.currentFrame = 0;
    }, this.speed);
  },
  methods: {
    animate: function() {
      this.currentFrame++;
      if (this.currentFrame >= this.frames.length) this.currentFrame = 0;
    },
    animateBack: function() {
      this.currentFrame--;
      if (this.currentFrame <= 0) this.currentFrame = this.frames.length - 1;
    },
    stopAnimation() {
      clearInterval(this.animationInterval);
      this.animationInterval = null;
    },
    stopAnimationAt0() {
      clearInterval(this.animationInterval);
      this.animationInterval = null;
      this.currentFrame = 0;
    },
    restartAnimation(speed = this.speed) {
      if (this.animationInterval === null) {
        this.currentFrame = 0;
        this.animationInterval = setInterval(() => {
          this.currentFrame++;
          if (this.currentFrame == this.frames.length) this.currentFrame = 0;
        }, speed);
      }
    },
    continueAnimation(speed = this.speed) {
      if (this.animationInterval === null) {
        this.animationInterval = setInterval(() => {
          this.currentFrame++;
          if (this.currentFrame == this.frames.length) this.currentFrame = 0;
        }, speed);
      }
    },
    updateSpeed(event) {
      const speed = parseInt(event.target.value);
      this.stopAnimation();
      this.speed = speed;
      this.continueAnimation();
    }
  },
  computed: {
    animationRunning() {
      return this.animationInterval !== null;
    }
  }
};
</script>

<style scoped>
.animated {
  width: 60mm;
}
</style>