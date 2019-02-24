<template>
  <canvas class="canvas" @click="draw" ref="canvas" :width="80" :height="80"></canvas>
</template>

<script>
export default {
  name: "PanelCanvas",
  props: ["baseSrc", "x", "y", "number", "size"],
  data() {
    return {
      ctx: null,
      canvas: null,
      img: null
    };
  },
  mounted() {
    let vm = this;
    vm.canvas = vm.$refs.canvas;
    vm.ctx = vm.canvas.getContext("2d");
    vm.img = document.createElement("img");
    this.img.onload = () => {
      this.draw();
    };
  },
  methods: {
    draw() {
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);

      let panelWidth = this.img.width / this.size;
      let panelHeight = this.img.height / this.size;
      let offsetX = panelWidth * this.x;
      let offsetY = panelHeight * this.y;

      if (this.isEmptyPanel) {
        this.ctx.rect(0, 0, 80, 80);
        this.ctx.fill();
      } else {
        this.ctx.drawImage(
          this.img,
          offsetX,
          offsetY,
          panelWidth,
          panelHeight,
          0,
          0,
          80,
          80
        );
      }
    }
  },
  computed: {
    isEmptyPanel(number) {
      return Math.pow(this.size, 2) === this.number;
    }
  },
  watch: {
    baseSrc() {
      this.img.src = this.baseSrc;
    },
    number() {
      this.draw();
    }
  }
};
</script>

<style scoped>
</style>
