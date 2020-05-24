<template>
<div class="frameComposer">
    <Keybow 
    :frame="currentFrame" 
    composing 
    @apply-color="applyColor"
    />
    <div class="frameControls">
        <button @click="firstFrame">{{firstFrameSymbol}}</button>
        <button @click="prevFrame">{{prevFrameSymbol}}</button>
        <button @click="addFrameBefore">{{addFrameBeforeSymbol}}</button>
        <label class="frameLabel">{{currentFrameNumber + 1}}</label>
        <button @click="addFrameAfter">{{addFrameAfterSymbol}}</button>
        <button @click="nextFrame">{{nextFrameSymbol}}</button>
        <button @click="lastFrame">{{lastFrameSymbol}}</button>
    </div>
    <color-picker 
    theme="light"
    :color="color"
    @changeColor="usingColor"
    />
</div>
</template>

<script>
import Keybow from './Keybow.vue'
import colorPicker from '@caohenghu/vue-colorpicker'

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
        }
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
                this.$emit("add-frame-before")
            },
            addFrameAfter() {
                this.$emit("add-frame-after");
            },
        usingColor(color) {
            console.log("Using color: " + color.rgba)
            this.currentColor = {
                red: color.rgba.r,
                green: color.rgba.g,
                blue: color.rgba.b
            }
        },
        applyColor(spot) {
            console.log("Apply color to spot: "+ spot);
            this.$emit("apply-color", this.currentColor, spot);
        }
    },
    computed: {
        color() {
            return "rgb("+
            this.currentColor.red+","+
            this.currentColor.green+","+
            this.currentColor.blue+")";
        }
    }
}
</script>

<style scoped>
.frameComposer{
    width: 218px;
}
.frameLabel{
    padding: 1mm;
}
.frameControls{
    padding: 2mm;
}
</style>