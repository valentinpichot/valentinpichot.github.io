<script lang="ts">
import { defineComponent } from 'vue';
import gsap from 'gsap';

export default defineComponent({
  name: 'VideoPortal',
  data() {
    return {
      isCinemaOpen: false,
      cinemaUrl: 'https://www.youtube.com/embed/x5AXm0apTjA?autoplay=1&rel=0&modestbranding=1',
      videos: [
        { title: `L'arrivée en voiture`, url: '/assets/voiture1.jpg' },
        { title: 'La descente', url: '/assets/arrivee2.jpg' },
        { title: 'La montée des marches', url: '/assets/val1.jpg' },
        { title: `L'arrivée à la mairie`, url: '/assets/auby1.jpg' },
        { title: 'Les sourires', url: '/assets/mairie3.jpg' },
        { title: 'Les bagues', url: '/assets/bague2.jpg' },
        { title: 'Auby et Valou', url: '/assets/mairie2.jpg' },
        { title: 'Les mariés au chateau', url: '/assets/marche1.jpg' },
      ]
    };
  },
  mounted() {
    this.initFilmStrip();
    this.initAnimations();
  },
  methods: {
    initFilmStrip() {
      const strip = this.$refs.strip as HTMLElement;
      if (strip) {
        gsap.to(strip, {
          xPercent: -60,
          ease: 'linear',
          scrollTrigger: {
            trigger: strip,
            start: 'top 80%',
            end: 'bottom 20%',
            scrub: 2,
          }
        });
      }
    },
    initAnimations() {
      const header = this.$refs.header as HTMLElement;
      const feature = this.$refs.feature as HTMLElement;

      gsap.from(header, {
        opacity: 0,
        y: 50,
        duration: 1,
        scrollTrigger: {
          trigger: header,
          start: 'top 80%',
        }
      });

      gsap.from(feature, {
        opacity: 0,
        scale: 0.95,
        duration: 1.5,
        ease: 'power3.out',
        scrollTrigger: {
          trigger: feature,
          start: 'top 75%',
        }
      });
    },
    openCinema() {
      this.isCinemaOpen = true;
      document.body.style.overflow = 'hidden';
    },
    closeCinema() {
      this.isCinemaOpen = false;
      document.body.style.overflow = 'auto';
    }
  }
});
</script>

<template>
  <section class="video-portal">
    <!-- Texture Overlay for "Film" feel -->
    <div class="grain-overlay"></div>

    <div ref="header" class="header">
      <h2 class="font-serif italic text-gold">Les vidéos</h2>
      <p class="font-sans subtitle">Des instants figés dans le temps</p>
    </div>

    <!-- Main Feature Segment (The "Grosse Vidéo") -->
    <div class="main-feature-container">
      <div ref="feature" class="main-feature" @click="openCinema">
        <div class="feature-bg">
          <video autoplay muted loop playsinline class="teaser-video">
            <source src="/assets/video/pre.mp4" type="video/mp4">
          </video>
          <div class="feature-overlay"></div>
        </div>

        <div class="feature-content">
          <span class="eyebrow">Maintenant au cinéma</span>
          <h3 class="feature-title font-serif">Le Film de notre Histoire</h3>
          <div class="play-trigger">
            <div class="play-circle">
              <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M8 5V19L19 12L8 5Z" fill="white" />
              </svg>
            </div>
            <span class="play-label">Visionner la vidéo complète</span>
          </div>
        </div>
      </div>
    </div>

    <div class="strip-container">
      <div ref="strip" class="film-strip">
        <div v-for="(video, index) in [...videos, ...videos]" :key="index" class="film-frame">
          <div class="video-container">
            <img :src="video.url" alt="" @mouseenter="($event.target as HTMLVideoElement).play()"
              @mouseleave="($event.target as HTMLVideoElement).pause()">
            <div class="frame-overlay">
              <span class="font-serif">{{ video.title }}</span>
            </div>
          </div>
          <div class="sprocket-holes">
            <span></span><span></span><span></span><span></span>
          </div>
        </div>
      </div>
    </div>

    <!-- Video Modal -->
    <Teleport to="body">
      <Transition name="cinema">
        <div v-if="isCinemaOpen" class="cinema-modal" @click="closeCinema">
          <div class="modal-content" @click.stop>
            <button class="close-btn" @click="closeCinema">
              <span class="close-icon">&times;</span>
              <span class="close-text">FERMER</span>
            </button>
            <div class="video-wrapper">
              <iframe :src="cinemaUrl" frameborder="0"
                allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
                allowfullscreen>
              </iframe>
            </div>
          </div>
        </div>
      </Transition>
    </Teleport>
  </section>
</template>

<style scoped>
.video-portal {
  position: relative;
  min-height: 100vh;
  background-color: #0d0c0a;
  /* Darker, richer black */
  color: var(--color-linen);
  padding: 100px 0;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.grain-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: url('https://www.transparenttextures.com/patterns/carbon-fibre.png');
  opacity: 0.03;
  pointer-events: none;
  z-index: 5;
}

.header {
  text-align: center;
  margin-bottom: 80px;
  position: relative;
  z-index: 10;
}

h2 {
  font-size: clamp(2.5rem, 6vw, 4.5rem);
  letter-spacing: -1px;
  margin-bottom: 15px;
}

.subtitle {
  font-size: 0.9rem;
  letter-spacing: 5px;
  text-transform: uppercase;
  opacity: 0.6;
}

/* Main Feature Styling */
.main-feature-container {
  max-width: 1200px;
  width: 90%;
  margin: 0 auto 100px;
  position: relative;
  z-index: 10;
}

.main-feature {
  position: relative;
  width: 100%;
  border-radius: 4px;
  overflow: hidden;
  aspect-ratio: 21/9;
  cursor: pointer;
  box-shadow: 0 30px 60px rgba(0, 0, 0, 0.5);
  transition: transform 0.6s var(--transition-smooth);
}

.main-feature:hover {
  transform: scale(1.02);
}

.feature-bg {
  position: absolute;
  inset: 0;
}

.teaser-video {
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: brightness(0.6) contrast(1.1);
  transition: transform 1.2s ease;
}

.main-feature:hover .teaser-video {
  transform: scale(1.1);
}

.feature-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(0deg, rgba(13, 12, 10, 0.9) 0%, rgba(13, 12, 10, 0) 60%);
}

.feature-content {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  padding: 50px;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.eyebrow {
  font-family: var(--font-sans);
  font-size: 11px;
  letter-spacing: 4px;
  text-transform: uppercase;
  color: var(--color-dusty-rose);
  margin-bottom: 20px;
}

.feature-title {
  font-size: clamp(1.8rem, 4vw, 3.5rem);
  margin-bottom: 30px;
}

.play-trigger {
  display: flex;
  align-items: center;
  gap: 20px;
  transition: all 0.3s ease;
}

.play-circle {
  width: 60px;
  height: 60px;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.4s var(--transition-smooth);
}

.play-circle svg {
  width: 24px;
  height: 24px;
  margin-left: 4px;
}

.play-label {
  font-family: var(--font-sans);
  font-size: 10px;
  letter-spacing: 3px;
  text-transform: uppercase;
  opacity: 0.8;
}

.main-feature:hover .play-circle {
  background: var(--color-linen);
  transform: scale(1.1);
}

.main-feature:hover .play-circle svg path {
  fill: #0d0c0a;
}

/* Film Strip Styling */
.strip-container {
  width: 100%;
  overflow: hidden;
  margin-top: 40px;
  padding: 20px 0;
}

.film-strip {
  display: flex;
  gap: 30px;
  padding: 0 100px;
  width: max-content;
}

.film-frame {
  flex-shrink: 0;
  width: 400px;
  background: #1a1917;
  padding: 45px 12px;
  position: relative;
  transition: transform 0.4s ease;
}

.film-frame:hover {
  transform: translateY(-10px);
  z-index: 20;
}

.video-container {
  position: relative;
  width: 100%;
  aspect-ratio: 4/5;
  /* Portrait look for variance */
  overflow: hidden;
}

.film-frame img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: contrast(1.2) brightness(0.8);
  transition: all 0.6s ease;
}

.film-frame:hover img {
  filter: contrast(1) brightness(1);
  transform: scale(1.05);
}

.frame-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(0deg, rgba(0, 0, 0, 0.6) 0%, transparent 100%);
  display: flex;
  align-items: flex-end;
  padding: 20px;
  opacity: 0;
  transition: opacity 0.4s ease;
}

.film-frame:hover .frame-overlay {
  opacity: 1;
}

.sprocket-holes {
  display: flex;
  justify-content: space-around;
  padding-top: 25px;
}

.sprocket-holes span {
  width: 12px;
  height: 16px;
  background: #0d0c0a;
  border-radius: 2px;
}

/* Cinema Modal */
.cinema-modal {
  position: fixed;
  inset: 0;
  z-index: 1000;
  background: rgba(13, 12, 10, 0.98);
  backdrop-filter: blur(20px);
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-content {
  width: 90%;
  max-width: 1400px;
  aspect-ratio: 16/9;
  position: relative;
}

.video-wrapper {
  width: 100%;
  height: 100%;
  background: #000;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.video-wrapper iframe {
  width: 100%;
  height: 100%;
}

.close-btn {
  position: absolute;
  top: -60px;
  right: 0;
  background: transparent;
  border: none;
  color: white;
  display: flex;
  align-items: center;
  gap: 15px;
  cursor: pointer;
}

.close-icon {
  font-size: 32px;
  line-height: 1;
}

.close-text {
  font-size: 10px;
  letter-spacing: 4px;
  opacity: 0.6;
}

/* Transitions */
.cinema-enter-active,
.cinema-leave-active {
  transition: opacity 0.6s ease;
}

.cinema-enter-from,
.cinema-leave-to {
  opacity: 0;
}

@media (max-width: 1024px) {
  .main-feature {
    aspect-ratio: 16/9;
  }
}

@media (max-width: 768px) {
  .main-feature {
    aspect-ratio: 1;
  }

  .feature-content {
    padding: 30px;
  }

  .film-frame {
    width: 280px;
  }

  .video-portal {
    padding: 60px 0;
  }
}
</style>
