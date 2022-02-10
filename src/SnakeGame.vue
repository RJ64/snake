<template>
  <canvas id="canvas"></canvas>
  <Tablero :estado="estado" :canvas="canvas" :repintar="repintar" :infoZonaJuego="infoZonaJuego" @cambiarEstado="cambiarEstado" @cambiarDif="cambiarDif"/>
  <Serpiente :estado="estado" :canvas="canvas" :repintar="repintar" :infoZonaJuego="infoZonaJuego" :comida="comida" :teclasPulsadas="teclasPulsadas" @posicionSerpiente="posicionSerpiente"/>
  <Comida :estado="estado" :canvas="canvas" :repintar="repintar" :infoZonaJuego="infoZonaJuego" :serpiente="serpiente" @posicionComida="posicionComida"/>
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
      canvas: null,
      refresco: 20,
      infoZonaJuego: {
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
      estado: '', // '' - Sin empezar, 'iniciando' - Cuenta atrás, 'jugando' - Jugando
      repintar: 1,
      comida: null,
      serpiente: [[11, 10], [25, 25]],
    };
  },
  methods: {
    posicionComida(nuevaPosicion) {
      this.comida = nuevaPosicion;
    },
    posicionSerpiente(nuevaPosicion) {
      this.serpiente = nuevaPosicion;
    },
    limpiar() {
      this.canvas.clearRect(0, 0, window.innerWidth, window.innerHeight);
    },
    iniciarJuego() {
      this.cambiarEstado('iniciando');
    },
    cambiarEstado(nuevoEstado) {
      this.estado = nuevoEstado;
    },
    cambiarDif(difX, difY) {
      this.infoZonaJuego.difX = difX;
      this.infoZonaJuego.difY = difY;
    },
    prepararCanvas() {
      var c = document.getElementById("canvas");
      var ctx = c.getContext("2d");    
      ctx.canvas.width  = window.innerWidth;
      ctx.canvas.height = window.innerHeight;
      this.canvas = ctx;
    },
    inicializar() {
      this.prepararCanvas();
      this.escucharTeclasPulsadas();
      setInterval(() => {
        this.limpiar();
        this.repintar *= -1;
      }, this.refresco);
    },
    escucharTeclasPulsadas() {
      window.addEventListener('keyup', (e) => {
        
        if (e.key === "ArrowUp") {
          this.teclasPulsadas.arriba = false;
        }
        else if (e.key === "ArrowRight") {
          this.teclasPulsadas.derecha = false;
        }
        else if (e.key === "ArrowDown") {
          this.teclasPulsadas.abajo = false;
        }
        else if (e.key === "ArrowLeft") {
          this.teclasPulsadas.izquierda = false;
        }
      });

      window.addEventListener('keydown', (e) => {
        if (e.key === "ArrowUp") {
          this.teclasPulsadas.arriba = true;
        }
        else if (e.key === "ArrowRight") {
          this.teclasPulsadas.derecha = true;
        }
        else if (e.key === "ArrowDown") {
          this.teclasPulsadas.abajo = true;
        }
        else if (e.key === "ArrowLeft") {
          this.teclasPulsadas.izquierda = true;
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
