<script lang="ts">
import { defineComponent } from 'vue';
import gsap from 'gsap';

const GALLERY_CODE = 'oZS0yTBr';
const GALLERY_URL = 'https://clarissephotographe.pixieset.com/aubeandvalentin/';

export default defineComponent({
  name: 'PhotoPortal',
  data() {
    return {
      images: [
        { url: '/assets/repas2.jpg', x: -20, y: -20, scale: 1.2 },
        { url: '/assets/repas3.jpg', x: 20, y: -10, scale: 0.8 },
        { url: '/assets/repas1.jpg', x: -10, y: 20, scale: 1.1 },
        { url: '/assets/repas4.jpg', x: 15, y: 15, scale: 0.9 },
      ],
      toastVisible: false,
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
    async openGallery() {
      // Copy access code to clipboard silently
      try {
        await navigator.clipboard.writeText(GALLERY_CODE);
      } catch {
        // fallback
      }

      // Show toast
      this.toastVisible = true;

      // After 1.8s, hide toast and launch morph animation
      setTimeout(() => {
        this.toastVisible = false;
        const overlay = this.$refs.morphOverlay as HTMLElement;
        gsap.to(overlay, {
          scale: 100,
          opacity: 1,
          duration: 1.5,
          ease: 'power4.inOut',
          onComplete: () => {
            // Attempt to pass password in URL + copy fallback
            const urlWithPass = `${GALLERY_URL}?password=${GALLERY_CODE}`;
            window.open(urlWithPass, '_blank');
            gsap.set(overlay, { scale: 0, opacity: 0 });
          }
        });
      }, 1800);
    },
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
        Cliquer ici pour toutes les photos
      </button>
      <p class="cta-hint font-sans">(Le code d'accès sera automatiquement copié)</p>
    </div>

    <!-- Clipboard toast -->
    <Transition name="toast">
      <div v-if="toastVisible" class="clipboard-toast font-sans">
        <span class="toast-icon">📋</span>
        Code copié · collez-le sur la page suivante
      </div>
    </Transition>
  </section>
</template>

<style scoped>
.photo-portal {
  position: relative;
  height: 90vh;
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

.cta-hint {
  margin-top: 15px;
  font-size: 0.8rem !important;
  opacity: 0.4 !important;
  letter-spacing: 1px;
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

/* ── Toast ── */
.clipboard-toast {
  position: fixed;
  bottom: 36px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 999;
  background: var(--color-espresso);
  color: var(--color-linen);
  padding: 14px 28px;
  border-radius: 100px;
  font-size: 0.9rem;
  letter-spacing: 1px;
  display: flex;
  align-items: center;
  gap: 10px;
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.2);
  white-space: nowrap;
}

.toast-icon {
  font-size: 1.1rem;
}

.toast-enter-active {
  transition: opacity 0.35s ease, transform 0.35s ease;
}

.toast-leave-active {
  transition: opacity 0.25s ease, transform 0.25s ease;
}

.toast-enter-from {
  opacity: 0;
  transform: translateX(-50%) translateY(16px);
}

.toast-leave-to {
  opacity: 0;
  transform: translateX(-50%) translateY(16px);
}

@media (max-width: 768px) {
  h2 {
    font-size: 2.5rem;
  }

  .floating-img {
    width: 150px;
    height: 200px;
  }

  .clipboard-toast {
    font-size: 0.8rem;
    padding: 12px 20px;
  }
}
</style>
