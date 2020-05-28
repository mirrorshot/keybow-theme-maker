<template>
  <div class="renderer" :style="{width: rendererWidth, height: rendererHeight, borderWidth: borderWidth, padding: padding}">
    <canvas ref="renderedCanvas" :style="{width: rendererWidth, height: rendererHeight}"></canvas>
  </div>
</template>

<script>
function renderWhite(canvas, imageWidth, imageHeight){
  //console.log("canvas to white");
  let context = canvas.getContext("2d");
  let imageData = context.createImageData(imageWidth, imageHeight);
  for(let i=0; i<imageData.data.length; i++)
    imageData.data[i]=255;
  context.putImageData(imageData, 0, 0);
  context.save();
}

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
  data() {
    return {
      lastFrameCount: this.frames.length
    }
  },
  mounted() {
    this.render();
  },
  methods: {
    render() {
      let canvas = this.$refs.renderedCanvas;
      let context = canvas.getContext("2d");
      let imageWidth = 12 * this.scale;
      let imageHeight = this.frames.length * this.scale;
      if(this.frames.length !== this.lastFrameCount){
        this.lastFrameCount = this.frames.length;
        renderWhite(canvas, imageWidth, imageHeight);
      }
      //console.log("rendering at scale: " + this.scale);
      //console.log("ImageData: " + imageWidth + "x" + imageHeight);
      let imageData = context.createImageData(
        imageWidth,
        imageHeight
      );
      let lastPoint=-4;
      const rowLen = 12 * this.scale * 4;
      //console.log("scaled row length: " + rowLen);
      //console.log("scaled line height: " + rowLen * this.scale);
      for (let rowNum = 0; rowNum < this.frames.length; rowNum++) {
        let rowBlock = rowLen * rowNum * this.scale;
        //console.log("row:\n  num: " + rowNum + "\n  block_start: " + rowBlock);
        for (let i = 0; i < this.scale; i++) {
          let rowStart = rowBlock + (rowLen * i);
          //console.log("  color_row:\n    num: " + i + "\n    start: " + rowStart);
          for (let colorNum = 0; colorNum < 12; colorNum++) {
            let red = this.frames[rowNum][colorNum].red;
            let green = this.frames[rowNum][colorNum].green;
            let blue = this.frames[rowNum][colorNum].blue;
            let colorBlock = this.scale * colorNum * 4;
            //console.log("    color: \n      num: " + colorNum + "\n      block_start: " + colorBlock);
            for (let j = 0; j < this.scale; j++) {
              let point = rowStart + colorBlock + (j * 4);
              //if(point !== (lastPoint+4))
                //console.log("Discontinue from " + lastPoint + " to " + point)
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
