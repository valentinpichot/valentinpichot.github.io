<script lang="ts">
import { defineComponent } from 'vue';
import gsap from 'gsap';

import imgMerci from '@/assets/last3.webp';

export default defineComponent({
  name: 'ThankYouSection',
  data() {
    return {
      imgMerci,
    };
  },
  mounted() {
    this.initAnimation();
  },
  methods: {
    initAnimation() {
      gsap.from(this.$refs.content as HTMLElement, {
        opacity: 0,
        y: 50,
        duration: 1.5,
        ease: 'power3.out',
        scrollTrigger: {
          trigger: '.thank-you-container',
          start: 'top 60%',
        }
      });

      gsap.to(this.$refs.bg as HTMLElement, {
        y: '20%',
        ease: 'none',
        scrollTrigger: {
          trigger: '.thank-you-container',
          start: 'top bottom',
          end: 'bottom top',
          scrub: true
        }
      });
    }
  }
});
</script>

<template>
  <section class="thank-you-container">
    <div ref="bg" class="thank-you-bg">
      <img :src="imgMerci" alt="Merci">
      <div class="overlay"></div>
    </div>
    
    <div ref="content" class="thank-you-content">
      <h2 class="font-serif">Merci</h2>
      <p class="font-serif message">
        À vous tous qui avez partagé ce moment inoubliable à nos côtés.<br>
        Votre présence, vos sourires et votre amour ont rendu cette journée magique.
      </p>
      <div class="initials font-serif">Aube & Valentin</div>
    </div>
  </section>
</template>

<style scoped>
.thank-you-container {
  position: relative;
  height: 100vh;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  background-color: var(--color-espresso);
}

.thank-you-bg {
  position: absolute;
  top: -20%;
  left: 0;
  width: 100%;
  height: 140%;
  z-index: 1;
}

.thank-you-bg img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: brightness(0.6) sepia(0.2);
}

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(to bottom, rgba(30, 25, 21, 0.4), rgba(30, 25, 21, 0.8));
}

.thank-you-content {
  position: relative;
  z-index: 10;
  text-align: center;
  color: var(--color-linen);
  max-width: 800px;
  padding: 40px;
}

h2 {
    font-family: var(--font-signature);
  font-size: 8rem;
  margin-bottom: 30px;
}

.message {
  font-size: 1.5rem;
  line-height: 1.6;
  opacity: 0.9;
  margin-bottom: 60px;
}

.initials {
  font-family: var(--font-signature);
  font-size: 2rem;
  opacity: 1;
  border-top: 1px solid rgba(250, 249, 246, 0.3);
  display: inline-block;
  padding-top: 20px;
}

@media (max-width: 768px) {
  h2 { font-size: 4rem; }
  .message { font-size: 1.2rem; }
  .thank-you-container { height: 80vh; }
}
</style>
