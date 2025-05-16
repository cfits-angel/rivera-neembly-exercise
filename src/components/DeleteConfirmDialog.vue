<template>
  <VDialog width="400" :model-value="modelValue" @update:model-value="$emit('update:modelValue', $event)">
    <DialogCloseBtn @click="$emit('update:modelValue', false)" />

    <VCard>
      <VCardTitle>Confirm Delete</VCardTitle>
      <VCardText>Are you sure you want to delete this product?</VCardText>
      <VCardActions>
        <VSpacer />
        <VBtn variant="tonal" color="secondary" @click="$emit('cancel')">Cancel</VBtn>
        <VBtn variant="tonal" color="error" @click="$emit('confirm')">Delete</VBtn>
      </VCardActions>
    </VCard>
  </VDialog>
</template>

<script setup>
const props = defineProps({
  modelValue: Boolean,
});
const emits = defineEmits(["update:modelValue", "confirm", "cancel"]);
</script>
