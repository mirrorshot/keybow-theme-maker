<template>
    <div class="renderer" :style="{width: rendererWidth}">
        <canvas ref="renderedCanvas"></canvas>
        {{rendered}}
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
        }
    },
    computed: {
        rendered() {
            let context = this.$refs['renderedCanvas'].getContext("2d");
            let imageData = context.createImageData(12*this.scale, this.frames.length * this.scale);
            const rowLen = 12*this.scale*4;
            for(let rowNum=0; rowNum<this.frames; rowNum++) {
                for(let i=0; i<this.scale; i++) {
                    let rowBlock = (rowLen*rowNum*this.scale);
                    for(let colorNum=0; colorNum<12; colorNum++) {
                        let red = this.frames[rowNum][colorNum].red;
                        let green = this.frames[rowNum][colorNum].green;
                        let blue = this.frames[rowNum][colorNum].blue;
                        let colorBlock = (this.scale*colorNum*4);
                        for(let j=0; j<this.scale; j++) {
                            let point = (rowBlock+(rowLen*i))+(colorBlock+(j*4));
                            imageData.data[point]=red;
                            imageData.data[point+1]=green;
                            imageData.data[point+2]=blue;
                            imageData.data[point+3]=255;
                        }
                    }
                }
            }
            context.putImageData(imageData,0,0);
            context.save();
            return imageData.length;
        },
        imageWidth() {
            return 12 * this.scale;
        },
        imageHeight() {
            return this.frames.length * this.scale;
        },
        rendererWidth() {
            return (12*this.scale)+30;
        }
    }
}
</script>

<style scoped>
.renderer{
    height: 100%;
    display: inline-block;
}
</style>
