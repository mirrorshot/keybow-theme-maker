<template>
  <div
    class="renderer"
    :style="{width: rendererWidth, height: rendererHeight, borderWidth: borderWidthMeasure, padding: paddingMeasure}"
  >
    <canvas ref="renderedCanvas"></canvas>
  </div>
</template>

<script>
export default {
  name: "renderer",
  props: {
    borderWidth: {
      type: Number,
      required: true
    },
    padding: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      frames: [],
      scale: 10
    };
  },
//  mounted() {
//    this.render(this.scale, this.frames);
//  },
  methods: {
    render(scale, frames) {
      this.scale = scale;
      this.frames = frames;
      let canvas = this.$refs.renderedCanvas;
      let context = canvas.getContext("2d");
      canvas.height = 0;
      canvas.width = 0;
      canvas.height = this.computeRendererHeight();
      canvas.width = this.computeRendererWidth();
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
    },
    computeRendererWidth() {
      return 12 * this.scale;
    },
    computeRendererHeight() {
      return this.frames.length * this.scale;
    }
  },
  computed: {
    rendererWidth() {
      let width = (
        this.computeRendererWidth() +
        2 * (this.padding + this.borderWidth)
      );
      width = this.computeRendererWidth();
      width = width  + "px";
      return width;
    },
    rendererHeight() {
      let height = (
        this.computeRendererHeight() +
        2 * (this.padding + this.borderWidth)
      );
      height = this.computeRendererHeight();
      height = height  + "px";
      return height;
    },
    borderWidthMeasure() {
      return this.borderWidth + "px";
    },
    paddingMeasure() {
      return this.padding + "px";
    }
  }
};
</script>

<style scoped>
.renderer {
  align-items: stretch;
  display: inline-block;
  border: solid black;
  background: grey;
}
</style>
