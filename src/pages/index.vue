<template>
  <AppContainer>
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

    <ProductForm
      :model-value="formDialog"
      :is-editing="isEditing"
      :product="selectedProduct"
      :categories="categories"
      @update:modelValue="formDialog = $event"
      @submit="handleFormSubmit"
      @cancel="formDialog = false"
    />

    <ProductViewDialog
      :model-value="viewDialog"
      :product="selectedProduct"
      @update:modelValue="viewDialog = $event"
    />

    <DeleteConfirmDialog
      :model-value="deleteDialog"
      @update:modelValue="deleteDialog = $event"
      @confirm="confirmDelete"
      @cancel="deleteDialog = false"
    />

    <AlertSnackbar
      v-model="alert.show"
      :message="alert.message"
      :color="alert.color"
      :icon="alert.icon"
    />
  </AppContainer>
</template>

<script setup>
import { ref, reactive, onMounted } from "vue";
import ProductTable from "@/components/ProductTable.vue";
import ProductForm from "@/components/ProductForm.vue";
import ProductViewDialog from "@/components/ProductViewDialog.vue";
import DeleteConfirmDialog from "@/components/DeleteConfirmDialog.vue";
import AlertSnackbar from "@/components/AlertSnackbar.vue";

const API_URL = "https://fakestoreapi.com/products";

const products = ref([]);
const loading = ref(false);
const searchData = ref("");
const currentPage = ref(1);
const pageSize = 5;

const columns = reactive([
  { key: "product", label: "Product", visible: true },
  { key: "price", label: "Price", visible: true },
  { key: "description", label: "Description", visible: true },
  { key: "category", label: "Category", visible: true },
  { key: "rating", label: "Rating", visible: true },
  { key: "actions", label: "Actions", visible: true },
]);

const formDialog = ref(false);
const isEditing = ref(false);
const selectedProduct = ref(null);

const viewDialog = ref(false);
const deleteDialog = ref(false);
const deleteId = ref(null);

const alert = reactive({
  show: false,
  message: "",
  color: "success",
  icon: "tabler-check",
});

const categories = [
  "men's clothing",
  "women's clothing",
  "jewelery",
  "electronics",
];

const fetchProducts = async () => {
  loading.value = true;
  try {
    const res = await fetch(API_URL);
    const data = await res.json();
    products.value = data.reverse();
  } catch (error) {
    showAlert("Failed to fetch products", "error");
  } finally {
    loading.value = false;
  }
};

onMounted(fetchProducts);

const showAlert = (message, type = "success") => {
  alert.message = message;
  alert.color = type === "success" ? "success" : "error";
  alert.icon = type === "success" ? "tabler-check" : "tabler-alert-circle";
  alert.show = true;
};

const showAddForm = () => {
  isEditing.value = false;
  selectedProduct.value = null;
  formDialog.value = true;
};

const showEditForm = (product) => {
  isEditing.value = true;
  selectedProduct.value = { ...product };
  formDialog.value = true;
};

const showViewDialog = (product) => {
  selectedProduct.value = { ...product };
  viewDialog.value = true;
};

const showDeleteConfirm = (id) => {
  deleteId.value = id;
  deleteDialog.value = true;
};

const handleFormSubmit = async (product) => {
  formDialog.value = false;
  loading.value = true;

  try {
    let response;
    if (isEditing.value) {
      
      response = await fetch(`${API_URL}/${product.id}`, {
        method: "PUT",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(product),
      });
    } else {
      
      response = await fetch(API_URL, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(product),
      });
    }

    if (!response.ok) throw new Error("API Error");

    const savedProduct = await response.json();

    if (isEditing.value) {
      const index = products.value.findIndex((p) => p.id === savedProduct.id);
      if (index !== -1) products.value[index] = savedProduct;
      showAlert("Product updated successfully");
    } else {
      products.value.unshift(savedProduct);
      showAlert("Product created successfully");
      

    }
  } catch (error) {
    showAlert("Failed to save product", "error");
  } finally {
    loading.value = false;
  }
};


const confirmDelete = async () => {
  if (!deleteId.value) return;

  deleteDialog.value = false;
  loading.value = true;

  try {
    const response = await fetch(`${API_URL}/${deleteId.value}`, {
      method: "DELETE",
    });

    if (!response.ok) throw new Error("Delete failed");

    products.value = products.value.filter((p) => p.id !== deleteId.value);
    showAlert("Product deleted successfully");

    currentPage.value = 1;

  } catch (error) {
    showAlert("Failed to delete product", "error");
  } finally {
    loading.value = false;
    deleteId.value = null;
  }
};
</script>