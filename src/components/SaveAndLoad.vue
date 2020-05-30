<template>
  <div class="controls">
    <div>
      <label for="loadingFramesFile">JSON:</label>
      <input type="file" id="loadingFramesFile" ref="loadingFramesFile" />
      <button @click="loadJson">Load</button>
    </div>
    <div>
      <label for="loadingFramesImage">PNG:</label>
      <input type="file" id="loadingFramesImage" ref="loadingFramesImage" />
      <button @click="loadPng">Load</button>
    </div>
    <div>
      <label for="loadingPaletteFile">Palette:</label>
      <input type="file" id="loadingPaletteFile" ref="loadingPaletteFile" />
      <button @click="loadPalette">Load</button>
    </div>
    <div>
      <button @click="saveJson">Save</button>
      <button @click="download">Download</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "SaveAndLoad",
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
    }
  },
  methods: {
    loadJson() {
      let reader = new FileReader();
      reader.onload = event => {
        let loaded = JSON.parse(event.target.result);
        console.log("parsed input", loaded);
        this.$emit("loading-frames", loaded);
      };
      reader.readAsText(this.$refs.loadingFramesFile.files[0]);
    },
    loadPng() {
      console.log("Loading PNG");
    },
    loadPalette() {
      console.log("Loading palette");
    },
    saveJson() {
      const data = JSON.stringify(this.frames);
      const filename = "keybow-theme.json";
      var blob = new Blob([data], { type: "application/json" });
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
    },
    download() {}
  }
};
</script>
