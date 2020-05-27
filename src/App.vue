<template>
  <div id="app">
    <div id="renderer" :style="{width: rendererBoxWidth, height: rendererBoxHeight}">
      <div>
        <label for="renderScale">Scale:</label>
        <input 
        :value="scale"
        @change="updateScale" 
        type="number"
        step="1"
        id="renderScale" 
        ref="renderScale" 
        min="1"
        max="500"
        size="3" />
      </div>
      <renderer
        :frames="frames"
        :borderWidth="borderWidthMeasure"
        :padding="paddingMeasure"
        :rendererHeight="rendererHeight"
        :rendererWidth="rendererWidth"
        v-bind:scale="scale"
        ref="renderer"
      ></renderer>
    </div>
    <div id="composer">
      <composer
        :currentFrame="frames[currentFrame]"
        :currentFrameNumber="currentFrame"
        @apply-color="applyColor"
        @to-first-frame="toFirstFrame"
        @to-last-frame="toLastFrame"
        @to-prev-frame="toPrevFrame"
        @to-next-frame="toNextFrame"
        @add-frame-before="addFrameBefore"
        @add-frame-after="addFrameAfter"
        @delete-frame="deleteFrame"
      />
    </div>
  </div>
</template>

<script>
function makeColor(red, green, blue) {
  return {
    red: red,
    green: green,
    blue: blue
  };
}
function duplicateFrame(frame) {
  let newFrame = [];
  for (let color in frame) {
    newFrame.push(makeColor(color.red, color.green, color.blue));
  }
  return newFrame;
}
function makeFrame() {
  return [
    makeColor(255, 0, 0),
    makeColor(0, 255, 0),
    makeColor(0, 0, 255),
    makeColor(255, 255, 255),
    makeColor(0, 255, 255),
    makeColor(255, 255, 0),
    makeColor(255, 0, 255),
    makeColor(0, 0, 0),
    makeColor(0, 0, 255),
    makeColor(0, 255, 0),
    makeColor(255, 0, 0),
    makeColor(255, 255, 255)
  ];
}
function makeFrames(n) {
  let frames = [];
  for (let i = 0; i < n; i++) frames.push(makeFrame());
  return frames;
}
import Composer from "./components/Composer.vue";
import Renderer from "./components/Renderer.vue";

export default {
  name: "App",
  components: {
    Composer,
    Renderer
  },
  data() {
    return {
      scale: 15,
      minRendererWidth: 100,
      borderWidth: 2,
      padding: 5,
      currentFrame: 0,
      frames: makeFrames(10)
    };
  },
  computed: {
    composingFrame() {
      return this.frames[this.currentFrame];
    },
    rendererWidth() {
      return 12 * this.scale + "px";
    },
    rendererHeight() {
      return this.frames.length * this.scale + "px";
    },
    rendererBoxWidth() {
      let width = Math.max(12 * this.scale + (this.borderWidth + this.padding) * 2, this.minRendererWidth);
      return width + "px";
    },
    rendererBoxHeight() {
      return (
        this.frames.length * this.scale +
        (this.borderWidth + this.padding) * 2 +
        "px"
      );
    },
    borderWidthMeasure() {
      return this.borderWidth + "px";
    },
    paddingMeasure() {
      return this.padding + "px";
    }
  },
  methods: {
    updateScale() {
      this.scale = parseInt(this.$refs.renderScale.value);
    },
    toFirstFrame() {
      this.currentFrame = 0;
    },
    toLastFrame() {
      this.currentFrame = this.frames.length - 1;
    },
    toPrevFrame() {
      if (this.currentFrame > 0) this.currentFrame = this.currentFrame - 1;
    },
    toNextFrame() {
      if (this.currentFrame < this.frames.length - 1)
        this.currentFrame = this.currentFrame + 1;
    },
    addFrameBefore() {
      this.frames.splice(
        this.currentFrame,
        0,
        duplicateFrame(this.frames[this.currentFrame])
      );
    },
    addFrameAfter() {
      this.frames.splice(
        this.currentFrame + 1,
        0,
        duplicateFrame(this.frames[this.currentFrame])
      );
      this.currentFrame = this.currentFrame + 1;
    },
    deleteFrame() {
      if (this.frames.length > 1) {
        console.log("deleting frame " + this.currentFrame + " of " + this.frames.length);
        this.frames.splice(this.currentFrame, 1);
        if(this.currentFrame === this.frames.length)
          this.currentFrame = this.currentFrame - 1;
        console.log("moved to frame " + this.currentFrame + " of " + this.frames.length);
        this.$refs.renderer.render();
      }
    },
    applyColor(color, spot) {
      this.frames[this.currentFrame][spot].red = color.red;
      this.frames[this.currentFrame][spot].green = color.green;
      this.frames[this.currentFrame][spot].blue = color.blue;
      this.$refs.renderer.render();
    }
  }
};
</script>

<style>
html,
body {
  height: 100%;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  display: flex;
}
#composer {
  align-items: stretch;
  margin-left: 5mm;
}
#renderer {
  vertical-align: top;
  align-items: stretch;
}
</style>
