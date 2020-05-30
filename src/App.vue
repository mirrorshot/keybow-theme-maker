<template>
  <div id="app">
    <div id="left-module" :style="{width: rendererBoxWidth, height: rendererBoxHeight}">
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
          size="3"
        />
      </div>
      <renderer
        :frames="frames"
        v-bind:borderWidth="rendererBorderWidth"
        v-bind:padding="rendererPadding"
        v-bind:scale="scale"
        ref="renderer"
      ></renderer>
    </div>
    <div id="right-module">
      <SaveAndLoad
        :palette="composerPalette"
        :frames="frames"
        @loading-frames="loadFrames"
        @loading-palette="loadPalette"
      />
      <div id="animated">
        <animated ref="animated" :frames="frames"></animated>
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
function makeRandColor() {
  return {
    red: Math.floor(Math.random() * 256),
    green: Math.floor(Math.random() * 256),
    blue: Math.floor(Math.random() * 256)
  };
}
function duplicateFrame(frame) {
  let newFrame = [];
  for (let color = 0; color<frame.length; color++) {
    newFrame.push(makeColor(frame[color].red, frame[color].green, frame[color].blue));
  }
  return newFrame;
}
function makeRandomFrame() {
  let frames = [];
  for (let i = 0; i < 12; i++) frames.push(makeRandColor());
  return frames;
}
function makeFrames(n) {
  let frames = [];
  for (let i = 0; i < n; i++) frames.push(makeRandomFrame());
  return frames;
}
import Composer from "./components/Composer.vue";
import Renderer from "./components/Renderer.vue";
import Animated from "./components/Animated.vue";
import SaveAndLoad from "./components/SaveAndLoad.vue";

export default {
  name: "App",
  components: {
    Composer,
    Renderer,
    Animated,
    SaveAndLoad
  },
  data() {
    return {
      scale: 15,
      minRendererWidth: 100,
      rendererBorderWidth: 2,
      rendererPadding: 5,
      currentFrame: 0,
      frames: makeFrames(20),
      composerPalette: []
    };
  },
  computed: {
    composingFrame() {
      return this.frames[this.currentFrame];
    },
    rendererBoxWidth() {
      let width = Math.max(
        12 * this.scale + (this.borderWidth + this.padding) * 2,
        this.minRendererWidth
      );
      return width + "px";
    },
    rendererBoxHeight() {
      return (
        this.frames.length * this.scale +
        (this.borderWidth + this.padding) * 2 +
        "px"
      );
    }
  },
  mounted() {
      this.render();
  },
  methods: {
    loadFrames(newFrames) {
      this.$refs.animated.stopAnimationAt0();
      this.currentFrame = 0;
      console.log("loaded new frames", newFrames);
      console.log("frames were", this.frames);
      this.frames.splice(0, this.frames.length, newFrames);
      console.log("frames are", this.frames);
      this.$refs.animated.restartAnimation();
    },
    loadPalette(palette) {
      this.composerPalette.splice(0, this.composerPalette.length, palette);
    },
    updateScale(event) {
      this.scale = parseInt(event.target.value);
      console.log("new scale is: " + this.scale);
      this.render();
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
      this.render();
    },
    deleteFrame() {
      this.$refs.animated.stopAnimationAt0();
      if (this.frames.length > 1) {
        this.frames.splice(this.currentFrame, 1);
        if (this.currentFrame === this.frames.length)
          this.currentFrame = this.currentFrame - 1;
        this.render();
      }
      this.$refs.animated.restartAnimation();
    },
    applyColor(color, spot) {
      this.frames[this.currentFrame][spot].red = color.red;
      this.frames[this.currentFrame][spot].green = color.green;
      this.frames[this.currentFrame][spot].blue = color.blue;
      this.render();
    },
    render() {
      this.$refs.renderer.render(this.scale);
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
#right-module {
  align-items: stretch;
  margin-left: 5mm;
}
#renderer {
  vertical-align: top;
  align-items: stretch;
}
</style>
