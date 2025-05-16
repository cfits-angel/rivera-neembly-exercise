<template>
  <VDialog :model-value="modelValue" width="600" @update:model-value="$emit('update:modelValue', $event)">
    <DialogCloseBtn @click="$emit('update:modelValue', false)" />

    <VCard class="pa-sm-8 pa-5">
      <VCardItem class="text-center">
        <VCardTitle class="text-h5 mb-3">
          {{ isEditing ? "Edit Product Information" : "Add New Product" }}
        </VCardTitle>
        <p class="mb-0">
          {{
            isEditing
              ? "Updating product details will affect the catalog."
              : "Fill the form to add a new product."
          }}
        </p>
      </VCardItem>

      <VCardText>
        <VForm class="mt-6" @submit.prevent="onFormSubmit">
          <VRow>
            <VCol cols="12" md="12">
              <AppTextField
                v-model="localProduct.title"
                label="Name"
                placeholder="Enter name"
                required
              />
            </VCol>

            <VCol cols="12" md="6">
              <AppTextField
                v-model.number="localProduct.price"
                label="Price"
                placeholder="Enter price"
                type="number"
                min="0"
                step="0.01"
                required
              />
            </VCol>

            <VCol cols="12" md="6">
              <br />
              <VSelect
                v-model="localProduct.category"
                class="mt-1"
                :items="categories"
                label="Category"
                placeholder="Select category"
                clearable
                chips
                required
                dense
              />
            </VCol>

            <VCol cols="12">
              <AppTextField
                v-model="localProduct.description"
                label="Description"
                placeholder="Enter description"
                textarea
                rows="4"
                required
              />
            </VCol>

            <VCol cols="12">
              <AppTextField
                v-model="localProduct.image"
                placeholder="Enter image URL"
                label="Image URL"
                type="url"
              />
            </VCol>

            <VCol cols="12" class="d-flex flex-wrap justify-center gap-4">
              <VBtn type="submit">{{ isEditing ? "Update" : "Create" }}</VBtn>

              <VBtn color="secondary" variant="tonal" @click="onCancel">Cancel</VBtn>
            </VCol>
          </VRow>
        </VForm>
      </VCardText>
    </VCard>
  </VDialog>
</template>

<script setup>
import { reactive, watch } from "vue";

const props = defineProps({
  modelValue: Boolean,
  isEditing: Boolean,
  product: Object,
  categories: Array,
});

const emits = defineEmits(["update:modelValue", "submit", "cancel"]);

const localProduct = reactive({
  id: 0,
  title: "",
  price: 0.1,
  description: "",
  category: null,
  image: "",
});

const reset = () => {
  localProduct.id = 0;
  localProduct.title = "";
  localProduct.price = 1;
  localProduct.description = "";
  localProduct.category = null;
  localProduct.image = "";
};

watch(
  () => props.product,
  (newVal) => {
    if (newVal) {
      Object.assign(localProduct, newVal);
    } else {
      reset();
    }
  },
  { immediate: true }
);

const onFormSubmit = () => {
  emits("submit", { ...localProduct });
};

const onCancel = () => {
  emits("cancel");
};
</script>
