<script setup lang="ts">
import { ref, defineProps, watch, computed, defineEmits } from 'vue'

const props = defineProps<{
  enabled: boolean
}>()

const emits = defineEmits<{
  (e: 'update:enabled', value: boolean): void
}>()

// Sync local state with parent
const isOn = ref(props.enabled)

watch(
  () => props.enabled,
  (newVal) => {
    isOn.value = newVal
  }
)

function toggle() {
  isOn.value = !isOn.value
  emits('update:enabled', isOn.value)
}

const switchClasses = computed(() => [
  'relative inline-flex items-center h-6 rounded-full w-11 transition-colors duration-300',
  isOn.value ? 'bg-green-600' : 'bg-gray-300'
])
</script>

<template>
  <button :class="switchClasses" @click="toggle">
    <span
      class="inline-block w-4 h-4 transform bg-white rounded-full transition-transform duration-300"
      :class="isOn ? 'translate-x-6' : 'translate-x-1'"
    />
  </button>
</template>

<style scoped>
/* No additional scoped styles needed — fully styled with Tailwind */
</style>