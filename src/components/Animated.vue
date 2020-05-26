<template>
    <div class="animated">
        <keybow :frame="frames[current]"></keybow>
    </div>
</template>

<script>
import Keybow from './Keybow.vue'

export default {
    name: "Animated",
    props: {
        frames: {
            type: Array,
            required: true
        }
    },
    data() {
        return {
            currentFrame: 0
        }
    },
    mounted: {},
    computed: {
        current() {
            this.currentFrame++;
            if(this.currentFrame === this.frames.length)
                this.currentFrame=0;
            return this.currentFrame;
        }
    }
}
</script>