<script setup>
import { useCssModule } from "vue";
const styles = useCssModule();

const props = defineProps({
    data: {
        type: Object,
        default: () => ({}),
    },
});
</script>

<template>
    <NuxtLink :to="data.link" :class="styles.button" :target="data.target || '_self'" :rel="data.rel || ''">
        {{ data.title }}
    </NuxtLink>
</template>

<style lang="scss" scoped module>
.button {
    font-family: var(--font-02);
    font-size: 1.25rem;
    font-weight: 500;
    color: var(--color-01);
    text-align: center;
    width: fit-content;
    min-width: 8rem;
    padding: 1rem 1.5rem;
    border-radius: 5rem;
    position: relative;
    z-index: 1;
    overflow: hidden;
    border: 0.125rem solid var(--color-01);
    transition: all 0.3s ease-in-out;

    @include media(mobile) {
        font-size: 1rem;
        min-width: auto;
        padding: 0.75rem 1rem;
    }

    &:hover {
        background: var(--color-02);
        color: var(--color-white);
    }
}
</style>