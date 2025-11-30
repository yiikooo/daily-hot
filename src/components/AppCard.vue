<template>
  <div class="app-card">
    <div class="app-header">
      <img :src="`app-icon${apiUrl}.png`" alt="App Icon" class="app-icon" />
      <h2 class="app-name">{{ appName }}</h2>
      <div v-if="formattedUpdateTime" class="update-time">{{ formattedUpdateTime }}</div>
    </div>
    <div class="app-content-wrapper">
      <div v-if="loading" class="loading-indicator">加载中...</div>
      <div v-if="error" class="error-message">错误: {{ error.message }}</div>
      <ul v-if="hotSearchData.length" class="hot-search-list">
        <li v-for="(item, index) in hotSearchData" :key="item.id" class="hot-search-item">
          <span class="rank">{{ index + 1 }}</span>
          <div class="item-content">
            <a :href="item.url" target="_blank" class="item-title">{{ item.title }}</a>
            <div class="item-meta">
              <span v-if="item.author" class="item-author">{{ item.author }}</span>
              <span class="item-hot">{{ formatHot(item.hot) }}</span>
            </div>
          </div>
          <img v-if="item.cover" :src="item.cover" alt="Cover" class="item-cover" />
        </li>
      </ul>
      <div v-else-if="!loading && !error" class="no-data">暂无数据</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';

interface HotSearchItem {
  id: string;
  title: string;
  desc: string;
  cover: string;
  author: string;
  timestamp: string | null;
  hot: number;
  url: string;
  mobileUrl: string;
}

interface ApiResponse {
  code: number;
  name: string;
  title: string;
  type: string;
  description: string;
  params: unknown; // 根据提供的数据结构，此处类型不明确，暂时使用 unknown
  link: string;
  total: number;
  updateTime: string;
  fromCache: boolean;
  data: HotSearchItem[];
}

const props = defineProps<{
  appName: string;
  apiUrl: string;
}>();

const hotSearchData = ref<HotSearchItem[]>([]);
const loading = ref(true);
const error = ref<Error | null>(null);
const api = 'https://hot.api.yik.at';
const updateTime = ref<string | null>(null);

const fetchHotSearchData = async () => {
  try {
    loading.value = true;
    error.value = null;
    const response = await fetch(api + props.apiUrl);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data: ApiResponse = await response.json();
    updateTime.value = data.updateTime;
    // 根据热度进行排序
    hotSearchData.value = data.data;
    // hotSearchData.value = data.data.sort((a, b) => b.hot - a.hot);
  } catch (e) {
    if (e instanceof Error) {
      error.value = e;
    } else {
      error.value = new Error('An unknown error occurred');
    }
  } finally {
    loading.value = false;
  }
};

const formattedUpdateTime = computed(() => {
  if (!updateTime.value) return '';
  console.log(updateTime.value);

  const date = new Date(updateTime.value);
  // const month = (date.getMonth() + 1).toString().padStart(2, '0');
  // const day = date.getDate().toString().padStart(2, '0');
  const hours = date.getHours().toString().padStart(2, '0');
  const minutes = date.getMinutes().toString().padStart(2, '0');
  return `${hours}:${minutes}`;
});

onMounted(() => {
  fetchHotSearchData();
});

// 格式化热度显示
const formatHot = (hot: number): string => {
  try {
    if (hot >= 10000) {
      return (hot / 10000).toFixed(2) + 'w';
    } else if (hot >= 7000) {
      // 超过7千但不足一万，显示为0.XXW
      return (hot / 10000).toFixed(2) + 'w';
    }
    return hot.toString();
  } catch {
    return 'N/A';
  }
};
</script>

<style scoped>
.app-card {
  border: 1px solid var(--border);
  border-radius: 8px;
  background-color: var(--title-bar-bg); /* 使用 CSS 变量 */
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05); /* 阴影保持不变 */
  height: 500px; /* 统一卡片高度 */
  display: flex; /* 使用 flex 布局确保内容从顶部开始 */
  flex-direction: column; /* 垂直排列内容 */
}

.app-header {
  margin-top: 12px;
  margin-left: 12px;
  margin-right: 12px;
  display: flex;
  align-items: center;
  padding-bottom: 10px; /* 标题和内容之间的间距 */
  border-bottom: 1px solid var(--border); /* 标题下方分隔线 */
  /* 确保标题在滚动内容之上 */
  z-index: 1;
  position: sticky; /* 固定标题 */
  top: 0;
  position: relative; /* 允许绝对定位子元素 */
}

.update-time {
  position: absolute; /* 绝对定位 */
  top: 0px; /* 调整位置 */
  right: 0px; /* 调整位置 */
  font-size: 12px;
  color: var(--secondary-text-color);
  background-color: var(--title-bar-bg); /* 背景颜色与卡片头部一致 */
  padding: 2px 8px;
  border-radius: 0 8px 0 8px; /* 圆角与卡片顶部圆角匹配 */
}

.app-content-wrapper {
  padding: 0 16px;
  flex-grow: 1; /* 让内容区域占据剩余空间 */
  overflow-y: auto; /* 仅内容区域滚动 */
  padding-top: 10px; /* 调整内容顶部内边距，与标题分隔 */
}

.app-icon {
  width: 22px;
  height: 22px;
  border-radius: 8px;
  margin-right: 10px;
  margin-left: 6px;
  object-fit: cover;
}

.app-name {
  font-size: 16px;
  color: var(--text-color);
  margin: 0;
  margin-bottom: 2px;
}

.loading-indicator,
.error-message,
.no-data {
  text-align: center;
  padding: 20px 0;
  color: var(--secondary-text-color); /* 使用 CSS 变量 */
}

.error-message {
  color: #d9534f; /* 错误颜色保持不变 */
}

.hot-search-list {
  list-style: none;
  margin: 0 16px;

  padding: 0;
  margin: 0;
}

.hot-search-item {
  display: flex;
  align-items: center;
  padding: 10px 0;
  border-bottom: 1px dashed var(--border); /* 使用 CSS 变量 */
}

.hot-search-item:last-child {
  border-bottom: none;
}

.rank {
  font-size: 1.2em;
  font-weight: bold;
  color: var(--secondary-text-color); /* 使用 CSS 变量 */
  margin-right: 15px;
  width: 25px;
  text-align: center;
  flex-shrink: 0;
}

.item-content {
  flex-grow: 1;
  margin-right: 10px;
}

.item-title {
  font-size: 15px;
  color: var(--text-color); /* 使用 CSS 变量 */
  text-decoration: none;
  transition: color 0.2s;
  display: block; /* Ensures title takes full width */
  margin-bottom: 5px;
}

.item-title:hover {
  color: var(--secondary-text-color); /* 使用 CSS 变量 */
  text-decoration: underline;
}

.item-meta {
  font-size: 13px;
  color: var(--secondary-text-color); /* 使用 CSS 变量 */
}

.item-author {
  margin-right: 10px;
}

.item-hot {
  color: #f0ad4e; /* 热度颜色保持不变 */
}

.item-cover {
  width: 80px;
  height: 60px;
  object-fit: cover;
  border-radius: 4px;
  margin-left: 15px;
  flex-shrink: 0;
}
</style>
