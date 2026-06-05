<script setup lang="ts">
import Skeleton from "@/components/Skeleton.vue";
import { Icon, loadIcon } from "@iconify/vue";
import {onMounted, ref} from "vue";

const props = defineProps<{
  icon: string
}>()

const loaded = ref(false)
onMounted(async () => {
  await loadIcon(props.icon)
  loaded.value = true
})
</script>

<template>
  <div>
    <Icon v-if="loaded" :icon="icon" v-bind="$attrs"/>
    <Skeleton v-else v-bind="$attrs"/>
  </div>
</template>