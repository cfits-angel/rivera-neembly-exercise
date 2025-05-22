<template>
  <AppContainer>
    <!-- Product table with props and emitted events -->
    <ProductTable
      :rawData="products"
      :searchData="searchData"
      :currentPage="currentPage"
      :pageSize="pageSize"
      :fetchingData="loading"
      :columns="columns"
      @update:searchData="val => searchData = val"
      @update:currentPage="val => currentPage = val"
      @add-new="showAddForm"
      @view-product="showViewDialog"
      @edit-product="showEditForm"
      @delete-product="showDeleteConfirm"
    />

    <!-- Dialog for creating or editing a product -->
    <ProductForm
      :model-value="formDialog"
      :is-editing="isEditing"
      :product="selectedProduct"
      :categories="categories"
      @update:modelValue="formDialog = $event"
      @submit="handleFormSubmit"
      @cancel="formDialog = false"
    />

    <!-- Dialog for viewing product details -->
    <ProductViewDialog
      :model-value="viewDialog"
      :product="selectedProduct"
      @update:modelValue="viewDialog = $event"
    />

    <!-- Delete confirmation dialog -->
    <DeleteConfirmDialog
      :model-value="deleteDialog"
      @update:modelValue="deleteDialog = $event"
      @confirm="confirmDelete"
      @cancel="deleteDialog = false"
    />

    <!-- Snackbar alert component for success/error messages -->
    <AlertSnackbar
      v-model="alert.show"
      :message="alert.message"
      :color="alert.color"
      :icon="alert.icon"
    />
  </AppContainer>
</template>

<script setup>
import AlertSnackbar from "@/components/AlertSnackbar.vue";
import DeleteConfirmDialog from "@/components/DeleteConfirmDialog.vue";
import ProductForm from "@/components/ProductForm.vue";
import ProductTable from "@/components/ProductTable.vue";
import ProductViewDialog from "@/components/ProductViewDialog.vue";
import { onMounted, reactive, ref } from "vue";

// API base URL
const API_URL = "https://fakestoreapi.com/products";

// Reactive state for data and UI
const products = ref([]);
const loading = ref(false);
const searchData = ref("");
const currentPage = ref(1);
const pageSize = 5; // Fixed page size

// Columns definition for table with visibility toggle
const columns = reactive([
  { key: "product", label: "Product", visible: true },
  { key: "price", label: "Price", visible: true },
  { key: "description", label: "Description", visible: true },
  { key: "category", label: "Category", visible: true },
  { key: "rating", label: "Rating", visible: true },
  { key: "actions", label: "Actions", visible: true },
]);

// Form and dialog-related states
const formDialog = ref(false);
const isEditing = ref(false);
const selectedProduct = ref(null);
const viewDialog = ref(false);
const deleteDialog = ref(false);
const deleteId = ref(null);

// Alert message config
const alert = reactive({
  show: false,
  message: "",
  color: "success",
  icon: "tabler-check",
});

// Static product categories
const categories = [
  "men's clothing",
  "women's clothing",
  "jewelery",
  "electronics",
];

// Fetch product list from API
const fetchProducts = async () => {
  loading.value = true;
  try {
    const res = await fetch(API_URL);
    const data = await res.json();
    products.value = data.reverse(); // Reverse to show latest first
  } catch (error) {
    showAlert("Failed to fetch products", "error");
  } finally {
    loading.value = false;
  }
};

// Run on component mount
onMounted(fetchProducts);

// Show alert with message and style
const showAlert = (message, type = "success") => {
  alert.message = message;
  alert.color = type === "success" ? "success" : "error";
  alert.icon = type === "success" ? "tabler-check" : "tabler-alert-circle";
  alert.show = true;
};

// Open form in add mode
const showAddForm = () => {
  isEditing.value = false;
  selectedProduct.value = null;
  formDialog.value = true;
};

// Open form in edit mode with selected product
const showEditForm = (product) => {
  isEditing.value = true;
  selectedProduct.value = { ...product };
  formDialog.value = true;
};

// Show view dialog with product details
const showViewDialog = (product) => {
  selectedProduct.value = { ...product };
  viewDialog.value = true;
};

// Open delete confirmation dialog with selected product ID
const showDeleteConfirm = (id) => {
  deleteId.value = id;
  deleteDialog.value = true;
};

// Handle form submission for both add and edit
const handleFormSubmit = async (product) => {
  formDialog.value = false;
  loading.value = true;

  try {
    let response;
    if (isEditing.value) {
      // Update existing product
      response = await fetch(`${API_URL}/${product.id}`, {
        method: "PUT",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(product),
      });
    } else {
      // Create new product
      response = await fetch(API_URL, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(product),
      });
    }

    if (!response.ok) throw new Error("API Error");

    const savedProduct = await response.json();

    if (isEditing.value) {
      // Update product in local array
      const index = products.value.findIndex((p) => p.id === savedProduct.id);
      if (index !== -1) products.value[index] = savedProduct;
      showAlert("Product updated successfully");
    } else {
      // Add new product to top of list
      products.value.unshift(savedProduct);
      showAlert("Product created successfully");
    }
  } catch (error) {
    showAlert("Failed to save product", "error");
  } finally {
    loading.value = false;
  }
};

// Confirm and perform product deletion
const confirmDelete = async () => {
  if (!deleteId.value) return;

  deleteDialog.value = false;
  loading.value = true;

  try {
    const response = await fetch(`${API_URL}/${deleteId.value}`, {
      method: "DELETE",
    });

    if (!response.ok) throw new Error("Delete failed");

    // Remove product from local list
    products.value = products.value.filter((p) => p.id !== deleteId.value);
    showAlert("Product deleted successfully");

    // Reset to first page after deletion
    currentPage.value = 1;

  } catch (error) {
    showAlert("Failed to delete product", "error");
  } finally {
    loading.value = false;
    deleteId.value = null;
  }
};
</script>