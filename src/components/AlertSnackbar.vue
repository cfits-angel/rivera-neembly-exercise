<template>
  <!-- Snackbar component to show alert messages -->
  <VSnackbar
    :model-value="modelValue" <!-- Controls visibility -->
    @update:model-value="$emit('update:modelValue', $event)" <!-- Sync visibility with parent -->
    :color="color" <!-- Sets background color (success, error, etc.) -->
    timeout="3500" <!-- Auto-hide after 3.5 seconds -->
    location="top right" <!-- Position on screen -->
    elevation="5" <!-- Shadow depth -->
    shaped <!-- Gives snackbar rounded corners -->
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