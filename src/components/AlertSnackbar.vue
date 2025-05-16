<template>
  <VSnackbar
    :model-value="modelValue"
    @update:model-value="$emit('update:modelValue', $event)"
    :color="color"
    timeout="3500"
    location="top right"
    elevation="5"
    shaped
  >
    <VIcon left>{{ icon }}</VIcon>
    {{ message }}

    <template #actions>
      <VBtn color="dark" icon @click="$emit('update:modelValue', false)">
        <VIcon icon="tabler-x" />
      </VBtn>
    </template>
  </VSnackbar>
</template>


<script setup>
const props = defineProps({
  modelValue: Boolean,
  message: String,
  color: String,
  icon: String,
});

const emits = defineEmits(["update:modelValue"]);
</script>