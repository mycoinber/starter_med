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
  if (!props.data.offer?._id) return null;

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
  enabled: computed(() => Boolean(props.data.offer?._id)),
});

const getMediaUrl = (media) => {
  const path = media?.path;
  if (!path) return "";
  if (/^https?:\/\//.test(path)) return path;

  const host = String(backHost || "").replace(/\/$/, "");
  const normalizedPath = path.startsWith("/") ? path : `/${path}`;
  return `${host}${normalizedPath}`;
};

const offerImageUrl = computed(() => {
  const media =
    offer.value?.mainImage?.[0] ||
    offer.value?.background?.[0] ||
    heroSections.value?.[0]?.images?.[0];

  return getMediaUrl(media) || "/hero.png";
});

const offerImageStyle = computed(() => ({
  backgroundImage: `url("${offerImageUrl.value}")`,
}));

watch(offer, (newData) => {
});
</script>

<template>
  <div v-if="offer" :class="styles.wrapper">
    <a
      v-if="offer.link"
      :href="offer.link"
      :class="styles.img"
      :style="offerImageStyle"
      :aria-label="offer.label || t('play')"
      target="_blank"
      rel="noopener noreferrer"
    >
    </a>

    <div v-else :class="styles.img" :style="offerImageStyle">
    </div>
  </div>
</template>

<style lang="scss" scoped module>
.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100vw;
  margin-left: calc(50% - 50vw);
  margin-right: calc(50% - 50vw);
  margin-bottom: 2.5rem;
}

.img {
  background-image: url('/hero.png');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  display: block;
  width: 100vw;
  height: 100vh;
  cursor: pointer;

  @include media(mobile) {
    height: 100vh;
  }
}
</style>
