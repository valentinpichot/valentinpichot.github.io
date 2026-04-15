<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'CinemaToggle',
  emits: ['toggle'],
  data() {
    return {
      isActive: false,
    };
  },
  methods: {
    handleToggle() {
      this.isActive = !this.isActive;
      this.$emit('toggle', this.isActive);
    },
  },
});
</script>

<template>
  <button :class="['cinema-toggle', { active: isActive }]" @click="handleToggle" aria-label="Mode Cinéma">
    <div class="toggle-icon">
      <svg v-if="!isActive" viewBox="0 0 24 24" fill="currentColor">
        <path d="M12 7c-2.76 0-5 2.24-5 5s2.24 5 5 5 5-2.24 5-5-2.24-5-5-5zm0-5C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.42 0-8-3.58-8-8s3.58-8 8-8 8 3.58 8 8-3.58 8-8 8z"/>
      </svg>
      <svg v-else viewBox="0 0 24 24" fill="currentColor">
        <path d="M12 3c-4.97 0-9 4.03-9 9s4.03 9 9 9 9-4.03 9-9c0-.46-.04-.92-.1-1.36-.98 1.37-2.58 2.26-4.4 2.26-3.03 0-5.5-2.47-5.5-5.5 0-1.82.89-3.42 2.26-4.4-.44-.06-.9-.1-1.36-.1z"/>
      </svg>
    </div>
    <span class="toggle-text">{{ isActive ? 'Mode Lumineux' : 'Mode Cinéma' }}</span>
  </button>
</template>

<style scoped>
.cinema-toggle {
  position: fixed;
  bottom: 40px;
  right: 40px;
  z-index: 1000;
  display: flex;
  align-items: center;
  gap: 12px;
  background-color: var(--color-espresso);
  color: var(--color-linen);
  border: none;
  padding: 12px 20px;
  border-radius: 40px;
  cursor: pointer;
  font-family: var(--font-sans);
  font-weight: 500;
  font-size: 14px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.1);
  transition: transform 0.3s var(--transition-smooth), background-color 0.3s ease;
}

.cinema-toggle:hover {
  transform: translateY(-5px);
  background-color: var(--color-black);
}

.cinema-toggle.active {
  background-color: var(--color-linen);
  color: var(--color-espresso);
}

.toggle-icon {
  width: 20px;
  height: 20px;
}

.toggle-text {
  text-transform: uppercase;
  letter-spacing: 1px;
}

@media (max-width: 768px) {
  .cinema-toggle {
    bottom: 20px;
    right: 20px;
    padding: 10px 16px;
  }
  .toggle-text {
    display: none;
  }
}
</style>
