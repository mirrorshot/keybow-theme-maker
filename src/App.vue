<template>
  <div id="app">
    <renderer :frames="frames" v-bind:scale="50"/>
    <composer 
    :currentFrame="composingFrame" 
    :currentFrameNumber="currentFrame"
    @apply-color="applyColor"
    @to-first-frame="toFirstFrame"
    @to-last-frame="toLastFrame"
    @to-prev-frame="toPrevFrame"
    @to-next-frame="toNextFrame"
    @add-frame-before="addFrameBefore"
    @add-frame-after="addFrameAfter"
    />
  </div>
</template>

<script>
function makeColor(red, green, blue) {
  return {
    red: red,
    green: green,
    blue: blue
  }
}
function duplicateFrame(frame) {
  let newFrame = [];
  for(let color in frame){
    newFrame.push(makeColor(color.red, color.green, color.blue));
  }
}
function makeFrame() {
  return [
        makeColor(255,0,0),
        makeColor(0,255,0),
        makeColor(0,0,255),
        makeColor(255,255,255),
        makeColor(0,255,255),
        makeColor(255,255,0),
        makeColor(255,0,255),
        makeColor(0,0,0),
        makeColor(0,0,255),
        makeColor(0,255,0),
        makeColor(255,0,0),
        makeColor(255,255,255)
      ];
}
function makeFrames(n) {
  let frames = [];
  for(let i = 0; i < n; i++)
    frames.push(makeFrame());
  return frames;
}
import Composer from './components/Composer.vue'
import Renderer from './components/Renderer.vue'

export default {
  name: 'App',
  components: {
    Composer,
    Renderer
  },
  data() {
    return {
      currentFrame: 0,
      frames: makeFrames(100)
    }
  },
  computed: {
    composingFrame() {
      console.log("composed frame updated")
      return this.frames[this.currentFrame];
    }
  },
  methods: {
            toFirstFrame() {
                this.currentFrame=0;
            },
            toLastFrame() {
                this.currentFrame=this.frames.length-1;
            },
            toPrevFrame() {
              if(this.currentFrame>0)
              this.currentFrame=this.currentFrame-1;
            },
            toNextFrame() {
              if(this.currentFrame<this.frames.length-1)
                this.currentFrame=this.currentFrame+1;
            },
            addFrameBefore() {
                this.frames.splice(this.currentFrame, 0, duplicateFrame(this.frames[this.currentFrame]));
            },
            addFrameAfter() {
                this.frames.splice(this.currentFrame+1, 0, duplicateFrame(this.frames[this.currentFrame]));
                this.currentFrame = this.currentFrame + 1;
            },
    applyColor(color, spot){
      console.log("Appling color "+ color + " to spot " + spot);
      console.log(this.frames[this.currentFrame][spot])
      this.frames[this.currentFrame][spot].red = color.red;
      this.frames[this.currentFrame][spot].green = color.green;
      this.frames[this.currentFrame][spot].blue = color.blue;
      console.log(this.frame[spot])
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
