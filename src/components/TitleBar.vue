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
        <p class="date">{{ currentDateTime }}</p>
        <p class="lunar-date">{{ currentLunarDate }}</p>
      </div>
      <div class="buttons">
        <button class="icon-button" @click="refreshPage">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" viewBox="0 0 512 512">
            <path
              d="M436.7 74.7L448 85.4 448 32c0-17.7 14.3-32 32-32s32 14.3 32 32l0 128c0 17.7-14.3 32-32 32l-128 0c-17.7 0-32-14.3-32-32s14.3-32 32-32l47.9 0-7.6-7.2c-.2-.2-.4-.4-.6-.6-75-75-196.5-75-271.5 0s-75 196.5 0 271.5 196.5 75 271.5 0c8.2-8.2 15.5-16.9 21.9-26.1 10.1-14.5 30.1-18 44.6-7.9s18 30.1 7.9 44.6c-8.5 12.2-18.2 23.8-29.1 34.7-100 100-262.1 100-362 0S-25 175 75 75c99.9-99.9 261.7-100 361.7-.3z" />
          </svg>
        </button>
        <button class="icon-button" @click="toggleTheme">
          <svg xmlns="http://www.w3.org/2000/svg" width="18" viewBox="0 0 576 512">
            <path
              d="M178.2-10.1c7.4-3.1 15.8-2.2 22.5 2.2l87.8 58.2 87.8-58.2c6.7-4.4 15.1-5.2 22.5-2.2S411.4-.5 413 7.3l20.9 103.2 103.2 20.9c7.8 1.6 14.4 7 17.4 14.3s2.2 15.8-2.2 22.5l-58.2 87.8 58.2 87.8c4.4 6.7 5.2 15.1 2.2 22.5s-9.6 12.8-17.4 14.3L433.8 401.4 413 504.7c-1.6 7.8-7 14.4-14.3 17.4s-15.8 2.2-22.5-2.2l-87.8-58.2-87.8 58.2c-6.7 4.4-15.1 5.2-22.5 2.2s-12.8-9.6-14.3-17.4L143 401.4 39.7 380.5c-7.8-1.6-14.4-7-17.4-14.3s-2.2-15.8 2.2-22.5L82.7 256 24.5 168.2c-4.4-6.7-5.2-15.1-2.2-22.5s9.6-12.8 17.4-14.3L143 110.6 163.9 7.3c1.6-7.8 7-14.4 14.3-17.4zM207.6 256a80.4 80.4 0 1 1 160.8 0 80.4 80.4 0 1 1 -160.8 0zm208.8 0a128.4 128.4 0 1 0 -256.8 0 128.4 128.4 0 1 0 256.8 0z" />
          </svg>
        </button>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { onMounted, onUnmounted, ref } from 'vue';
import calendar from 'js-calendar-converter';

const isShrunk = ref(false);
const currentDateTime = ref('');
const currentLunarDate = ref('');
let timer: number | undefined;

const getLunarDate = (date: Date) => {
  const lunar = calendar.solar2lunar(date.getFullYear(), date.getMonth() + 1, date.getDate());
  if (lunar) {
    const dayOfWeek = new Date().toLocaleString('zh-CN', { weekday: 'long' });
    return `${lunar.gzYear}年 ${lunar.IMonthCn}${lunar.IDayCn} ${dayOfWeek}`;
  }
  return '';
};

const updateDateTime = () => {
  const now = new Date();
  currentDateTime.value = now
    .toLocaleString('zh-CN', {
      year: 'numeric',
      month: '2-digit',
      day: '2-digit',
      hour: '2-digit',
      minute: '2-digit',
      second: '2-digit',
      hour12: false,
    })
    .replace(/\//g, '-'); // 格式化为YYYY-MM-DD HH:mm:ss

  currentLunarDate.value = getLunarDate(now);
};

const handleScroll = () => {
  if (window.scrollY > 50) {
    isShrunk.value = true;
  } else {
    isShrunk.value = false;
  }
};

onMounted(() => {
  window.addEventListener('scroll', handleScroll);
  updateDateTime();
  timer = setInterval(updateDateTime, 1000); // 每秒更新一次时间
});

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll);
  if (timer) {
    clearInterval(timer);
  }
});

const refreshPage = () => {
  window.location.reload();
};

const toggleTheme = () => {
  const isDark = document.documentElement.getAttribute('data-theme') === 'dark';
  if (isDark) {
    document.documentElement.removeAttribute('data-theme');
  } else {
    document.documentElement.setAttribute('data-theme', 'dark');
  }
};
</script>
<style scoped>
.title-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0px 45px;
  padding-top: 25px;
  background-color: var(--bg);
  color: var(--text-color);
  width: 100%;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1000;
  border-bottom: var(--border) solid 0px;
}

.title-bar.shrink {
  padding: 10px 30px;
  border-bottom: var(--border) solid 1px;
  background-color: var(--title-bar-bg);
}

.left-section {
  display: flex;
  align-items: center;
}

.icon-wrapper {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-right: 15px;
}

.hot-list-icon {
  width: 38px;
  height: 38px;
}

.text-content .title {
  font-size: 18px;
  margin: 0;
}

.text-content .subtitle {
  font-size: 12px;
  color: var(--secondary-text-color);
  margin: 0;
}

.right-section {
  display: flex;
  align-items: center;
  flex-grow: 1; /* 允许右侧部分增长 */
  justify-content: flex-end; /* 默认靠右 */
  min-width: 0; /* 允许收缩 */
}

.date-time {
  text-align: right;
  margin-right: 20px;
  flex-shrink: 0; /* 防止日期时间部分收缩 */
}

@media (max-width: 768px) {
  .right-section .date-time {
    display: none; /* 更小屏幕直接隐藏日期时间 */
  }
}

.icon-button path {
  fill: var(--secondary-text-color);
}

@media (min-width: 769px) {
  .right-section {
    justify-content: center; /* 宽屏时尝试居中 */
  }
  .date-time {
    margin-right: auto; /* 推开按钮，实现居中 */
    margin-left: auto; /* 推开左侧，实现居中 */
    position: absolute; /* 绝对定位，脱离文档流 */
    left: 50%; /* 距离左侧50% */
    transform: translateX(-50%); /* 向左移动自身宽度的一半 */
  }
  .buttons {
    margin-left: auto; /* 推开日期，靠右显示 */
  }
}

@media (max-width: 500px) {
  .right-section {
    justify-content: flex-end; /* 按钮靠右 */
  }
  .date-time .lunar-date {
    display: none; /* 小屏幕隐藏农历 */
  }
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
  border-radius: 16px;
  padding: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-left: 10px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  width: 65px;
  height: 30px;
}
.buttons .icon-button {
  padding: 6px;
  margin-left: 15px;
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
