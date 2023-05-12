<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="10">
      <v-card>
        <v-card-title class="headline">
          Welcome to the Vuetify + Nuxt.js template
        </v-card-title>
        <v-card-text>
          <div class="img-container" ref="imgContainer">
    <img src="../static/MapPokemon.png" alt="Description de l'image" class="img-responsive" ref="image">
  </div>
        </v-card-text>
        
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import imagebg from "../static/MapPokemon.png"
import ResizeObserver from 'resize-observer-polyfill';
//          <v-img cols="1" sm="1" md="1" class="sprite" :style="{ backgroundPosition: spritePosition }"/>

export default {
  name: 'IndexPage',
  data() {
    return {
      spritePosition: '0px 0px',
      animationInterval: null,
      curentPosition: {
        x: 0,
        y: 0,
      },
      keyDownEvents: [],
    };
  },
  props: {
    imagebg: imagebg,
  },
  methods: {

    startAnimation() {
      this.animationInterval = setInterval(() => {
        this.move();
      }, 100);
    },
    stopAnimation() {
      clearInterval(this.animationInterval);
      this.animationInterval = null;
    },

    move() {
      switch (this.keyDownEvents[0]) {
        case 'ArrowUp':
          this.curentPosition.y += 10;
          break;
        case 'ArrowDown':
          this.curentPosition.y -= 10;
          break;
        case 'ArrowLeft':
          this.curentPosition.x += 10;
          break;
        case 'ArrowRight':
          this.curentPosition.x -= 10;
          break;
      }
      this.spritePosition = `${this.curentPosition.x}px ${this.curentPosition.y}px`;
    
  }
  },
  mounted() {
    const ro = new ResizeObserver(entries => {
      for (const entry of entries) {
        // Calcule les dimensions de l'image en fonction de la taille du conteneur
        const containerWidth = entry.contentRect.width;
        const containerHeight = entry.contentRect.height;
        const containerRatio = containerWidth / containerHeight;
        const imageRatio = this.$refs.image.naturalWidth / this.$refs.image.naturalHeight;
        let imageWidth, imageHeight;
        if (containerRatio > imageRatio) {
          imageWidth = containerHeight * imageRatio;
          imageHeight = containerHeight;
        } else {
          imageWidth = containerWidth;
          imageHeight = containerWidth / imageRatio;
        }
        // Calcule la position de l'image pour qu'elle reste centrée sur le centre de l'image affichée à l'écran
        const containerLeft = this.$refs.imgContainer.getBoundingClientRect().left;
        const containerTop = this.$refs.imgContainer.getBoundingClientRect().top;
        const containerCenterX = containerLeft + containerWidth / 2;
        const containerCenterY = containerTop + containerHeight / 2;
        const imageLeft = containerCenterX - imageWidth / 2;
        const imageTop = containerCenterY - imageHeight / 2;
        // Applique les dimensions et la position calculées à l'image
        this.$refs.image.style.width = `${imageWidth}px`;
        this.$refs.image.style.height = `${imageHeight}px`;
        this.$refs.image.style.left = `${imageLeft}px`;
        this.$refs.image.style.top = `${imageTop}px`;
      }
    });
    ro.observe(this.$refs.imgContainer);



    window.addEventListener('keyup', (event) => {
      this.keyDownEvents = [];
      const index = this.keyDownEvents.indexOf(event.key);
      if (index !== -1) {
        this.keyDownEvents.splice(index, 1);
        this.keyDownEvents = [];
      }
      // Si toutes les touches sont relâchées, arrêter l'animation
      if (this.keyDownEvents.length === 0) {
        this.stopAnimation();
      }
    });
    window.addEventListener('keydown', (event) => {
      if (!this.keyDownEvents.includes(event.key)) {
        this.keyDownEvents.push(event.key);
      }
      // Si l'animation n'est pas en cours, la démarrer
      if (!this.animationInterval) {
        this.startAnimation();
      }
    });
  },
}
</script>

<style scoped>
.sprite {
  max-width: 100hv;
  max-height: 100hv;
  min-width: 50vh;
  min-height: 50vh;
  transform: scale(2,2);
  transform-origin: center 25%;
  object-position: right bottom;
  background-image: url(../static/MapPokemon.png);
  background-repeat: no-repeat;
}
</style>