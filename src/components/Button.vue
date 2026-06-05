<script setup lang="ts">
const props = withDefaults(defineProps<{
  variant?: "primary" | "secondary"
}>(), {
  variant: "primary",
})

const styles = {
  'primary': 'bg-primary text-bg hover:bg-primary/80',
  'secondary': 'border-3 border-secondary bg-secondary/20 hover:bg-secondary/50',
}
</script>

<template>
  <button class="rounded-xl p-2 transition-opacity hover:cursor-pointer disabled:opacity-50 disabled:cursor-auto!" :class="styles[variant!]">
    <slot/>
  </button>
</template>