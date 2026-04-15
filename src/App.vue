<script lang="ts">
import { defineComponent } from 'vue';
import Lenis from 'lenis';
import gsap from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

import CustomCursor from './components/CustomCursor.vue';
import HeroSection from './components/HeroSection.vue';
import PhotoPortal from './components/PhotoPortal.vue';
import VideoPortal from './components/VideoPortal.vue';
import InteractiveTimeline from './components/InteractiveTimeline.vue';

gsap.registerPlugin(ScrollTrigger);

export default defineComponent({
  name: 'App',
  components: {
    CustomCursor,
    HeroSection,
    PhotoPortal,
    VideoPortal,
    InteractiveTimeline,
  },
  data() {
    return {
      lenis: null as Lenis | null,
    };
  },
  mounted() {
    this.initLenis();
    this.initScrollTriggerSync();
  },
  beforeUnmount() {
    if (this.lenis) {
      this.lenis.destroy();
    }
  },
  methods: {
    initLenis() {
      this.lenis = new Lenis({
        duration: 1.5, // Dream-like slower feel
        easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
        orientation: 'vertical',
        gestureOrientation: 'vertical',
        smoothWheel: true,
        wheelMultiplier: 0.9, // More precise and cinematic
        touchMultiplier: 2,
        infinite: false,
      });

      const requestAniFrame = (time: number) => {
        this.lenis?.raf(time);
        requestAnimationFrame(requestAniFrame);
      };

      requestAnimationFrame(requestAniFrame);
    },
    initScrollTriggerSync() {
      if (this.lenis) {
        this.lenis.on('scroll', ScrollTrigger.update);
        gsap.ticker.add((time) => {
          this.lenis?.raf(time * 1000);
        });
        gsap.ticker.lagSmoothing(0);
      }
    },
  },
});
</script>

<template>
  <div id="app-container">
    <div id="grain"></div>
    <CustomCursor />
    
    <main>
      <HeroSection />
      
      <div class="portals-container">
        <PhotoPortal />
        <VideoPortal />
      </div>

      <InteractiveTimeline />
      
      <section class="footer-spacer section-padding">
        <div class="container">
          <p class="font-serif" style="text-align: center; opacity: 0.5;">Fait avec amour pour un jour inoubliable.</p>
        </div>
      </section>
    </main>
  </div>
</template>

<style scoped>
#app-container {
  position: relative;
  min-height: 100vh;
}

.portals-container {
  display: flex;
  flex-direction: column;
  gap: 0;
}

main {
  position: relative;
  z-index: 1;
}
</style>
