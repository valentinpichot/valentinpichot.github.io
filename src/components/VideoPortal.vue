<script lang="ts">
import { defineComponent } from 'vue';
import gsap from 'gsap';

export default defineComponent({
  name: 'VideoPortal',
  data() {
    return {
      videos: [
        { title: 'La Préparation', url: 'https://cdn.pixabay.com/video/2024/03/20/204803-925552205_large.mp4' },
        { title: 'La Cérémonie', url: 'https://cdn.pixabay.com/video/2022/10/23/136139-764371523_large.mp4' },
        { title: 'Le Baiser', url: 'https://cdn.pixabay.com/video/2019/11/22/29338-374868363_large.mp4' },
        { title: 'La Fête', url: 'https://cdn.pixabay.com/video/2024/03/20/204803-925552205_large.mp4' },
      ]
    };
  },
  mounted() {
    this.initFilmStrip();
  },
  methods: {
    initFilmStrip() {
      const strip = this.$refs.strip as HTMLElement;
      if (strip) {
        gsap.to(strip, {
          xPercent: -50,
          ease: 'none',
          scrollTrigger: {
            trigger: strip,
            start: 'top center',
            end: 'bottom top',
            scrub: 1,
          }
        });
      }
    },
    openCinema() {
      window.open('https://drive.google.com', '_blank');
    }
  }
});
</script>

<template>
  <section class="video-portal">
    <div class="header">
      <h2 class="font-serif">Le Portail du Mouvement</h2>
      <p class="font-sans">L'histoire en mouvement.</p>
    </div>

    <div class="strip-container">
      <div ref="strip" class="film-strip">
        <div v-for="(video, index) in [...videos, ...videos]" :key="index" class="film-frame">
          <div class="video-container">
            <video muted loop onmouseover="this.play()" onmouseout="this.pause()" playsinline>
              <source :src="video.url" type="video/mp4">
            </video>
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

    <div class="footer">
      <button class="portal-btn" @click="openCinema">
        Le film de notre histoire (Cinéma)
      </button>
    </div>
  </section>
</template>

<style scoped>
.video-portal {
  position: relative;
  min-height: 70vh;
  background-color: var(--color-espresso);
  color: var(--color-linen);
  padding: 60px 0;
  display: flex;
  flex-direction: column;
  justify-content: center;
  overflow: hidden;
}

.header {
  text-align: center;
  margin-bottom: 60px;
}

h2 {
  font-size: 3.5rem;
  margin-bottom: 10px;
}

p {
  font-size: 1.1rem;
  opacity: 0.6;
  letter-spacing: 2px;
  text-transform: uppercase;
}

.strip-container {
  width: 100%;
  overflow: hidden;
  margin: 40px 0;
}

.film-strip {
  display: flex;
  gap: 20px;
  padding: 0 50px;
  width: max-content;
}

.film-frame {
  flex-shrink: 0;
  width: 450px;
  background: #111;
  padding: 40px 10px;
  position: relative;
}

.video-container {
  position: relative;
  width: 100%;
  aspect-ratio: 16/9;
  overflow: hidden;
}

video {
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: grayscale(0.5) contrast(1.2);
  transition: filter 0.5s ease;
}

.film-frame:hover video {
  filter: grayscale(0) contrast(1);
}

.frame-overlay {
  position: absolute;
  bottom: 10px;
  left: 10px;
  padding: 5px 15px;
  background: rgba(0,0,0,0.5);
  backdrop-filter: blur(5px);
}

.sprocket-holes {
  display: flex;
  justify-content: space-around;
  padding-top: 20px;
}

.sprocket-holes span {
  width: 15px;
  height: 20px;
  background: var(--color-espresso);
  border-radius: 4px;
}

.footer {
  text-align: center;
  margin-top: 60px;
}

.portal-btn {
  background: transparent;
  color: var(--color-linen);
  border: 1px solid var(--color-dusty-rose);
  padding: 15px 40px;
  font-family: var(--font-sans);
  text-transform: uppercase;
  letter-spacing: 2px;
  cursor: pointer;
  transition: all 0.3s var(--transition-smooth);
}

.portal-btn:hover {
  background: var(--color-dusty-rose);
  color: var(--color-espresso);
}

@media (max-width: 768px) {
  .film-frame { width: 280px; }
  h2 { font-size: 2rem; }
}
</style>
