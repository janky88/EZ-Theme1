<template>
  <div class="monitor-container">
    <div class="monitor-frame-wrapper">
      <iframe
        src="https://tz.******.com/"
        class="monitor-frame"
        frameborder="0"
        allowfullscreen
        referrerpolicy="no-referrer"
        @load="onFrameLoad"
        @error="onFrameError"
      ></iframe>

      <!-- 加载中提示 -->
      <div v-if="loading" class="monitor-loading">
        <div class="loading-spinner"></div>
        <p>正在加载监控面板...</p>
      </div>

      <!-- 加载失败提示 -->
      <div v-if="loadError" class="monitor-error">
        <div class="error-icon">⚠️</div>
        <p>监控面板加载失败，请检查网络或直接访问</p>
        <a href="https://tz.gyxxk.com/" target="_blank" class="monitor-link">
          在新标签页中打开
        </a>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, onBeforeUnmount } from 'vue';

export default {
  name: 'MonitorPage',
  setup() {
    const loading = ref(true);
    const loadError = ref(false);

    // 超时兜底：10 秒后若 iframe 仍未触发 load，显示错误提示
    let timeoutTimer = null;

    onMounted(() => {
      timeoutTimer = setTimeout(() => {
        if (loading.value) {
          loading.value = false;
          loadError.value = true;
        }
      }, 10000);
    });

    onBeforeUnmount(() => {
      if (timeoutTimer) clearTimeout(timeoutTimer);
    });

    const onFrameLoad = () => {
      if (timeoutTimer) clearTimeout(timeoutTimer);
      loading.value = false;
    };

    const onFrameError = () => {
      if (timeoutTimer) clearTimeout(timeoutTimer);
      loading.value = false;
      loadError.value = true;
    };

    return { loading, loadError, onFrameLoad, onFrameError };
  }
};
</script>

<style lang="scss" scoped>
.monitor-container {
  width: 100%;
  height: calc(100vh - 120px);
  display: flex;
  flex-direction: column;
}

.monitor-frame-wrapper {
  position: relative;
  flex: 1;
  border-radius: 16px;
  overflow: hidden;
  background: var(--card-background);
  border: 1px solid var(--border-color);
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.06);
}

.monitor-frame {
  width: 100%;
  height: 100%;
  border: none;
  display: block;
}

.monitor-loading,
.monitor-error {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 12px;
  background: var(--card-background);
  color: var(--secondary-text-color);
  font-size: 14px;
}

.loading-spinner {
  width: 36px;
  height: 36px;
  border: 3px solid var(--border-color);
  border-top-color: var(--theme-color);
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.error-icon {
  font-size: 36px;
}

.monitor-link {
  margin-top: 4px;
  color: var(--theme-color);
  text-decoration: none;
  font-weight: 500;
  padding: 8px 20px;
  border: 1px solid var(--theme-color);
  border-radius: 20px;
  transition: background 0.2s;

  &:hover {
    background: rgba(var(--theme-color-rgb), 0.08);
  }
}

@media (max-width: 768px) {
  .monitor-container {
    height: calc(100vh - 160px);
  }
}
</style>
