<template>
  <VDialog
    width="600"
    :model-value="modelValue"
    @update:model-value="$emit('update:modelValue', $event)"
  >
    <!-- Close button component, emits event to close dialog -->
    <DialogCloseBtn @click="$emit('update:modelValue', false)" />

    <VCard>
      <VCardTitle class="text-h5">{{ product?.title }}</VCardTitle>

      <VCardText>
        <VImg :src="product?.image" width="150" class="pb-5" />

        <p><strong>Price:</strong> {{ product?.price }}</p>
        <p><strong>Category:</strong> {{ product?.category }}</p>
        <p><strong>Rating:</strong> {{ product?.rating?.rate ?? "-" }}</p>
        <p><strong>Description:</strong> {{ product?.description }}</p>
      </VCardText>
    </VCard>
  </VDialog>
</template>

<script setup>
const props = defineProps({
  modelValue: Boolean,  // Controls visibility of dialog
  product: Object,      // The product details to show
});

const emits = defineEmits(["update:modelValue", "close"]);
</script>