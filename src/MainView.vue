<template>
  <TitleBar></TitleBar>
  <main class="container"></main>
</template>
<script setup lang="ts">
import TitleBar from './components/TitleBar.vue';
</script>
<style scoped>
main {
  max-width: 1200px;
  width: 100%;
  padding: 20px;
  padding-top: 100px; /* 为吸顶的标题栏留出空间 */
  margin: 0 auto; /* 居中显示 */
  min-height: 150vh; /* 确保有足够的滚动内容 */
}
</style>
