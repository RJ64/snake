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
    infoZonaJuego: {
      required: true,
    },
    comida: {
      required: true,
    },
    teclasPulsadas: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      posicionesX: [11, 10],
      posicionesY: [25, 25],
      sentido: 1, // 0 arriba, 1 derecha, 2 abajo, 3 izquierda
      tiempoEntreMovimientos: 50,
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
      let posXPunto = this.infoZonaJuego.difX + (posX * this.infoZonaJuego.anchoPixelSerpiente);
      let posYPunto = this.infoZonaJuego.difY + (posY * this.infoZonaJuego.anchoPixelSerpiente);
      this.ctx.fillRect(posXPunto, posYPunto, this.infoZonaJuego.anchoPixelSerpiente, this.infoZonaJuego.anchoPixelSerpiente);
    },
    pintar() {
      this.ctx.beginPath();
      for (let i = 0; i < this.longitud; i++) {
        this.pintarPunto(this.posicionesX[i], this.posicionesY[i]);
      }
      this.ctx.stroke();
    },
    movimiento() {

      this.actualizarSentido();

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
    actualizarSentido() {
      console.log('vfgdbv')
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
}
</script>