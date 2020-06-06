<template>
  <div class="gradienter"></div>
</template>

<script>
export default {
  name: "Gradienter",
  props: {
    frameSize: {
      type: Number,
      default: 12
    }
  },
  methods: {
    composeGradients(frames, steps = 1, around = false) {
      let extended = [];
      for (let i = 0; i < frames.length - 1; i++) {
        const currentFrame = frames[i];
        const nextFrame = frames[i + 1];
        const interpolatedFrames = this.interpolateFrames(
          currentFrame,
          nextFrame,
          parseInt(steps) + 2
        );
        for (let step = 0; step < interpolatedFrames.length; step++)
          extended.push(interpolatedFrames[step]);
      }
      if(around) {
        const currentFrame = frames[frames.length-1];
        const nextFrame = frames[0];
        const interpolatedFrames = this.interpolateFrames(
          currentFrame,
          nextFrame,
          parseInt(steps) + 2
        );
        for (let step = 0; step < interpolatedFrames.length-1; step++)
          extended.push(interpolatedFrames[step]);
      }
      return extended;
    },
    interpolateFrames(frame1, frame2, steps) {
      const interpolated = [];
      for (let i = 0; i < steps; i++) interpolated.push([]);
      for (let color = 0; color < this.frameSize; color++) {
        const gradient = this.interpolateColors(
          frame1[color],
          frame2[color],
          steps
        );
        for (let i = 0; i < steps; i++) interpolated[i][color] = gradient[i];
      }
      return interpolated;
    },
    interpolateColors(color1, color2, steps) {
      const stepFactor = 1 / (steps - 1);
      const interpolatedColorArray = [];
      for (var i = 0; i < steps; i++) {
        interpolatedColorArray.push(
          this.interpolateColor(color1, color2, stepFactor * i)
        );
      }
      return interpolatedColorArray;
    },
    interpolateColor(color1, color2, factor = 0.5) {
      return {
        red: Math.round(color1.red + factor * (color2.red - color1.red)),
        green: Math.round(
          color1.green + factor * (color2.green - color1.green)
        ),
        blue: Math.round(color1.blue + factor * (color2.blue - color1.blue))
      };
    }
  }
};
</script>

<style scoped>
.gradienter {
  display: none;
}
</style>