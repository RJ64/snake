<template>
  <canvas id="canvas"></canvas>
  <Tablero :info="info" @cambiarEstado="cambiarEstado" @cambiarDif="cambiarDif"/>
  <Serpiente :info="info" @posicionSerpiente="posicionSerpiente"/>
  <Comida :info="info" @posicionComida="posicionComida"/>
</template>

<script>
import Tablero from './components/Tablero.vue'
import Serpiente from './components/Serpiente.vue'
import Comida from './components/Comida.vue'

export default {
  name: 'SnakeGame',
  components: {
    Tablero, Serpiente, Comida
  },
  data() {
    return {
      info: {
        canvas: null,
        refresco: 20,
        repintar: 1,
        estado: '', // '' - Sin empezar, 'iniciando' - Cuenta atrás, 'jugando' - Jugando
        zonaJuego: {
          anchoPixelSerpiente: 4,
          anchoTablero: 400,
          altoTablero: 200,
          difX: 0,
          difY: 0,
        },
        teclasPulsadas: {
          arriba: false,
          derecha: false,
          abajo: false,
          izquierda: false,
        },
        posiciones: {
          comida: null,
          serpiente: [[11, 10], [25, 25]],
        },
      },
    };
  },
  computed: {
    canvas() {
      return this.info.canvas;
    },
  },
  methods: {
    posicionComida(nuevaPosicion) {
      this.info.posiciones.comida = nuevaPosicion;
    },
    posicionSerpiente(nuevaPosicion) {
      this.info.posiciones.serpiente = nuevaPosicion;
    },
    limpiar() {
      this.canvas.clearRect(0, 0, window.innerWidth, window.innerHeight);
    },
    iniciarJuego() {
      this.cambiarEstado('iniciando');
    },
    cambiarEstado(nuevoEstado) {
      this.info.estado = nuevoEstado;
    },
    cambiarDif(difX, difY) {
      this.info.zonaJuego.difX = difX;
      this.info.zonaJuego.difY = difY;
    },
    prepararCanvas() {
      var c = document.getElementById("canvas");
      var ctx = c.getContext("2d");    
      ctx.canvas.width  = window.innerWidth;
      ctx.canvas.height = window.innerHeight;
      this.info.canvas = ctx;
    },
    inicializar() {
      this.prepararCanvas();
      this.escucharTeclasPulsadas();
      setInterval(() => {
        this.limpiar();
        this.info.repintar *= -1;
      }, this.info.refresco);
    },
    escucharTeclasPulsadas() {
      window.addEventListener("keyup", (e) => {

        if (e.key === "ArrowUp") {
          this.info.teclasPulsadas.arriba = false;
        }
        else if (e.key === "ArrowRight") {
          this.info.teclasPulsadas.derecha = false;
        }
        else if (e.key === "ArrowDown") {
          this.info.teclasPulsadas.abajo = false;
        }
        else if (e.key === "ArrowLeft") {
          this.info.teclasPulsadas.izquierda = false;
        }
      });

      window.addEventListener("keydown", (e) => {
        if (e.key === "ArrowUp") {
          this.info.teclasPulsadas.arriba = true;
        }
        else if (e.key === "ArrowRight") {
          this.info.teclasPulsadas.derecha = true;
        }
        else if (e.key === "ArrowDown") {
          this.info.teclasPulsadas.abajo = true;
        }
        else if (e.key === "ArrowLeft") {
          this.info.teclasPulsadas.izquierda = true;
        }
      });

    },
  },
  mounted() {

    this.inicializar();
    setTimeout(() => {
      this.iniciarJuego();
    }, 500);

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
