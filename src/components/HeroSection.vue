<script lang="ts">
import { defineComponent } from 'vue';
import gsap from 'gsap';

export default defineComponent({
  name: 'HeroSection',
  mounted() {
    this.initAppearAnimation();
    this.initHeroAnimation();
  },
  methods: {
    initAppearAnimation() {
      const hero = this.$refs.hero as HTMLElement;
      gsap.fromTo(hero,
        { autoAlpha: 0, y: '-5%' },
        {
          autoAlpha: 1,
          y: '0%',
          duration: 2,
          delay: 0.5,
          ease: 'power1.out'
        }
      );
    },
    initHeroAnimation() {
      const hero = this.$refs.hero as HTMLElement;
      const mask = this.$refs.mask as HTMLElement;
      const content = this.$refs.content as HTMLElement;
      const signature = this.$refs.signature as HTMLElement;
      const video = this.$refs.video as HTMLElement;

      const tl = gsap.timeline({
        scrollTrigger: {
          trigger: hero,
          start: 'top top',
          end: '+=150%',
          scrub: 1,
          pin: true,
          anticipatePin: 1,
        }
      });

      // Phase 1: Reveal & Bloom
      tl.to(signature, {
        scale: 1.05,
        duration: 0.5,
        ease: 'linear'
      })
        // Phase 2: The Portal Zoom
        .to(signature, {
          scale: 80,
          x: -200, // Offset to zoom "through" a specific letter
          duration: 2,
          ease: 'power2.in'
        }, 0.5)
        .to(video, {
          width: '100%',
          height: '100%',
          transform: 'translate(0%, 0%)',
          duration: 1.0,
          ease: 'power2.inOut'
        }, 1.3) // Ends at 2.5, matching the signature zoom-through finish
        .to(mask, {
          backgroundColor: 'rgba(250, 249, 246, 0)', // Seamlessly fade to transparent
          duration: 1,
          ease: 'none'
        }, 1)
        .to(content, {
          y: 100,
          opacity: 0,
          duration: 0.8
        }, 0.2);
    }
  }
});
</script>

<template>
  <section ref="hero" class="hero">
    <div class="video-background">
      <div class="video-overlay"></div>
      <video ref="video" autoplay muted loop playsinline class="bg-video">
        <source src="/assets/video/face1.mp4" type="video/mp4">
      </video>
    </div>

    <div ref="mask" class="text-mask-container">
      <div ref="signature" class="signature-wrapper">
        <span class="name">Aube</span>
        <span class="ampersand">et</span>
        <span class="name">Valentin</span>
      </div>
    </div>

    <div ref="content" class="hero-content">
      <p class="hero-subtitle">11 Avril 2026</p>
      <div class="center-indicator">
        <div class="center-line"></div>
        <span class="scroll-text-center">DÉROULEZ POUR DÉCOUVRIR</span>
      </div>
    </div>

    <div class="mouse-fixed">
      <div class="mouse">
        <div class="wheel"></div>
      </div>
      <div class="arrows">
        <span class="arrow-down"></span>
        <span class="arrow-down"></span>
      </div>
    </div>
  </section>
</template>

<style scoped>
.hero {
  position: relative;
  height: 100vh;
  width: 100%;
  overflow: hidden;
  background-color: var(--color-linen);
  opacity: 0;
  visibility: hidden;
}

.video-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
}

.bg-video {
  width: 50%;
  height: 50%;
  transform: translate(50%, 50%);
  object-fit: cover;
  filter: sepia(0.1) brightness(0.9) contrast(1.1);
  will-change: width, height, transform;
}

.video-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: radial-gradient(circle, transparent 0%, rgba(250, 249, 246, 0.2) 100%);
  z-index: 2;
}

.text-mask-container {
  position: relative;
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  pointer-events: none;
  background: var(--color-linen);
  mix-blend-mode: multiply;
  /* Reveals video through black text */
}

.signature-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 30px;
  color: #000;
  transform-origin: center center;
}

.name {
  font-family: var(--font-signature);
  font-size: 6vw;
  line-height: 1;
  white-space: nowrap;
}

.ampersand {
  font-family: var(--font-signature);
  font-size: 5vw;
  /* Note: mix-blend-mode: multiply will darken this */
  margin-top: 20px;
}

.hero-content {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  bottom: 3%;
  z-index: 20;
  text-align: center;
  color: var(--color-espresso);
}

.hero-subtitle {
  font-family: var(--font-sans);
  font-size: 11px;
  text-transform: uppercase;
  letter-spacing: 6px;
  margin-bottom: 24px;
  opacity: 0.8;
}

.center-indicator {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
  margin-top: 10px;
}

.center-line {
  width: 2px;
  height: 50px;
  background-color: var(--color-dusty-rose);
  animation: scrollLine 2.5s infinite var(--transition-smooth);
}

.scroll-text-center {
  font-family: var(--font-sans);
  font-size: 11px;
  letter-spacing: 4px;
  opacity: 0.6;
  font-weight: 500;
  text-transform: uppercase;
  color: var(--color-espresso);
}

.mouse-fixed {
  position: absolute;
  right: 30px;
  bottom: 50px;
  z-index: 30;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  opacity: 1;
}

.mouse {
  width: 30px;
  height: 44px;
  border: 1.5px solid var(--color-dusty-rose);
  border-radius: 10px;
  position: relative;
}

.wheel {
  width: 2px;
  height: 6px;
  background-color: var(--color-dusty-rose);
  position: absolute;
  top: 6px;
  left: 50%;
  transform: translateX(-50%);
  animation: scrollWheel 2s infinite ease-in-out;
}

.arrows {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.mouse-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
}

.mouse {
  width: 18px;
  height: 30px;
  border: 1.5px solid var(--color-dusty-rose);
  border-radius: 10px;
  position: relative;
}

.wheel {
  width: 2px;
  height: 5px;
  background-color: var(--color-dusty-rose);
  position: absolute;
  top: 5px;
  left: 50%;
  transform: translateX(-50%);
  animation: scrollWheel 2s infinite ease-in-out;
}

.arrows {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.arrow-down {
  display: block;
  width: 6px;
  height: 6px;
  border-bottom: 1.5px solid var(--color-dusty-rose);
  border-right: 1.5px solid var(--color-dusty-rose);
  transform: rotate(45deg);
  margin: -1px;
  animation: arrowFade 2s infinite;
}

.arrow-down:nth-child(2) {
  animation-delay: 0.2s;
}

.scroll-text {
  font-family: var(--font-sans);
  font-size: 12px;
  letter-spacing: 3px;
  opacity: 0.7;
  font-weight: 500;
  color: var(--color-espresso);
}

@keyframes scrollLine {
  0% {
    transform: scaleY(0);
    transform-origin: top;
  }

  50% {
    transform: scaleY(1);
    transform-origin: top;
  }

  51% {
    transform: scaleY(1);
    transform-origin: bottom;
  }

  100% {
    transform: scaleY(0);
    transform-origin: bottom;
  }
}

@keyframes scrollLine {
  0% {
    transform: scaleY(0);
    transform-origin: top;
  }

  50% {
    transform: scaleY(1);
    transform-origin: top;
  }

  51% {
    transform: scaleY(1);
    transform-origin: bottom;
  }

  100% {
    transform: scaleY(0);
    transform-origin: bottom;
  }
}

@keyframes scrollWheel {
  0% {
    transform: translate(-50%, 0);
    opacity: 0;
  }

  30% {
    opacity: 1;
  }

  100% {
    transform: translate(-50%, 10px);
    opacity: 0;
  }
}

@keyframes arrowFade {
  0% {
    opacity: 0;
    transform: rotate(45deg) translate(-2px, -2px);
  }

  50% {
    opacity: 1;
  }

  100% {
    opacity: 0;
    transform: rotate(45deg) translate(2px, 2px);
  }
}

@media (max-width: 768px) {
  .signature-wrapper {
    flex-direction: column;
    gap: 0;
  }

  .name {
    font-size: 22vw;
  }

  .ampersand {
    font-size: 15vw;
    margin: -10px 0;
  }
}
</style>
