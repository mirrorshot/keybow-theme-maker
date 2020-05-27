<template>
  <div class="animated">
    <keybow :frame="frames[currentFrame]"></keybow>
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
      required: false,
      default: 100
    }
  },
  data() {
    return {
      currentFrame: 0
    };
  },
  mounted() {
    console.log("Start animation interval with speed: " + this.speed);
    this.intervalid1 = setInterval(() => {
      this.currentFrame++;
      if (this.currentFrame == this.frames.length) this.currentFrame = 0;
    }, this.speed);
  },
  computed: {}
};
</script>
