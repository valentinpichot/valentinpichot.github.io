<script lang="ts">
import { defineComponent } from 'vue';
import gsap from 'gsap';

export default defineComponent({
  name: 'PhotoPortal',
  data() {
    return {
      images: [
        { url: 'src/assets/repas2.jpg', x: -20, y: -20, scale: 1.2 },
        { url: 'src/assets/repas3.jpg', x: 20, y: -10, scale: 0.8 },
        { url: 'src/assets/repas1.jpg', x: -10, y: 20, scale: 1.1 },
        { url: 'src/assets/repas4.jpg', x: 15, y: 15, scale: 0.9 },
      ],
    };
  },
  mounted() {
    window.addEventListener('mousemove', this.handleParallax);
  },
  beforeUnmount() {
    window.removeEventListener('mousemove', this.handleParallax);
  },
  methods: {
    handleParallax(e: MouseEvent) {
      const { clientX, clientY } = e;
      const xPos = (clientX / window.innerWidth) - 0.5;
      const yPos = (clientY / window.innerHeight) - 0.5;

      const items = this.$refs.items as HTMLElement[];
      if (items) {
        items.forEach((item, index) => {
          const depth = (index + 1) * 20;
          gsap.to(item, {
            x: xPos * depth,
            y: yPos * depth,
            duration: 1,
            ease: 'power2.out',
          });
        });
      }
    },
    openGallery() {
      // Morphing effect simulation
      const overlay = this.$refs.morphOverlay as HTMLElement;

      gsap.to(overlay, {
        scale: 100,
        opacity: 1,
        duration: 1.5,
        ease: 'power4.inOut',
        onComplete: () => {
          window.open('https://drive.google.com', '_blank');
          gsap.set(overlay, { scale: 0, opacity: 0 });
        }
      });
    }
  }
});
</script>

<template>
  <section ref="portal" class="photo-portal">
    <div ref="morphOverlay" class="morph-overlay"></div>

    <div class="portal-background">
      <div v-for="(img, index) in images" :key="index" ref="items" class="floating-img" :style="{
        top: 50 + img.y + '%',
        left: 50 + img.x + '%',
        transform: `scale(${img.scale})`
      }">
        <img :src="img.url" alt="Mariage Moment">
      </div>
    </div>

    <div class="portal-content">
      <h2 class="font-serif">Les photos</h2>
      <p class="font-sans">L'éternité capturée à travers l'objectif.</p>
      <button class="portal-btn" @click="openGallery">
        Cliquez ici pour toutes les photos
      </button>
    </div>
  </section>
</template>

<style scoped>
.photo-portal {
  position: relative;
  height: 80vh;
  width: 100%;
  background-color: var(--color-linen);
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.portal-background {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  pointer-events: none;
}

.floating-img {
  position: absolute;
  width: 300px;
  height: 400px;
  overflow: hidden;
  box-shadow: 0 30px 60px rgba(0, 0, 0, 0.1);
  border: 10px solid white;
}

.floating-img img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: sepia(0.2) contrast(1.1);
}

.portal-content {
  position: relative;
  z-index: 10;
  text-align: center;
  max-width: 600px;
  padding: 40px;
  background: rgba(250, 249, 246, 0.8);
  backdrop-filter: blur(10px);
  border-radius: 2px;
}

h2 {
  font-size: 4rem;
  margin-bottom: 20px;
  color: var(--color-espresso);
}

p {
  font-size: 1.2rem;
  margin-bottom: 40px;
  letter-spacing: 1px;
  opacity: 0.8;
}

.portal-btn {
  background: var(--color-espresso);
  color: var(--color-linen);
  border: none;
  padding: 15px 40px;
  font-family: var(--font-sans);
  text-transform: uppercase;
  letter-spacing: 2px;
  cursor: pointer;
  transition: all 0.3s var(--transition-smooth);
}

.portal-btn:hover {
  background: var(--color-dusty-rose);
  transform: translateY(-5px);
}

.morph-overlay {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 10px;
  height: 10px;
  background: var(--color-sable);
  border-radius: 50%;
  transform: translate(-50%, -50%) scale(0);
  z-index: 100;
  pointer-events: none;
  opacity: 0;
}

@media (max-width: 768px) {
  h2 {
    font-size: 2.5rem;
  }

  .floating-img {
    width: 150px;
    height: 200px;
  }
}
</style>
