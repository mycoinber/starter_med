<script setup>
import { useCssModule } from "vue";
import { useNuxtApp } from "#app";
import { useAsyncData } from "#app";

const { $axios } = useNuxtApp();
const config = useRuntimeConfig();
const siteId = import.meta.server
  ? config.server.siteId
  : config.public.siteId;
const styles = useCssModule();

const fetchPages = async (siteId) => {
  try {
    const response = await $axios.get(`/pages/nav?siteId=${siteId}`);
    return response.data;
  } catch (error) {
    console.error("Ошибка при запросе данных:", error);
    throw error;
  }
};

const { data, error } = useAsyncData("pages", () => fetchPages(siteId), {
  ssr: true,
});

const hasError = error.value;
</script>

<template>
  <article :class="styles.article">

    <div v-if="hasError">Произошла ошибка при загрузке данных</div>

    <Header v-if="data && !hasError" :data="data" />

    <slot />

    <Footer v-if="data && !hasError" :data="data" />
  </article>
</template>

<style lang="scss" scoped module>
.article {
  max-width: 70%;
  margin: 0 auto;
}
</style>
