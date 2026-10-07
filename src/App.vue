<template>
  <router-view />

  <button
    class="theme-toggle"
    type="button"
    :aria-label="darkMode ? 'Activar modo claro' : 'Activar modo oscuro'"
    :aria-pressed="darkMode"
    @click="toggleTheme"
  >
    <span aria-hidden="true">{{ darkMode ? '☀️' : '🌙' }}</span>
  </button>
</template>

<script setup>
import { onMounted, ref, watch } from 'vue'

const darkMode = ref(false)

const toggleTheme = () => {
  darkMode.value = !darkMode.value
}

onMounted(() => {
  const savedTheme = localStorage.getItem('tailscan-theme')
  if (savedTheme === 'dark') {
    darkMode.value = true
  }
})

watch(darkMode, (isDark) => {
  document.documentElement.dataset.theme = isDark ? 'dark' : 'light'
  localStorage.setItem('tailscan-theme', isDark ? 'dark' : 'light')
}, { immediate: true })
</script>

<style scoped>
.theme-toggle {
  position: fixed;
  right: 22px;
  bottom: 22px;
  z-index: 10000;
  width: 50px;
  height: 50px;
  border: 2px solid rgba(255, 255, 255, .9);
  border-radius: 50%;
  background: rgba(30, 58, 138, .9);
  box-shadow: 0 10px 28px rgba(15, 23, 42, .28);
  color: #fff;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 22px;
  transition: transform .2s ease, box-shadow .2s ease;
}
.theme-toggle:hover {
  transform: translateY(-3px) scale(1.04);
  box-shadow: 0 14px 32px rgba(15, 23, 42, .36);
}
.theme-toggle:focus-visible {
  outline: 3px solid rgba(242, 99, 43, .5);
  outline-offset: 3px;
}
@media (max-width: 700px) {
  .theme-toggle {
    right: 14px;
    bottom: 14px;
    width: 46px;
    height: 46px;
  }
}
</style>