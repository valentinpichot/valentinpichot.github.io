<script lang="ts">
import { defineComponent } from 'vue';
import gsap from 'gsap';

export default defineComponent({
  name: 'InteractiveTimeline',
  data() {
    return {
      events: [
        { time: '14:00', title: 'Le Oui', desc: 'Une promesse pour l’éternité.', icon: '💍', x: -100 },
        { time: '16:00', title: 'Le Cocktail', desc: 'Des rires et des verres qui trinquent.', icon: '🥂', x: 100 },
        { time: '18:00', title: 'Le Bouquet', desc: 'Le vol gracieux d’un espoir fleuri.', icon: '💐', x: -100 },
        { time: '20:00', title: 'Le Dîner', desc: 'Partage et saveurs sous les étoiles.', icon: '✨', x: 100 },
        { time: '22:00', title: 'Le Premier Pas', desc: 'Une danse au rythme du cœur.', icon: '💃', x: -100 },
      ]
    };
  },
  mounted() {
    this.initTimelineAnimation();
  },
  methods: {
    initTimelineAnimation() {
      const items = this.$refs.timelineItem as HTMLElement[];
      const progress = this.$refs.progressLine as HTMLElement;

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
  }
});
</script>

<template>
  <section class="timeline-container section-padding">
    <div class="line-base">
      <div ref="progressLine" class="line-progress"></div>
    </div>

    <div class="container">
      <h2 class="font-serif timeline-title">Le Fil de l'Émotion</h2>
      
      <div class="timeline-items">
        <div v-for="(event, index) in events" 
             :key="index" 
             ref="timelineItem"
             :class="['timeline-item', index % 2 === 0 ? 'left' : 'right']">
          <div class="item-content">
            <span class="event-time font-sans">{{ event.time }}</span>
            <h3 class="font-serif">{{ event.title }}</h3>
            <p class="font-sans">{{ event.desc }}</p>
            <div class="event-icon">{{ event.icon }}</div>
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
  padding-bottom: 100px;
}

.timeline-title {
  text-align: center;
  font-size: 3rem;
  margin-bottom: 60px;
  color: var(--color-espresso);
}

.line-base {
  position: absolute;
  left: 50%;
  top: 300px;
  bottom: 100px;
  width: 2px;
  background-color: var(--color-sable);
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
  gap: 80px;
}

.timeline-item {
  width: 45%;
  position: relative;
}

.timeline-item.left { align-self: flex-start; text-align: right; }
.timeline-item.right { align-self: flex-end; text-align: left; }

.item-content {
  padding: 30px;
  background: white;
  border-radius: 4px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.05);
  position: relative;
}

.event-time {
  display: block;
  font-size: 0.9rem;
  color: var(--color-dusty-rose);
  font-weight: 700;
  margin-bottom: 10px;
}

h3 {
  font-size: 1.8rem;
  margin-bottom: 15px;
}

p {
  font-size: 1rem;
  line-height: 1.6;
  opacity: 0.7;
}

.event-icon {
  position: absolute;
  top: 50%;
  font-size: 2rem;
  transform: translateY(-50%);
}

.left .event-icon { right: -80px; }
.right .event-icon { left: -80px; }

/* Dot on the line */
.timeline-item::after {
  content: '';
  position: absolute;
  top: 50%;
  width: 12px;
  height: 12px;
  background: var(--color-espresso);
  border-radius: 50%;
  transform: translateY(-50%);
}

.left::after { right: calc(-11.1% - 6px); }
.right::after { left: calc(-11.1% - 6px); }

@media (max-width: 768px) {
  .line-base { left: 20px; }
  .timeline-item { width: calc(100% - 60px); align-self: flex-end !important; text-align: left !important; }
  .left::after, .right::after { left: -46px !important; }
  .event-icon { display: none; }
}
</style>
