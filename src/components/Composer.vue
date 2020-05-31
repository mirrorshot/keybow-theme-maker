<template>
  <div class="frameComposer">
    <Keybow :frame="currentFrame" composing @apply-color="applyColor" />
    <div class="frameControls">
      <button @click="firstFrame">{{firstFrameSymbol}}</button>
      <button @click="prevFrame">{{prevFrameSymbol}}</button>
      <button @click="addFrameBefore">{{addFrameBeforeSymbol}}</button>
      <button @click="removeFrame" class="delete">{{currentFrameNumber + 1}}</button>
      <button @click="addFrameAfter">{{addFrameAfterSymbol}}</button>
      <button @click="nextFrame">{{nextFrameSymbol}}</button>
      <button @click="lastFrame">{{lastFrameSymbol}}</button>
    </div>
    <color-picker
      theme="light"
      :color="color"
      :colors-default="colorPaletteFormatted"
      @changeColor="usingColor"
    />
  </div>
</template>

<script>
import Keybow from "./Keybow.vue";
import colorPicker from "@caohenghu/vue-colorpicker";

export default {
  name: "FrameComposer",
  components: {
    Keybow,
    colorPicker
  },
  props: {
    currentFrame: {
      type: Array,
      required: true
    },
    currentFrameNumber: {
      type: Number,
      required: true
    },
    colorPalette: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      firstFrameSymbol: "<<",
      lastFrameSymbol: ">>",
      prevFrameSymbol: "<",
      nextFrameSymbol: ">",
      addFrameBeforeSymbol: "<+",
      addFrameAfterSymbol: ">+",
      currentColor: {
        red: 255,
        green: 255,
        blue: 255
      }
    };
  },
  methods: {
    firstFrame() {
      this.$emit("to-first-frame");
    },
    lastFrame() {
      this.$emit("to-last-frame");
    },
    prevFrame() {
      this.$emit("to-prev-frame");
    },
    nextFrame() {
      this.$emit("to-next-frame");
    },
    addFrameBefore() {
      this.$emit("add-frame-before");
    },
    addFrameAfter() {
      this.$emit("add-frame-after");
    },
    removeFrame() {
      this.$emit("delete-frame");
    },
    usingColor(color) {
      this.currentColor = {
        red: color.rgba.r,
        green: color.rgba.g,
        blue: color.rgba.b
      };
    },
    applyColor(spot) {
      this.$emit("apply-color", this.currentColor, spot);
    }
  },
  computed: {
    color() {
      return (
        "rgb(" +
        this.currentColor.red +
        "," +
        this.currentColor.green +
        "," +
        this.currentColor.blue +
        ")"
      );
    },
    colorPaletteFormatted() {
      const colors = this.colorPalette.map(color => {
        return (
          "rgba(" + color.red + ", " + color.green + ", " + color.blue + ", 1)"
        );
      });
      return colors;
    }
  }
};
</script>

<style scoped>
.frameComposer {
  width: 218px;
}
.frameLabel {
  padding: 1mm;
}
.frameControls {
  padding: 2mm;
}
.delete {
  color: red;
}
</style>