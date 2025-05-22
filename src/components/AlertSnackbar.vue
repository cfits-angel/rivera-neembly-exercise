<template>
  <!-- Snackbar component to show alert messages -->
   <!-- Controls visibility -->
  <VSnackbar
    :model-value="modelValue"
    @update:model-value="$emit('update:modelValue', $event)" 
    :color="color"
    timeout="3500"
    location="top right"
    elevation="5"
    shaped
  >
    <!-- Icon on the left of the message -->
    <VIcon left>{{ icon }}</VIcon>
    
    <!-- Alert message content -->
    {{ message }}

    <!-- Close button slot -->
    <template #actions>
      <VBtn color="dark" icon @click="$emit('update:modelValue', false)">
        <VIcon icon="tabler-x" />
      </VBtn>
    </template>
  </VSnackbar>
</template>

<script setup>
// Define props passed from parent
const props = defineProps({
  modelValue: Boolean, // Visibility control
  message: String,     // Text message to display
  color: String,       // Snackbar color (e.g., success, error)
  icon: String,        // Icon name (e.g., tabler-check)
});

// Declare emitted event to update modelValue from within the component
const emits = defineEmits(["update:modelValue"]);
</script>