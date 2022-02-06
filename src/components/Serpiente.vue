<template>
  <span></span>
</template>

<script>
export default {
  name: 'Serpiente',
  emits: ["posicionSerpiente"],
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
    difX: {
      type: Number,
      required: true,
    },
    difY: {
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
    comida: {
      required: true,
    },
  },
  data() {
    return {
      posicionesX: [11, 10],
      posicionesY: [25, 25],
      sentido: 1, // 0 arriba, 1 derecha, 2 abajo, 3 izquierda
      tiempoEntreMovimientos: 50,
      teclasPulsadas: {
        'arriba': false,
        'derecha': false,
        'abajo': false,
        'izquierda': false,
      },
      comidasPendientes: [],
    };
  },
  computed: {
    ctx() {
      return this.canvas;
    },
    longitud() {
      return this.posicionesX.length;
    },
  },
  watch: {
    estado: function(nuevoEstado) {
      if (nuevoEstado === 'jugando') {
        this.movimiento();
      }
    },
    repintar: function() {
      this.pintar();
    },
  },
  methods: {
    pintarPunto(posX, posY) {
      this.ctx.fillRect(this.difX + (posX * this.anchoPixelSerpiente), this.difY + (posY * this.anchoPixelSerpiente), this.anchoPixelSerpiente, this.anchoPixelSerpiente);
    },
    pintar() {
      this.ctx.beginPath();
      for (let i = 0; i < this.longitud; i++) {
        this.pintarPunto(this.posicionesX[i], this.posicionesY[i]);
      }
      this.ctx.stroke();
    },
    movimiento() {

      let posicionesXTemporales = JSON.parse(JSON.stringify(this.posicionesX));
      let posicionesYTemporales = JSON.parse(JSON.stringify(this.posicionesY));
      
      let movimiento = this.sentido === 1 || this.sentido === 2 ? 1 : -1;
      if (this.comidasPendientes.length > 0 
        && this.comidasPendientes[0][0] === posicionesXTemporales[posicionesXTemporales.length - 1] 
        && this.comidasPendientes[0][1] === posicionesYTemporales[posicionesYTemporales.length - 1]
      ) {
        this.comidasPendientes.shift();
      }
      else {
        posicionesXTemporales.pop();
        posicionesYTemporales.pop();
      }
      if (this.sentido % 2 !== 0) {
        posicionesXTemporales.unshift(posicionesXTemporales[0] + movimiento);
        posicionesYTemporales.unshift(posicionesYTemporales[0]);
      }
      else {
        posicionesXTemporales.unshift(posicionesXTemporales[0]);
        posicionesYTemporales.unshift(posicionesYTemporales[0] + movimiento);
      }

      if (this.choque(posicionesXTemporales, posicionesYTemporales)) {
console.log('FIN')
      }
      else {
        
        if (this.comida !== null && this.comida[0] === posicionesXTemporales[0] && this.comida[1] === posicionesYTemporales[0]) {
          this.comidasPendientes.push(this.comida);
        }

        this.posicionesX = posicionesXTemporales;
        this.posicionesY = posicionesYTemporales;
console.log(this.posicionesY[0])

        this.$emit("posicionSerpiente", [this.posicionesX, this.posicionesY]);

        setTimeout(() => {
          this.movimiento();
        }, this.tiempoEntreMovimientos);

      }

    },
    choque(posicionesX, posicionesY) {

        let posXCabeza = posicionesX[0];
        let posYCabeza = posicionesY[0];

        // Pared izquierda
        if (posXCabeza < 0) {
          return true;
        }
        // Pared derecha
        if (posXCabeza >= (this.anchoTablero / this.anchoPixelSerpiente)) {
          return true;
        }
        // Pared superior
        if (posYCabeza < 0) {
          return true;
        }
        // Pared inferior
        if (posYCabeza >= (this.altoTablero / this.anchoPixelSerpiente)) {
          return true;
        }

        // Sí mismo
        for (let i = 1; i < this.longitud; i++) {
          if (posXCabeza === posicionesX[i] && posYCabeza === posicionesY[i]) {
            return true;
          }
        }

        return false;

    },
    actualizarSentido() {
      if (this.soloUnaTeclaPulsada()) {
        if (this.teclasPulsadas.arriba && this.sentido !== 2) {
          this.sentido = 0;
        }
        else if (this.teclasPulsadas.derecha && this.sentido !== 3) {
          this.sentido = 1;
        }
        else if (this.teclasPulsadas.abajo && this.sentido !== 0) {
          this.sentido = 2;
        }
        else if (this.teclasPulsadas.izquierda && this.sentido !== 1) {
          this.sentido = 3;
        }
      }
    },
    soloUnaTeclaPulsada() {
      return (this.teclasPulsadas.arriba && !this.teclasPulsadas.derecha && !this.teclasPulsadas.abajo && !this.teclasPulsadas.izquierda)
        || (!this.teclasPulsadas.arriba && this.teclasPulsadas.derecha && !this.teclasPulsadas.abajo && !this.teclasPulsadas.izquierda)
        || (!this.teclasPulsadas.arriba && !this.teclasPulsadas.derecha && this.teclasPulsadas.abajo && !this.teclasPulsadas.izquierda)
        || (!this.teclasPulsadas.arriba && !this.teclasPulsadas.derecha && !this.teclasPulsadas.abajo && this.teclasPulsadas.izquierda);
    },
  },
  mounted() {

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
      this.actualizarSentido();
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
      this.actualizarSentido();
    });

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
