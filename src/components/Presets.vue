<template>
  <div>
    <div>
      <span class="title">Random</span>
      <label for="number">Frames:</label>
      <input type="number" name="randomFramesNumber" ref="randomFramesNumber" size="8" value="25" />
      <button @click="generateRandom">Random</button>
      <button @click="generateRandomInPalette">In Palette</button>
    </div>
    <div>
      <span class="title">Presets</span>
      <button
        v-for="mode in defaults"
        @click="restart(mode)"
        :key="mode.label"
        :style="{display: presetsDisposition}"
      >Load {{mode.label}}</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Presets",
  props: {
    defaults: {
      type: Array,
      required: true
    },
    palette: {
      type: Array,
      required: true
    },
    frameSize: {
      type: Number,
      default: 12
    }
  },
  methods: {
    restart(mode) {
      this.$emit("load-frames", mode.frames);
    },
    generateRandom() {
      const frames = [];
      const count = this.$refs.randomFramesNumber.value;
      for (let i = 0; i < count; i++) frames.push(this.generateRandomFrame());
      this.$emit("load-frames", frames);
    },
    generateRandomFrame(palette) {
      let frame = [];
      for (let i = 0; i < this.frameSize; i++)
        frame.push(this.makeRandomColor(palette));
      return frame;
    },
    makeRandomColor(palette) {
      if (palette === undefined)
        return {
          red: Math.floor(Math.random() * 256),
          green: Math.floor(Math.random() * 256),
          blue: Math.floor(Math.random() * 256)
        };
      const color = this.palette[
        Math.floor(Math.random() * this.palette.length)
      ];
      return {
        red: color.red,
        green: color.green,
        blue: color.blue
      };
    },
    generateRandomInPalette() {
      if (this.palette.length === 0) return;
      const frames = [];
      const count = this.$refs.randomFramesNumber.value;
      for (let i = 0; i < count; i++)
        frames.push(this.generateRandomFrame(this.palette));
      this.$emit("load-frames", frames);
    }
  },
  computed: {
    presetsDisposition() {
      if (this.defaults.length > 3) return "block";
      else return "inline-block";
    }
  }
};
</script>

<style scoped>
.title {
  display: block;
  font-weight: bolder;
  font-size: 120%;
}
</style>