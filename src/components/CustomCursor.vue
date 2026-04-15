<script lang="ts">
import { defineComponent } from 'vue';
import gsap from 'gsap';

export default defineComponent({
  name: 'CustomCursor',
  data() {
    return {
      mouseX: 0,
      mouseY: 0,
      petals: [] as { id: number; x: number; y: number; rotation: number; scale: number }[],
      nextId: 0,
    };
  },
  mounted() {
    window.addEventListener('mousemove', this.updateMousePosition);
    this.spawnPetals();
  },
  beforeUnmount() {
    window.removeEventListener('mousemove', this.updateMousePosition);
  },
  methods: {
    updateMousePosition(e: MouseEvent) {
      this.mouseX = e.clientX;
      this.mouseY = e.clientY;
      
      const cursor = this.$refs.cursor as HTMLElement;
      if (cursor) {
        gsap.to(cursor, {
          x: this.mouseX,
          y: this.mouseY,
          duration: 0.1,
          ease: 'power2.out',
        });
      }
    },
    spawnPetals() {
      setInterval(() => {
        // Only spawn if mouse has moved significantly or just spawn a few
        if (this.mouseX !== 0) {
          const id = this.nextId++;
          const petal = {
            id,
            x: this.mouseX + (Math.random() - 0.5) * 20,
            y: this.mouseY + (Math.random() - 0.5) * 20,
            rotation: Math.random() * 360,
            scale: 0.5 + Math.random() * 0.5,
          };
          this.petals.push(petal);
          
          // Animate petal disappearance
          setTimeout(() => {
            this.petals = this.petals.filter(p => p.id !== id);
          }, 2000);
        }
      }, 150);
    }
  }
});
</script>

<template>
  <div class="cursor-wrapper">
    <div ref="cursor" class="main-cursor"></div>
    <div v-for="petal in petals" :key="petal.id" 
         class="petal" 
         :style="{ 
           left: petal.x + 'px', 
           top: petal.y + 'px', 
           transform: `rotate(${petal.rotation}deg) scale(${petal.scale})` 
         }">
      <svg viewBox="0 0 24 24" fill="var(--color-dusty-rose)" xmlns="http://www.w3.org/2000/svg">
        <path d="M12 2C12 2 15 6 15 10C15 14 12 18 12 18C12 18 9 14 9 10C9 6 12 2 12 2Z" />
      </svg>
    </div>
  </div>
</template>

<style scoped>
.cursor-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 10000;
}

.main-cursor {
  position: absolute;
  width: 8px;
  height: 8px;
  background-color: var(--color-espresso);
  border-radius: 50%;
  transform: translate(-50%, -50%);
  transition: transform 0.2s var(--transition-smooth), opacity 0.3s ease;
}

.petal {
  position: absolute;
  width: 12px;
  height: 12px;
  pointer-events: none;
  opacity: 0.6;
  animation: fall 2s forwards linear;
}

@keyframes fall {
  0% {
    transform: translate(0, 0) rotate(0deg) scale(1);
    opacity: 0.6;
  }
  100% {
    transform: translate(0, 100px) rotate(360deg) scale(0);
    opacity: 0;
  }
}
</style>
