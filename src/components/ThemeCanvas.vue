<template>
  <canvas ref="theme"></canvas>
</template>

<script>
export default {
  name: "ThemeCanvas",
  props: {
    frameSize: {
      type: Number,
      default: 12
    }
  },
  data() {
    return {
      frames: [],
      scale: 1,
      canvas: null
    };
  },
  mounted() {
    this.canvas = this.$refs.theme;
  },
  methods: {
    render(scale, frames) {
      this.scale = scale;
      this.frames = frames;
      let context = this.canvas.getContext("2d");
      this.canvas.height = this.computeRendererHeight();
      this.canvas.width = this.computeRendererWidth();
      for (let row = 0; row < frames.length; row++) {
        const y0 = row * scale;
        for (let color = 0; color < this.frameSize; color++) {
          const x0 = color * scale;
          context.fillStyle = this.padColor(frames[row][color]);
          context.fillRect(x0, y0, scale, scale);
        }
      }
      context.save();
    },
    padColor(color) {
      return (
        "#" +
        this.padColorComponent(color.red) +
        this.padColorComponent(color.green) +
        this.padColorComponent(color.blue)
      );
    },
    padColorComponent(component) {
      return parseInt(component).toString(16).padStart(2, "0");
    },
    computeRendererWidth() {
      return this.frameSize * this.scale;
    },
    computeRendererHeight() {
      return this.frames.length * this.scale;
    },
    extractData(imageType) {
      return this.canvas.toDataURL(imageType);
    },
    saveImage(callback, imageType) {
      this.canvas.toBlob(callback, imageType);
    }
  }
};
</script>