<template>
  <span></span>
</template>

<script>
export default {
  name: 'Comida',
  emits: ["posicionComida"],
  props: {
    canvas: {
      required: true,
    },
    estado: {
      type: String,
      required: true,
    },
    difX: {
      type: Number,
      required: true,
    },
    difY: {
      type: Number,
      required: true,
    },
    repintar: {
      type: Number,
      required: true,
    },
    anchoTablero: {
      type: Number,
      required: true,
    },
    altoTablero: {
      type: Number,
      required: true,
    },
    anchoPixelSerpiente: {
      type: Number,
      required: true,
    },
    serpiente: {
      required: true,
    },
  },
  data() {
    return {
      posicionComida: null,
    };
  },
  computed: {
    ctx() {
      return this.canvas;
    },
  },
  watch: {
    estado: function(nuevoEstado) {
      if (nuevoEstado === 'jugando') {
        this.colocarComida();
      }
    },
    repintar: function() {
      if (this.posicionComida !== null) {
        this.pintar();
      }
    },
    serpiente: function() {
      if (this.posicionComida !== null && this.serpiente[0][0] === this.posicionComida[0] && this.serpiente[1][0] === this.posicionComida[1]) {
        this.colocarComida();
      }
    },
  },
  methods: {
    colocarComida() {
      this.posicionComida = [this.buscarHuecoLibreComidaX(), this.buscarHuecoLibreComidaY()];
      this.$emit("posicionComida", this.posicionComida);
    },
    buscarHuecoLibreComidaX() {
      let posXComida = this.rand(0, (this.anchoTablero / this.anchoPixelSerpiente));
      let posXSerpiente = this.serpiente[0];
      if (posXSerpiente.includes(posXComida)) {
        return this.buscarHuecoLibreComidaX();
      }
      return posXComida;
    },
    buscarHuecoLibreComidaY() {
      let posYComida = this.rand(0, (this.altoTablero / this.anchoPixelSerpiente));
      let posYSerpiente = this.serpiente[1];
      if (posYSerpiente.includes(posYComida)) {
        return this.buscarHuecoLibreComidaY();
      }
      return posYComida;
    },
    rand(min, max) {
      return Math.floor(Math.random() * (max - min) + min);
    },
    pintar() {
      this.ctx.beginPath();
      this.ctx.fillRect(this.difX + (this.posicionComida[0] * this.anchoPixelSerpiente), this.difY + (this.posicionComida[1] * this.anchoPixelSerpiente), this.anchoPixelSerpiente, this.anchoPixelSerpiente);
      this.ctx.stroke();
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#canvas {
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  min-width: 200px;
  height: 100%;
  min-height: 100px;
}
</style>
