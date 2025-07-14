<script setup>
import { useCssModule } from "vue";
import { useQuery } from "@tanstack/vue-query";
import { useI18n } from 'vue-i18n';
import { computed } from 'vue';
const { t } = useI18n();

const config = useRuntimeConfig();

const props = defineProps({
  data: {
    type: Object,
    default: () => ({}),
  },
});

const backHost = import.meta.server
  ? config.server.backHost
  : config.public.backHost;

const styles = useCssModule();
const { $axios } = useNuxtApp();

const heroSections = computed(() => {
  return offer.value?.sections?.filter(section => section.type === 'hero') || [];
});

const fetchOffer = async () => {
  const response = await $axios.get(`/public/offer/${props.data.offer._id}`);
  return response.data;
};

const {
  data: offer,
  isPending,
  isError,
  error,
  refetch,
} = useQuery({
  queryKey: computed(() => ["offers", props.data.offer]),
  queryFn: fetchOffer,
});

watch(offer, (newData) => {
});
</script>

<template>
  <div v-if="offer" :class="styles.wrapper">
    <div :class="styles.content">

      <span :class="styles.title">{{ offer.label }}</span>

      <span :class="styles.span">{{ offer.title }}</span>

      <GeneralButton :data="{
        link: offer.link || '',
        title: offer.button1 || t('play'),
        target: '_blank',
        rel: 'noopener noreferrer',
      }" :class="styles.contentButton" />
    </div>

    <div :class="styles.img">
    </div>
  </div>
</template>

<style lang="scss" scoped module>
.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 2.5rem;
}

.content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  max-width: 40.625rem;
  padding-top: 5rem;
  margin-bottom: 2.5rem;

  @include media(mobile) {
    max-width: 100%;
    padding-top: 3rem;
  }
}

.title {
  font-size: 4.25rem;
  font-weight: 500;
  color: var(--color-black);
  margin-bottom: 1.5rem;
  text-align: center;
}

.span {
  font-size: 1rem;
  font-weight: 400;
  color: var(--color-gray);
  margin-bottom: 2.5rem;
  text-align: center;
}

.img {
  background-image: url('/hero.png');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  width: 100%;
  height: 50rem;

  @include media(mobile) {
    height: 20rem;
  }
}
</style>
