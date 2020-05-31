<template>
  <div>
    <span class="title">Presets</span>
    <button
      v-for="mode in defaults"
      @click="restart(mode)"
      :key="mode.label"
      :style="{display: presetsDisposition}"
    >Load {{mode.label}}</button>
  </div>
</template>

<script>
export default {
  name: "Presets",
  props: {
    defaults: {
      type: Array,
      required: true
    }
  },
  methods: {
    restart(mode) {
      this.$emit("load-frames", mode.frames);
    }
  },
  computed: {
    presetsDisposition() {
      if (this.defaults.length > 3) return "block";
      else return "inline-block";
    }
  }
};
</script>

<style scoped>
.title {
  display: block;
  font-weight: bolder;
  font-size: 120%;
}
</style>