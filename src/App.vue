<script setup lang="ts">
import { RouterView, useRoute } from 'vue-router';
import { NGlobalStyle, NMessageProvider, NNotificationProvider, darkTheme } from 'naive-ui';
import { useI18n } from 'vue-i18n';
import { useHead } from '@vueuse/head';
import { darkThemeOverrides, lightThemeOverrides } from './themes';
import { layouts } from './layouts';
import { useStyleStore } from './stores/style.store';

const route = useRoute();
const layout = computed(() => route?.meta?.layout ?? layouts.base);
const styleStore = useStyleStore();

const theme = computed(() => (styleStore.isDarkTheme ? darkTheme : null));
const themeOverrides = computed(() => (styleStore.isDarkTheme ? darkThemeOverrides : lightThemeOverrides));

const { locale } = useI18n();
syncRef(locale, useStorage('locale', locale));

// 动态生成 hreflang 标签
const locales = ['en', 'de', 'es', 'fr', 'pt', 'ru', 'uk', 'zh', 'vi'];
const currentUrl = window.location.href;
const baseUrl = currentUrl.split('/').slice(0, -1).join('/');

useHead({
  link: locales.map(lang => ({
    rel: 'alternate',
    hreflang: lang,
    href: `${baseUrl}/${lang}${window.location.pathname}`,
  })),
});
</script>

<template>
  <n-config-provider :theme="theme" :theme-overrides="themeOverrides">
    <NGlobalStyle />
    <NMessageProvider placement="bottom">
      <NNotificationProvider placement="bottom-right">
        <component :is="layout">
          <RouterView />
        </component>
      </NNotificationProvider>
    </NMessageProvider>
  </n-config-provider>
</template>

<style>
body {
  min-height: 100%;
  margin: 0;
  padding: 0;
}

html {
  height: 100%;
  margin: 0;
  padding: 0;
}

* {
  box-sizing: border-box;
}
</style>
