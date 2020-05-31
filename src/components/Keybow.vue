<template>
  <div class="keybow" :style="{width: keybowWidth, height: keybowHeight}">
    <div class="row">
      <button
        class="key"
        :style="{backgroundColor: color11, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(11)"
      >11</button>
      <button
        class="key"
        :style="{backgroundColor: color8, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(8)"
      >8</button>
      <button
        class="key"
        :style="{backgroundColor: color5, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(5)"
      >5</button>
      <button
        class="key"
        :style="{backgroundColor: color2, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(2)"
      >2</button>
    </div>
    <div class="row">
      <button
        class="key"
        :style="{backgroundColor: color10, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(10)"
      >10</button>
      <button
        class="key"
        :style="{backgroundColor: color7, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(7)"
      >7</button>
      <button
        class="key"
        :style="{backgroundColor: color4, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(4)"
      >4</button>
      <button
        class="key"
        :style="{backgroundColor: color1, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(1)"
      >1</button>
    </div>
    <div class="row">
      <button
        class="key"
        :style="{backgroundColor: color9, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(9)"
      >9</button>
      <button
        class="key"
        :style="{backgroundColor: color6, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(6)"
      >6</button>
      <button
        class="key"
        :style="{backgroundColor: color3, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(3)"
      >3</button>
      <button
        class="key"
        :style="{backgroundColor: color0, width: keyWidthMeasured, height: keyHeightMeasured, margin: keyMarginMeasured, border: keyBorderMeasured}"
        :disabled="!composing"
        @click="applyColor(0)"
      >0</button>
    </div>
  </div>
</template>

<script>
function styleColor(frameColor) {
  return (
    "rgb(" +
    frameColor.red +
    "," +
    frameColor.green +
    "," +
    frameColor.blue +
    ")"
  );
}
/*const frameToKey = [9,6,3,0,10,7,4,1,11,8,5,2]*/
const keyToFrame = [3, 7, 11, 2, 6, 10, 1, 5, 9, 0, 4, 8];
export default {
  name: "Keybow",
  props: {
    frame: {
      type: Array,
      required: true
    },
    composing: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      keySize: 12,
      keyMargin: 0.2,
      keyBorder: 0.8,
      measureUnit: "mm",
      keyBorderStyle: " solid grey"
    };
  },
  methods: {
    applyColor(key) {
      this.$emit("apply-color", keyToFrame[key]);
    }
  },
  computed: {
    color0() {
      return styleColor(this.frame[keyToFrame[0]]);
    },
    color1() {
      return styleColor(this.frame[keyToFrame[1]]);
    },
    color2() {
      return styleColor(this.frame[keyToFrame[2]]);
    },
    color3() {
      return styleColor(this.frame[keyToFrame[3]]);
    },
    color4() {
      return styleColor(this.frame[keyToFrame[4]]);
    },
    color5() {
      return styleColor(this.frame[keyToFrame[5]]);
    },
    color6() {
      return styleColor(this.frame[keyToFrame[6]]);
    },
    color7() {
      return styleColor(this.frame[keyToFrame[7]]);
    },
    color8() {
      return styleColor(this.frame[keyToFrame[8]]);
    },
    color9() {
      return styleColor(this.frame[keyToFrame[9]]);
    },
    color10() {
      return styleColor(this.frame[keyToFrame[10]]);
    },
    color11() {
      return styleColor(this.frame[keyToFrame[11]]);
    },
    keyWidthMeasured() {
      return this.keySize + this.measureUnit;
    },
    keyHeightMeasured() {
      return this.keySize + this.measureUnit;
    },
    keyMarginMeasured() {
      return this.keyMargin + this.measureUnit;
    },
    keyBorderMeasured() {
      return this.keyBorder + this.measureUnit + this.keyBorderStyle;
    },
    keybowWidth() {
      return (
        (this.keySize + this.keyBorder + this.keyMargin) * 4 + this.measureUnit
      );
    },
    keybowHeight() {
      return (
        (this.keySize + this.keyBorder + this.keyMargin) * 3 + this.measureUnit
      );
    }
  }
};
</script>

<style scoped>
.keybow {
  border: solid gold 1mm;
  border-radius: 5%;
  padding: 0.5mm;
}
.key {
  display: inline-block;
  border: solid grey;
  border-radius: 15%;
  color: black;
  font: outline;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: bold;
}
.row {
  display: block;
}
</style>
