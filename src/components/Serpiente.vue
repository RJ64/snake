<script>
export default {
  name: 'Serpiente',
  emits: ["posicionSerpiente"],
  props: {
    info: {
      required: true,
    },
  },
  data() {
    return {
      posicionesX: [11, 10],
      posicionesY: [25, 25],
      comidasPendientes: [],
      tiempoEntreMovimientos: 50,
    };
  },
  computed: {
    ctx() {
      return this.info.canvas;
    },
    estado() {
      return this.info.estado;
    },
    infoZonaJuego() {
      return this.info.zonaJuego;
    },
    teclasPulsadas() {
      return this.info.teclasPulsadas;
    },
    repintar() {
      return this.info.repintar;
    },
    comida() {
      return this.info.posiciones.comida;
    },
    longitud() {
      return this.posicionesX.length;
    },
    sentido() { // 0 arriba, 1 derecha, 2 abajo, 3 izquierda
      if (this.soloUnaTeclaPulsada()) {
        if (this.teclasPulsadas.arriba && this.sentido !== 2) {
          return 0;
        }
        if (this.teclasPulsadas.derecha && this.sentido !== 3) {
          return 1;
        }
        if (this.teclasPulsadas.abajo && this.sentido !== 0) {
          return 2;
        }
        if (this.teclasPulsadas.izquierda && this.sentido !== 1) {
          return 3;
        }
      }
      if (typeof this.sentido === 'undefined') {
        return 1;
      }
      return this.sentido;
    },
    posXPuntosSerpientePintado() {
      let array = [];
      for (let i = 0; i < this.longitud; i++) {
        let posXPunto = this.infoZonaJuego.difX + (this.posicionesX[i] * this.infoZonaJuego.anchoPixelSerpiente);
        array.push(posXPunto);
      }
      return array;
    },
    posYPuntosSerpientePintado() {
      let array = [];
      for (let i = 0; i < this.longitud; i++) {
        let posYPunto = this.infoZonaJuego.difY + (this.posicionesY[i] * this.infoZonaJuego.anchoPixelSerpiente);
        array.push(posYPunto);
      }
      return array;
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
    pintar() {
      this.ctx.beginPath();
      for (let i = 0; i < this.longitud; i++) {
        this.ctx.fillRect(this.posXPuntosSerpientePintado[i], this.posYPuntosSerpientePintado[i], this.infoZonaJuego.anchoPixelSerpiente, this.infoZonaJuego.anchoPixelSerpiente);
      }
      this.ctx.stroke();
    },
    movimiento() {

      let nuevaPosicion = this.calcularNuevaPosicion();
      let posicionesXTemporales = nuevaPosicion[0];
      let posicionesYTemporales = nuevaPosicion[1];

      if (this.choque(posicionesXTemporales, posicionesYTemporales)) {
console.log('FIN')
      }
      else {
        
        if (this.comida !== null && this.comida[0] === posicionesXTemporales[0] && this.comida[1] === posicionesYTemporales[0]) {
          this.comidasPendientes.push(this.comida);
        }

        this.posicionesX = posicionesXTemporales;
        this.posicionesY = posicionesYTemporales;

        this.$emit("posicionSerpiente", [this.posicionesX, this.posicionesY]);

        setTimeout(() => {
          this.movimiento();
        }, this.tiempoEntreMovimientos);

      }

    },
    calcularNuevaPosicion() {

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

      return [posicionesXTemporales, posicionesYTemporales];

    },
    choque(posicionesX, posicionesY) {

        let posXCabeza = posicionesX[0];
        let posYCabeza = posicionesY[0];

        // Pared izquierda
        if (posXCabeza < 0) {
          return true;
        }
        // Pared derecha
        if (posXCabeza >= (this.infoZonaJuego.anchoTablero / this.infoZonaJuego.anchoPixelSerpiente)) {
          return true;
        }
        // Pared superior
        if (posYCabeza < 0) {
          return true;
        }
        // Pared inferior
        if (posYCabeza >= (this.infoZonaJuego.altoTablero / this.infoZonaJuego.anchoPixelSerpiente)) {
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
    soloUnaTeclaPulsada() {
      return (this.teclasPulsadas.arriba && !this.teclasPulsadas.derecha && !this.teclasPulsadas.abajo && !this.teclasPulsadas.izquierda)
        || (!this.teclasPulsadas.arriba && this.teclasPulsadas.derecha && !this.teclasPulsadas.abajo && !this.teclasPulsadas.izquierda)
        || (!this.teclasPulsadas.arriba && !this.teclasPulsadas.derecha && this.teclasPulsadas.abajo && !this.teclasPulsadas.izquierda)
        || (!this.teclasPulsadas.arriba && !this.teclasPulsadas.derecha && !this.teclasPulsadas.abajo && this.teclasPulsadas.izquierda);
    },
  },
}
</script>