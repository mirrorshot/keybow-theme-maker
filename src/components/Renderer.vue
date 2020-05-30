<template>
  <div
    class="renderer"
    :style="{width: rendererWidth, height: rendererHeight, borderWidth: borderWidthMeasure, padding: paddingMeasure}"
  >
    <theme-canvas ref="renderedCanvas"
    ></theme-canvas>
  </div>
</template>

<script>
import ThemeCanvas from "./ThemeCanvas.vue";

export default {
  name: "renderer",
  components: {
    ThemeCanvas
  },
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
      this.$refs.renderedCanvas.render(scale, frames);
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
