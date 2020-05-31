<template>
  <div id="app">
    <div class="module" :style="{width: rendererBoxWidth, height: rendererBoxHeight}">
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
    <div class="module">
      <div id="animated">
        <animated ref="animated" :frames="frames"></animated>
      </div>
      <div id="composer">
        <composer
          :currentFrame="frames[currentFrame]"
          :currentFrameNumber="currentFrame"
          :colorPalette="composerPalette"
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
    <div class="module">
      <SaveAndLoad
        :palette="composerPalette"
        :frames="frames"
        v-bind:scale="saveScale"
        @loading-frames="loadFrames"
        @loading-palette="loadPalette"
      />
      <div>
        <label for="paletteSize">Max colors in palette:</label>
        <input id="paletteSize" ref="paletteSize" v-bind:value="paletteMaxSize" />
        <button @click="setPaletteSize">Set</button>
      </div>
      <Presets :defaults="composedDefaults" @load-frames="loadFrames"></Presets>
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
  for (let color = 0; color < frame.length; color++) {
    newFrame.push(
      makeColor(frame[color].red, frame[color].green, frame[color].blue)
    );
  }
  return newFrame;
}
function makeRandomFrame() {
  let frame = [];
  for (let i = 0; i < 12; i++) frame.push(makeRandColor());
  return frame;
}
function makeWhiteFrame() {
  let frame = [];
  for (let i = 0; i < 12; i++) frame.push(makeColor(255, 255, 255));
  return frame;
}
function makeBlackFrame() {
  let frame = [];
  for (let i = 0; i < 12; i++) frame.push(makeColor(0, 0, 0));
  return frame;
}
/*function makeRainbowFrame() {
  let frame = [];
  frame.push(makeColor());
  frame.push(makeColor());
  frame.push(makeColor());
  frame.push(makeColor());
  frame.push(makeColor());
  frame.push(makeColor());
  frame.push(makeColor());
  frame.push(makeColor());
  frame.push(makeColor());
  frame.push(makeColor());
  frame.push(makeColor());
  frame.push(makeColor());
  return frame;
}*/
function makeRandomFrames(n) {
  let frames = [];
  for (let i = 0; i < n; i++) frames.push(makeRandomFrame());
  return frames;
}
import Composer from "./components/Composer.vue";
import Renderer from "./components/Renderer.vue";
import Animated from "./components/Animated.vue";
import SaveAndLoad from "./components/SaveAndLoad.vue";
import Presets from "./components/Presets.vue";

export default {
  name: "App",
  components: {
    Composer,
    Renderer,
    Animated,
    SaveAndLoad,
    Presets
  },
  data() {
    return {
      scale: 15,
      saveScale: 1,
      minRendererWidth: 100,
      rendererBorderWidth: 2,
      rendererPadding: 5,
      currentFrame: 0,
      frames: makeRandomFrames(20),
      paletteMaxSize: 20,
      composerPalette: [],
      composedDefaults: [
        /*{ label: "Rainbow", frames: [makeRainbowFrame()] },*/
        { label: "White", frames: [makeWhiteFrame()] },
        { label: "Black", frames: [makeBlackFrame()] }
      ]
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
      const oldSize = this.frames.length;
      this.frames = this.frames.concat(newFrames);
      this.frames.splice(0, oldSize);
      this.render();
      this.$refs.animated.restartAnimation();
    },
    loadPalette(palette) {
      this.composerPalette.splice(0, this.composerPalette.length, palette);
    },
    updateScale(event) {
      this.scale = parseInt(event.target.value);
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
      const colorIndex = this.composerPalette.findIndex(c => {
        return (
          c.red === color.red &&
          c.green === color.green &&
          c.blue === color.blue
        );
      });
      if (colorIndex === -1) {
        this.composerPalette.unshift(color);
        if (this.composerPalette.length > this.paletteMaxSize)
          this.composerPalette.pop();
      } else if (colorIndex !== 0) {
        this.composerPalette.splice(colorIndex, 1);
        this.composerPalette.unshift(color);
      }
      this.frames[this.currentFrame][spot].red = color.red;
      this.frames[this.currentFrame][spot].green = color.green;
      this.frames[this.currentFrame][spot].blue = color.blue;
      this.render();
    },
    render() {
      this.$refs.renderer.render(this.scale, this.frames);
    },
    setPaletteSize() {
      this.paletteMaxSize = this.$refs.paletteSize.value;
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
.module {
  align-items: stretch;
  margin-left: 5mm;
  vertical-align: top;
}
#renderer {
  vertical-align: top;
  align-items: stretch;
}
</style>
