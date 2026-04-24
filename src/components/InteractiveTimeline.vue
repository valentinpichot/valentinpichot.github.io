<script lang="ts">
import { defineComponent } from 'vue';
import gsap from 'gsap';

export default defineComponent({
  name: 'InteractiveTimeline',
  data() {
    return {
      activeMediaIndices: {} as Record<number, number>,
      autoplayIntervals: {} as Record<number, any>,
      events: [
        {
          time: '10H59',
          title: 'Le Oui',
          desc: 'Une promesse pour l’éternité.',
          icon: '💍',
          media: [
            { type: 'image', url: 'src/assets/bague1.jpg' },
            { type: 'image', url: 'src/assets/bague2.jpg' }
          ]
        },
        {
          time: '12:30',
          title: 'Arrivée au chateau',
          desc: 'La Auby et son Valou qui se baladent.',
          icon: '🥂',
          media: [{ type: 'video', url: 'src/assets/video/balade.mp4' }]
        },
        {
          time: '13:00',
          title: 'L\'apéro',
          desc: 'C\'est le moment de trinquer.',
          icon: '💐',
          media: [{ type: 'image', url: 'src/assets/apero1.jpg' }]
        },
        {
          time: '14:00',
          title: 'Le repas',
          desc: 'C\'est le moment de passer à table.',
          icon: '✨',
          media: [{ type: 'image', url: 'src/assets/repas6.jpg' }]
        },
        {
          time: 'Les familles',
          title: 'Merci',
          desc: 'Merci à tous d\'avoir participé à notre bonheur.',
          icon: '💖',
          media: [{ type: 'image', url: 'src/assets/fin1.jpg' }]
        },
      ]
    };
  },
  created() {
    this.events.forEach((_, index) => {
      this.activeMediaIndices[index] = 0;
    });
  },
  mounted() {
    this.initTimelineAnimation();
    this.startAutoplay();
  },
  beforeUnmount() {
    this.stopAutoplay();
  },
  methods: {
    initTimelineAnimation() {
      const items = this.$refs.timelineItem as HTMLElement[];
      const progress = this.$refs.progressLine as HTMLElement;
      const bgImage = this.$refs.bgImage as HTMLElement;

      if (bgImage) {
        gsap.to(bgImage, {
          yPercent: 30,
          ease: 'none',
          scrollTrigger: {
            trigger: '.timeline-container',
            start: 'top bottom',
            end: 'bottom top',
            scrub: true,
          }
        });
      }

      if (progress) {
        gsap.to(progress, {
          height: '100%',
          ease: 'none',
          scrollTrigger: {
            trigger: '.timeline-container',
            start: 'top center',
            end: 'bottom center',
            scrub: true,
          }
        });
      }

      if (items) {
        items.forEach((item) => {
          gsap.from(item, {
            opacity: 0,
            y: 50,
            scale: 0.9,
            duration: 1,
            scrollTrigger: {
              trigger: item,
              start: 'top 80%',
              end: 'top 50%',
              scrub: 1,
            }
          });
        });
      }
    },
    startAutoplay() {
      this.events.forEach((event, index) => {
        if (event.media.length > 1) {
          this.autoplayIntervals[index] = setInterval(() => {
            this.nextMedia(index);
          }, 3000);
        }
      });
    },
    stopAutoplay() {
      Object.values(this.autoplayIntervals).forEach(clearInterval);
    },
    nextMedia(eventIndex: number) {
      const mediaCount = this.events[eventIndex].media.length;
      this.activeMediaIndices[eventIndex] = (this.activeMediaIndices[eventIndex] + 1) % mediaCount;
    },
    prevMedia(eventIndex: number) {
      const mediaCount = this.events[eventIndex].media.length;
      this.activeMediaIndices[eventIndex] = (this.activeMediaIndices[eventIndex] - 1 + mediaCount) % mediaCount;
    }
  }
});
</script>

<template>
  <section class="timeline-container section-padding">
    <div class="background-parallax">
      <img src="../assets/inter-bg2.jpg" ref="bgImage" class="bg-image" alt="">
      <div class="bg-overlay"></div>
    </div>

    <div class="line-base">
      <div ref="progressLine" class="line-progress"></div>
    </div>

    <div class="container">
      <h2 class="font-serif timeline-title">Le Fil de l'Émotion</h2>

      <div class="timeline-items">
        <div v-for="(event, index) in events" :key="index" ref="timelineItem"
          :class="['timeline-item', index % 2 === 0 ? 'left' : 'right']">

          <div class="item-content">
            <span class="event-time font-sans">{{ event.time }}</span>
            <h3 class="font-serif">{{ event.title }}</h3>
            <p class="font-sans">{{ event.desc }}</p>
            <div class="event-icon">{{ event.icon }}</div>
          </div>

          <div class="event-frame" :class="{ landscape: index === events.length - 1 }"
            :style="{ transform: `rotate(${index % 2 === 0 ? '-3deg' : '3deg'})` }">
            <div class="media-stack">
              <transition-group name="fade">
                <div v-for="(item, mIndex) in event.media" :key="mIndex" v-show="activeMediaIndices[index] === mIndex"
                  class="media-item">
                  <img v-if="item.type === 'image'" :src="item.url" :alt="event.title" class="event-photo">
                  <video v-else-if="item.type === 'video'" autoplay muted loop playsinline class="event-photo">
                    <source :src="item.url" type="video/mp4">
                  </video>
                </div>
              </transition-group>
            </div>

            <!-- Navigation for multiple media -->
            <div v-if="event.media.length > 1" class="media-nav">
              <button @click.stop="prevMedia(index)" class="nav-btn prev" aria-label="Précédent">
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
                  <path d="M15 18l-6-6 6-6" stroke-linecap="round" stroke-linejoin="round" />
                </svg>
              </button>
              <button @click.stop="nextMedia(index)" class="nav-btn next" aria-label="Suivant">
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
                  <path d="M9 18l6-6-6-6" stroke-linecap="round" stroke-linejoin="round" />
                </svg>
              </button>
              <div class="nav-dots">
                <span v-for="(_, mIndex) in event.media" :key="mIndex"
                  :class="['dot', { active: activeMediaIndices[index] === mIndex }]"></span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.timeline-container {
  position: relative;
  background-color: var(--color-linen);
  padding-bottom: 150px;
  overflow: hidden;
}

.background-parallax {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  pointer-events: none;
}

.bg-image {
  width: 100%;
  height: 130%;
  object-fit: cover;
  filter: blur(4px) brightness(0.9);
  position: absolute;
  top: -15%;
  left: 0;
}

.bg-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(to bottom,
      rgba(234, 231, 223, 0.95) 0%,
      rgba(234, 231, 223, 0.7) 30%,
      rgba(234, 231, 223, 0.7) 70%,
      rgba(234, 231, 223, 0.95) 100%);
}

.timeline-title {
  text-align: center;
  font-size: 4rem;
  margin-bottom: 100px;
  color: var(--color-espresso);
  letter-spacing: -1px;
  position: relative;
  z-index: 5;
}

.line-base {
  position: absolute;
  left: 50%;
  top: 350px;
  bottom: 150px;
  width: 1px;
  background-color: rgba(60, 50, 42, 0.2);
  transform: translateX(-50%);
}

.line-progress {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 0;
  background-color: var(--color-espresso);
}

.timeline-items {
  position: relative;
  z-index: 2;
  display: flex;
  flex-direction: column;
  gap: 120px;
}

.timeline-item {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  gap: 60px;
}

.timeline-item.left {
  flex-direction: row;
}

.timeline-item.right {
  flex-direction: row-reverse;
}

.item-content {
  width: 400px;
  padding: 40px;
  background: white;
  border-radius: 2px;
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.03);
  position: relative;
  text-align: left;
}

.left .item-content {
  text-align: right;
}

.right .item-content {
  text-align: left;
}

.event-frame {
  width: 300px;
  height: 380px;
  padding: 15px;
  background: white;
  box-shadow: 0 15px 45px rgba(0, 0, 0, 0.1);
  transition: all 0.6s var(--transition-smooth);
  position: relative;
}

.event-frame.landscape {
  width: 500px;
  height: 350px;
}

.event-frame:hover {
  transform: rotate(0deg) scale(1.05) !important;
  z-index: 10;
}

.media-stack {
  position: relative;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.media-item {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  will-change: opacity, transform;
}

.event-photo {
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: sepia(0.1) contrast(1.05);
}

/* Media Navigation */
.media-nav {
  position: absolute;
  inset: 0;
  pointer-events: none;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 10px;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.event-frame:hover .media-nav {
  opacity: 1;
}

.nav-btn {
  pointer-events: auto;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.8);
  backdrop-filter: blur(4px);
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: var(--color-espresso);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  transition: all 0.2s ease;
}

.nav-btn:hover {
  background: white;
  transform: scale(1.1);
}

.nav-btn svg {
  width: 18px;
  height: 18px;
}

.nav-dots {
  position: absolute;
  bottom: 25px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 6px;
  pointer-events: none;
}

.dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.4);
  transition: all 0.3s ease;
}

.dot.active {
  background: white;
  transform: scale(1.2);
  box-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
}

/* Fade Transition - Cinematic & Smooth */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 2s var(--transition-smooth), transform 2s var(--transition-smooth);
}

.fade-enter-from {
  opacity: 0;
  transform: scale(1.08);
}

.fade-leave-to {
  opacity: 0;
  transform: scale(0.94);
}

.event-time {
  display: block;
  font-size: 1rem;
  color: var(--color-dusty-rose);
  font-weight: 700;
  margin-bottom: 15px;
  letter-spacing: 2px;
}

h3 {
  font-size: 2.2rem;
  margin-bottom: 20px;
  color: var(--color-espresso);
}

p {
  font-size: 1.1rem;
  line-height: 1.8;
  opacity: 0.6;
}

.event-icon {
  position: absolute;
  top: -25px;
  font-size: 2.5rem;
  background: white;
  width: 60px;
  height: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.05);
}

.left .event-icon {
  right: -30px;
}

.right .event-icon {
  left: -30px;
}

/* Dot on the line */
.timeline-item::after {
  content: '';
  position: absolute;
  left: 50%;
  top: 50%;
  width: 12px;
  height: 12px;
  background: var(--color-espresso);
  border-radius: 50%;
  transform: translate(-50%, -50%);
  z-index: 5;
  border: 4px solid var(--color-linen);
}

@media (max-width: 992px) {
  .timeline-item {
    flex-direction: column !important;
    gap: 40px;
  }

  .item-content {
    width: 100%;
    text-align: center !important;
  }

  .event-frame {
    width: 100%;
    max-width: 400px;
    height: auto;
    aspect-ratio: 4/5;
  }

  .event-frame.landscape {
    max-width: 600px;
    aspect-ratio: 16/10;
  }

  .line-base {
    display: none;
  }

  .timeline-item::after {
    display: none;
  }

  .event-icon {
    left: 50% !important;
    right: auto !important;
    transform: translateX(-50%);
  }

  .media-nav {
    opacity: 1;
    /* Always show on mobile */
  }
}
</style>
