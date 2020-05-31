<template>
  <canvas ref="theme"></canvas>
</template>

<script>
export default {
  name: "ThemeCanvas",
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
      this.canvas.height = 0;
      this.canvas.width = 0;
      this.canvas.height = this.computeRendererHeight();
      this.canvas.width = this.computeRendererWidth();
      for (let row = 0; row < frames.length; row++) {
        const y0 = row * scale;
        for (let color = 0; color < 12; color++) {
          const x0 = color * scale;
          const fillColor =
            "#" +
            frames[row][color].red.toString(16).padStart(2, "0") +
            frames[row][color].green.toString(16).padStart(2, "0") +
            frames[row][color].blue.toString(16).padStart(2, "0");
          context.fillStyle = fillColor;
          context.fillRect(x0, y0, scale, scale);
        }
      }
      context.save();
    },
    computeRendererWidth() {
      return 12 * this.scale;
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