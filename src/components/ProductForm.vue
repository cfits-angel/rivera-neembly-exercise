<template>
  <!-- Dialog for adding or editing a product -->
    <!-- Controls dialog visibility -->
  <VDialog
    :model-value="modelValue"
    width="600"
    @update:model-value="$emit('update:modelValue', $event)"
  >
   <!-- Sync visibility -->
    
    <!-- Custom close button that closes the dialog -->
    <DialogCloseBtn @click="$emit('update:modelValue', false)" />

    <VCard class="pa-sm-8 pa-5">
      <VCardItem class="text-center">
        <!-- Title changes based on editing or creating -->
        <VCardTitle class="text-h5 mb-3">
          {{ isEditing ? "Edit Product Information" : "Add New Product" }}
        </VCardTitle>
        <!-- Instructional message changes accordingly -->
        <p class="mb-0">
          {{
            isEditing
              ? "Updating product details will affect the catalog."
              : "Fill the form to add a new product."
          }}
        </p>
      </VCardItem>

      <VCardText>
        <!-- Form with submit handler -->
        <VForm class="mt-6" @submit.prevent="onFormSubmit">
          <VRow>
            <!-- Product name input -->
            <VCol cols="12" md="12">
              <AppTextField
                v-model="localProduct.title"
                label="Name"
                placeholder="Enter name"
                required
              />
            </VCol>

            <!-- Product price input -->
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

            <!-- Category selector -->
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

            <!-- Description textarea -->
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

            <!-- Image URL input -->
            <VCol cols="12">
              <AppTextField
                v-model="localProduct.image"
                placeholder="Enter image URL"
                label="Image URL"
                type="url"
              />
            </VCol>

            <!-- Submit and cancel buttons -->
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

// Props received from parent
const props = defineProps({
  modelValue: Boolean, // Dialog visibility
  isEditing: Boolean,  // Whether editing or creating
  product: Object,     // Product data for editing
  categories: Array,   // List of categories for dropdown
});

// Events emitted to parent
const emits = defineEmits(["update:modelValue", "submit", "cancel"]);

// Local reactive product state to bind form inputs
const localProduct = reactive({
  id: 0,
  title: "",
  price: 0.1,
  description: "",
  category: null,
  image: "",
});

// Reset local product to default empty values
const reset = () => {
  localProduct.id = 0;
  localProduct.title = "";
  localProduct.price = 1;
  localProduct.description = "";
  localProduct.category = null;
  localProduct.image = "";
};

// Watch for changes to product prop and update localProduct accordingly
watch(
  () => props.product,
  (newVal) => {
    if (newVal) {
      Object.assign(localProduct, newVal); // Copy values for editing
    } else {
      reset(); // Clear form for new product
    }
  },
  { immediate: true } // Run immediately on mount
);

// Emit submit event with a copy of localProduct when form is submitted
const onFormSubmit = () => {
  emits("submit", { ...localProduct });
};

// Emit cancel event when user cancels
const onCancel = () => {
  emits("cancel");
};
</script>