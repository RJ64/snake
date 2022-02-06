<template>
  <span></span>
</template>

<script>
export default {
  name: 'Tablero',
  emits: ["cambiarEstado", "cambiarDif"],
  props: {
    canvas: {
      required: true,
    },
    estado: {
      type: String,
      required: true,
    },
    repintar: {
      type: Number,
      required: true,
    },
    infoZonaJuego: {
      required: true,
    },
  },
  data() {
    return {
      contadorCuentaAtras: false,
    };
  },
  computed: {
    ctx() {
      return this.canvas;
    },
    posX() {
      let pos = ((window.innerWidth - this.infoZonaJuego.anchoTablero) / 2);
      this.$emit("cambiarDif", pos, this.posY);
      return pos;
    },
    posY() {
      let pos = ((window.innerHeight - this.infoZonaJuego.altoTablero) / 2);
      this.$emit("cambiarDif", this.posX, pos);
      return pos;
    },
    centroX() {
      return window.innerWidth / 2;
    },
    centroY() {
      return window.innerHeight / 2;
    },
  },
  watch: {
    estado: function(nuevoEstado) {
      if (nuevoEstado === 'iniciando') {
        this.iniciarCuentaAtras();
      }
    },
    repintar: function() {
      this.pintar();
    },
  },
  methods: {
    pintarTablero() {
      this.ctx.beginPath();
      this.ctx.rect(this.posX, this.posY, this.infoZonaJuego.anchoTablero, this.infoZonaJuego.altoTablero);
      this.ctx.stroke();
    },
    pintarNumeroCuentaAtras(numero) {
      this.ctx.textAlign = "center";
      this.ctx.font = "30px Arial";
      this.ctx.fillText(numero, this.centroX, this.centroY);
    },
    iniciarCuentaAtras() {

      this.contadorCuentaAtras = 3;
      setTimeout(() => {
        this.contadorCuentaAtras = 2;
        setTimeout(() => {
          this.contadorCuentaAtras = 1;
          setTimeout(() => {
            this.contadorCuentaAtras = false;
            this.$emit("cambiarEstado", "jugando");
          }, 1000);
        }, 1000);
      }, 1000);

    },
    pintar() {
      this.pintarTablero()
      if (this.contadorCuentaAtras) {
        this.pintarNumeroCuentaAtras(this.contadorCuentaAtras);
      }
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
