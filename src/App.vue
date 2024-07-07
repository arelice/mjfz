<script setup lang="ts">
import { onMounted } from 'vue'
import { NConfigProvider } from 'naive-ui'
import { NaiveProvider } from '@/components/common'
import { useTheme } from '@/hooks/useTheme'
import { useLanguage } from '@/hooks/useLanguage'
import aiOther from "@/views/mj/aiOther.vue" 

const { theme, themeOverrides } = useTheme()
const { language } = useLanguage()

// 函数：存储 API 密钥到 localStorage
const storeApiKeys = (keys: Record<string, string>) => {
  localStorage.setItem('apiKeys', JSON.stringify(keys));
}

// 函数：从 localStorage 获取 API 密钥
const getApiKeys = (): Record<string, string> => {
  const keys = localStorage.getItem('apiKeys');
  return keys ? JSON.parse(keys) : {};
}

// 函数：从 URL 初始化
const initializeFromUrl = () => {
  const urlParams = new URLSearchParams(window.location.search);
  const key = urlParams.get('key');
  if (key) {
    // 如果 URL 中有密钥，则更新存储
    const apiKeys = {
      OPENAI_API_KEY: key,
      MJ_API_SECRET: key,
      SUNO_KEY: key,
      LUMA_KEY: key
    };
    storeApiKeys(apiKeys);
    
    // 从 URL 中移除 key 参数
    window.history.replaceState({}, document.title, window.location.pathname);
  } else {
    // 如果 URL 中没有密钥，检查是否已经存储在 localStorage 中
    const storedKeys = getApiKeys();
    if (Object.keys(storedKeys).length === 0) {
      console.log('No API keys found in URL or localStorage');
    }
  }
}

// 在组件挂载时执行初始化
onMounted(() => {
  initializeFromUrl();
})

// 导出 getApiKeys 函数，以便其他组件可以使用
defineExpose({ getApiKeys });
</script>

<template>
  <NConfigProvider
    class="h-full"
    :theme="theme"
    :theme-overrides="themeOverrides"
    :locale="language"
  >
    <NaiveProvider>
      <RouterView />
    </NaiveProvider>
  </NConfigProvider>
  <!-- 处理一下chat 与draw 共有的事情 -->
  <aiOther/>
</template>
