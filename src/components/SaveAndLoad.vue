<template>
  <div class="controls">
    <div>
      <input
        class="buttoned"
        type="file"
        accept="application/json"
        id="loadingFramesFile"
        ref="loadingFramesFile"
        @change="loadingFramesJson"
      />
      <button @click="loadJson">Load JSON</button>
      <input
        class="buttoned"
        type="file"
        accept="image/png"
        id="loadingFramesImage"
        ref="loadingFramesImage"
        @change="loadingFramesPNG"
      />
      <button @click="loadPNG" disabled>Load PNG</button>
      <input
        class="buttoned"
        type="file"
        accept="application/json"
        id="loadingPaletteFile"
        ref="loadingPaletteFile"
        @change="loadingPaletteJson"
      />
      <button @click="loadPalette">Load Palette</button>
    </div>
    <div>
      <button @click="saveJson">Save JSON</button>
      <button @click="download">Download</button>
      <button @click="savePaletteJson">Save Palette</button>
    </div>
    <div class="hidden">
      <theme-canvas ref="theme"></theme-canvas>
    </div>
  </div>
</template>

<script>
function saveFile(filename, blob) {
  if (window.navigator.msSaveOrOpenBlob) {
    window.navigator.msSaveBlob(blob, filename);
  } else {
    var elem = window.document.createElement("a");
    elem.href = window.URL.createObjectURL(blob);
    elem.download = filename;
    document.body.appendChild(elem);
    elem.click();
    document.body.removeChild(elem);
  }
}
import ThemeCanvas from "./ThemeCanvas.vue";

export default {
  name: "SaveAndLoad",
  components: {
    ThemeCanvas
  },
  props: {
    palette: {
      type: Array,
      required: false,
      default() {
        return [];
      }
    },
    frames: {
      type: Array,
      required: true
    },
    scale: {
      type: Number,
      default: 1
    }
  },
  methods: {
    loadingFramesJson(event) {
      let reader = new FileReader();
      reader.onload = event => {
        let loaded = JSON.parse(event.target.result);
        this.$emit("loading-frames", loaded);
      };
      reader.readAsText(event.target.files[0]);
    },
    loadJson() {
      this.$refs.loadingFramesFile.click();
    },
    loadPng() {
      console.error("PNG loading not implemented!");
    },
    loadingPaletteJson(event) {
      let reader = new FileReader();
      reader.onload = event => {
        let loaded = JSON.parse(event.target.result);
        this.$emit("loading-palette", loaded);
      };
      reader.readAsText(event.target.files[0]);
    },
    loadPalette() {
      this.$refs.loadingPaletteFile.click();
    },
    savePaletteJson() {
      const data = JSON.stringify(this.palette);
      const filename = "keybow-palette.json";
      var blob = new Blob([data], { type: "application/json" });
      saveFile(filename, blob);
    },
    saveJson() {
      const data = JSON.stringify(this.frames);
      const filename = "keybow-theme.json";
      var blob = new Blob([data], { type: "application/json" });
      saveFile(filename, blob);
    },
    download() {
      this.$refs.theme.render(this.scale, this.frames);
      const imageType = "image/png";
      this.$refs.theme.saveImage(blob => {
        saveFile("theme.png", blob);
      }, imageType);
    }
  }
};
</script>

<style scoped>
.hidden {
  display: none;
}
input.buttoned {
  display: none;
}
</style>