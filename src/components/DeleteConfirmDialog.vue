<template>
  <!-- Dialog component for confirming deletion -->
  <VDialog
    width="400"
    :model-value="modelValue" 
    @update:model-value="$emit('update:modelValue', $event)"
  >
    <!-- Controls dialog visibility -->
    <!-- Sync dialog state with parent -->

    <!-- Custom close button inside the dialog -->
    <DialogCloseBtn @click="$emit('update:modelValue', false)" />

    <VCard>
      <!-- Dialog title -->
      <VCardTitle>Confirm Delete</VCardTitle>

      <!-- Dialog body text -->
      <VCardText>Are you sure you want to delete this product?</VCardText>

      <!-- Dialog action buttons -->
      <VCardActions>
        <VSpacer /> <!-- Push buttons to the right -->
        <VBtn
          variant="tonal"
          color="secondary"
          @click="$emit('cancel')"
        >
          Cancel
        </VBtn> <!-- Emit cancel event to parent -->
        <VBtn
          variant="tonal"
          color="error"
          @click="$emit('confirm')"
        >
          Delete
        </VBtn> <!-- Emit confirm event to parent -->
      </VCardActions>
    </VCard>
  </VDialog>
</template>

<script setup>
// Props: control whether dialog is shown
const props = defineProps({
  modelValue: Boolean,
});

// Declare emitted events:
// - update:modelValue: sync visibility
// - confirm: user confirmed deletion
// - cancel: user canceled action
const emits = defineEmits(["update:modelValue", "confirm", "cancel"]);
</script>