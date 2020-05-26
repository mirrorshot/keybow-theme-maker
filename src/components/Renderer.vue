<template>
  <div class="renderer" :style="{width: rendererWidth, height: rendererHeight, borderWidth: borderWidth, padding: padding}">
    <canvas ref="renderedCanvas" :style="{width: rendererWidth, height: rendererHeight}"></canvas>
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
    rendererWidth: {
        type: String,
        required: true
    },
    rendererHeight: {
        type: String,
        required: true
    },
    borderWidth: {
        type: String,
        required: true
    },
    padding: {
        type: String,
        required: true
    }
  },
  mounted() {
    this.render();
  },
  methods: {
    render() {
      console.log("rendering at scale: " + this.scale);
      let context = this.$refs.renderedCanvas.getContext("2d");
      let imageWidth = 12 * this.scale;
      let imageHeight = this.frames.length * this.scale;
      console.log("ImageData: " + imageWidth + "x" + imageHeight);
      let imageData = context.createImageData(
        imageWidth,
        imageHeight
      );
      let lastPoint=-4;
      const rowLen = 12 * this.scale * 4;
      console.log("scaled row length: " + rowLen);
      console.log("scaled line height: " + rowLen * this.scale);
      for (let rowNum = 0; rowNum < this.frames.length; rowNum++) {
        let rowBlock = rowLen * rowNum * this.scale;
        for (let i = 0; i < this.scale; i++) {
          for (let colorNum = 0; colorNum < 12; colorNum++) {
            let red = this.frames[rowNum][colorNum].red;
            let green = this.frames[rowNum][colorNum].green;
            let blue = this.frames[rowNum][colorNum].blue;
            let colorBlock = this.scale * colorNum * 4;
            for (let j = 0; j < this.scale; j++) {
              let point = rowBlock + rowLen * i + (colorBlock + j * 4);
              if(point !== (lastPoint+4))
                console.log("Discontinue from " + lastPoint + " to " + point)
              lastPoint = point;
              imageData.data[point] = red;
              imageData.data[point + 1] = green;
              imageData.data[point + 2] = blue;
              imageData.data[point + 3] = 255;
            }
          }
        }
      }
      /*if((lastPoint + 4) !== imageData.data.length)*/
      console.log("Last colored pixel: " + lastPoint + " data size: " + imageData.data.length);
      context.putImageData(imageData, 0, 0);
      context.save();
    }
  },
  computed: {
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
