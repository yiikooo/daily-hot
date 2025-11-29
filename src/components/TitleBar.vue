<template>
  <div class="title-bar" :class="{ shrink: isShrunk }">
    <div class="left-section">
      <div class="icon-wrapper">
        <img src="../assets/static/icon.svg" alt="Hot List Icon" class="hot-list-icon" />
      </div>
      <div class="text-content">
        <h1 class="title">今日热榜</h1>
        <p class="subtitle">汇聚全网热点，热门尽览无余</p>
      </div>
    </div>
    <div class="right-section">
      <div class="date-time">
        <p class="date">2025-11-29 20:04:51</p>
        <p class="lunar-date">乙巳年 十月初十 星期六</p>
      </div>
      <div class="buttons">
        <button class="icon-button">
          <img src="../assets/static/refresh.svg" alt="Refresh" />
        </button>
        <button class="icon-button"><img src="../assets/static/moon.svg" alt="Dark Mode" /></button>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { onMounted, onUnmounted, ref } from 'vue';
const isShrunk = ref(false);

const handleScroll = () => {
  if (window.scrollY > 50) {
    isShrunk.value = true;
  } else {
    isShrunk.value = false;
  }
};

onMounted(() => {
  window.addEventListener('scroll', handleScroll);
});

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll);
});
</script>
<style scoped>
.title-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background-color: var(--title-bar-bg);
  color: var(--text-color);
  width: 100%;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1000;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease-in-out;
}

.title-bar.shrink {
  padding: 5px 20px;
  transform: scale(0.95);
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.15);
}

.left-section {
  display: flex;
  align-items: center;
}

.icon-wrapper {
  width: 40px;
  height: 40px;
  background-color: #ff4d4f; /* Example red color */
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-right: 15px;
}

.hot-list-icon {
  width: 24px;
  height: 24px;
  filter: invert(1); /* Make icon white */
}

.text-content .title {
  font-size: 24px;
  font-weight: bold;
  margin: 0;
}

.text-content .subtitle {
  font-size: 14px;
  color: var(--secondary-text-color);
  margin: 0;
}

.right-section {
  display: flex;
  align-items: center;
}

.date-time {
  text-align: right;
  margin-right: 20px;
}

.date-time .date {
  font-size: 14px;
  margin: 0;
}

.date-time .lunar-date {
  font-size: 12px;
  color: var(--secondary-text-color);
  margin: 0;
}

.buttons {
  display: flex;
}

.icon-button {
  background-color: var(--button-bg);
  border: none;
  border-radius: 8px;
  padding: 8px;
  margin-left: 10px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.icon-button:hover {
  background-color: var(--button-hover-bg);
}

.icon-button img {
  width: 20px;
  height: 20px;
  filter: var(--icon-filter, none); /* Apply filter if in dark mode */
}
</style>
