<script setup lang="ts">
import * as icons from 'lucide-vue-next'
import { computed } from 'vue'

interface IconProps {
  name: keyof typeof icons // Visit https://lucide.dev/icons to see all available icons
  size?: number
  color?: string
  strokeWidth?: number
  defaultClass?: string
}

const props = defineProps<IconProps>()

const icon = computed(() => icons[props.name])
</script>

<template>
  <component
    :is="icon"
    :size="size"
    :color="color"
    :stroke-width="strokeWidth"
    :default-class="defaultClass"
  />
</template>
