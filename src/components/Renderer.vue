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
    frames: {
      type: Array,
      required: true
    },
    scale: {
      type: Number,
      default: 10
    },
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
      lastFrameCount: this.frames.length
    };
  },
  mounted() {
    this.render();
  },
  methods: {
    render() {
      let canvas = this.$refs.renderedCanvas;
      let context = canvas.getContext("2d");
      canvas.height = 0;
      canvas.height = this.computeRendererHeight();
      canvas.width = this.computeRendererWidth();
      for (let row = 0; row < this.frames.length; row++) {
        const y0 = row * this.scale;
        for (let color = 0; color < 12; color++) {
          const x0 = color * this.scale;
          const fillColor =
            "#" +
            this.frames[row][color].red.toString(16).padStart(2, "0") +
            this.frames[row][color].green.toString(16).padStart(2, "0") +
            this.frames[row][color].blue.toString(16).padStart(2, "0");
          console.log("rectangle", x0, y0, fillColor);
          context.fillStyle = fillColor;
          context.fillRect(x0, y0, this.scale, this.scale);
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
      return (
        this.computeRendererWidth() +
        2 * (this.padding + this.borderWidth) +
        "px"
      );
    },
    rendererHeight() {
      return (
        this.computeRendererHeight() +
        2 * (this.padding + this.borderWidth) +
        "px"
      );
    },
    borderWidthMeasure() {
      return this.borderWidth + "px";
    },
    paddingMeasure() {
      return this.padding + "px";
    },
    rendererCanvasWidth() {
      return this.computeRendererWidth() + "px";
    },
    rendererCanvasHeight() {
      return this.computeRendererHeight() + "px";
    }
  }
};
</script>

<style scoped>
.renderer {
  align-items: stretch;
  display: inline-block;
  border: solid black;
}
</style>
