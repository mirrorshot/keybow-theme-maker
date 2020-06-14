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
        <div>
          <Gradienter ref="gradienter" :frameSize="frameSize"></Gradienter>
          <label for="gradientSteps">Gradient steps:</label>
          <input
            type="number"
            name="gradientSteps"
            ref="gradientSteps"
            size="8"
            maxlength="8"
            value="10"
          />
          <div>
            <button @click="computeGradient">compute</button>
            <button @click="computeGradientAround">around</button>
          </div>
          <div>
            <span class="past">{{framesHistory.previous.length}}</span>
            <button @click="revert" :disabled="!canRevert">Revert</button>
            <button @click="redo" :disabled="!canRedo">Redo</button>
            <span class="future">{{framesHistory.next.length}}</span>
          </div>
        </div>
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
        ></composer>
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
      <Presets
        :defaults="composedDefaults"
        :palette="composerPalette"
        v-bind:frameSize="frameSize"
        @load-frames="loadFrames"
      ></Presets>
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
  return frame.map(c => {
    return makeColor(c.red, c.green, c.blue);
  });
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
import Gradienter from "./components/Gradienter.vue";

export default {
  name: "App",
  components: {
    Composer,
    Gradienter,
    Renderer,
    Animated,
    SaveAndLoad,
    Presets
  },
  data() {
    return {
      frameSize: 12,
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
      ],
      framesHistory: {
        size: 20,
        previous: [],
        next: []
      }
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
    },
    canRevert() {
      return this.framesHistory.previous.length > 0;
    },
    canRedo() {
      return this.framesHistory.next.length > 0;
    }
  },
  mounted() {
    this.render();
  },
  methods: {
    loadFrames(newFrames) {
      this.savePast();
      this.loadFramesNoHistory(newFrames);
    },
    loadFramesNoHistory(newFrames) {
      this.$refs.animated.stopAnimationAt0();
      this.currentFrame = 0;
      this.frames.splice(0, this.frames.length, ...newFrames);
      this.render();
      this.$refs.animated.restartAnimation();
    },
    loadPalette(palette) {
      const oldSize = this.composerPalette.length;
      this.composerPalette = this.composerPalette.concat(palette);
      this.composerPalette.splice(0, oldSize);
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
    addFramesInPosition(position, frames) {
      if(frames === undefined)
        frames = [this.frames[this.currentFrame]];
      this.savePast();
      this.frames.splice(position, 0, ...frames);
      this.render();
    },
    addFrameBefore() {
      this.addFramesInPosition(this.currentFrame);
    },
    addFrameAfter() {
      this.addFramesInPosition(this.currentFrame + 1);
    },
    deleteFrame() {
      this.savePast();
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
      this.savePast();
      this.updatePalette(color);
      this.frames[this.currentFrame][spot].red = color.red;
      this.frames[this.currentFrame][spot].green = color.green;
      this.frames[this.currentFrame][spot].blue = color.blue;
      this.render();
    },
    updatePalette(color) {
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
        this.composerPalette.unshift(this.composerPalette.pop(colorIndex));
      }
    },
    render() {
      this.$refs.renderer.render(this.scale, this.frames);
    },
    setPaletteSize() {
      this.paletteMaxSize = this.$refs.paletteSize.value;
    },
    computeGradient(around = false) {
      this.loadFrames(
        this.$refs.gradienter.composeGradients(
          this.frames,
          this.$refs.gradientSteps.value,
          around
        )
      );
    },
    computeGradientAround() {
      this.computeGradient(true);
    },
    savePast() {
      this.framesHistory.next.splice(0, this.framesHistory.next.length);
      while (this.framesHistory.previous.length >= this.framesHistory.size)
        this.framesHistory.previous.pop(0);
      this.framesHistory.previous.push(this.duplicateCurrent());
    },
    duplicateCurrent() {
      return this.frames.map(duplicateFrame);
    },
    revert() {
      while (
        this.framesHistory.previous.length + this.framesHistory.next.length >=
        this.framesHistory.size
      )
        if (this.framesHistory.next.length === 0)
          this.framesHistory.previous.pop(0);
        else this.framesHistory.next.pop(0);
      this.framesHistory.next.push(this.duplicateCurrent());
      this.loadFramesNoHistory(this.framesHistory.previous.pop());
    },
    redo() {
      this.framesHistory.previous.push(this.duplicateCurrent());
      this.loadFramesNoHistory(this.framesHistory.next.pop());
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
.past {
  padding-right: 2mm;
}
.future {
  padding-left: 2mm;
}
</style>
