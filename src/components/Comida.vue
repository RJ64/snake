<script>
export default {
  name: 'Comida',
  emits: ["posicionComida"],
  props: {
    info: {
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
      return this.info.canvas;
    },
    estado() {
      return this.info.estado;
    },
    infoZonaJuego() {
      return this.info.zonaJuego;
    },
    repintar() {
      return this.info.repintar;
    },
    serpiente() {
      return this.info.posiciones.serpiente;
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
      let posXComida = this.rand(0, (this.infoZonaJuego.anchoTablero / this.infoZonaJuego.anchoPixelSerpiente));
      let posXSerpiente = this.serpiente[0];
      if (posXSerpiente.includes(posXComida)) {
        return this.buscarHuecoLibreComidaX();
      }
      return posXComida;
    },
    buscarHuecoLibreComidaY() {
      let posYComida = this.rand(0, (this.infoZonaJuego.altoTablero / this.infoZonaJuego.anchoPixelSerpiente));
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
      let posXComida = this.infoZonaJuego.difX + (this.posicionComida[0] * this.infoZonaJuego.anchoPixelSerpiente);
      let posYComida = this.infoZonaJuego.difY + (this.posicionComida[1] * this.infoZonaJuego.anchoPixelSerpiente);
      this.ctx.fillRect(posXComida, posYComida, this.infoZonaJuego.anchoPixelSerpiente, this.infoZonaJuego.anchoPixelSerpiente);
      this.ctx.stroke();
    },
  },
}
</script>