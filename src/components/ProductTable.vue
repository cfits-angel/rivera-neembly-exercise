<template>
  <VCard class="mb-6" title="Product List">
    <VCardText>
      <!-- Top bar with search input and action buttons -->
      <div class="d-flex justify-space-between flex-wrap gap-4">
        <!-- Search field -->
        <div class="d-flex gap-4 align-center">
          <div style="width: 350px;">
            <AppTextField
              prepend-inner-icon="tabler-search"
              :model-value="searchData"
              @update:modelValue="val => emit('update:searchData', val)"
              type="text"
              placeholder="Search"
              clearable
            />
          </div>
        </div>

        <!-- Buttons: Add New and Columns toggle menu -->
        <div class="d-flex align-center flex-wrap gap-4">
          <!-- Add New product button -->
          <VBtn prepend-icon="tabler-plus" @click="$emit('add-new')">
            Add New
            <VTooltip activator="parent" location="top">Create new product</VTooltip>
          </VBtn>

          <!-- Columns toggle menu -->
          <div>
            <VMenu v-model="toggleColumnsMenu" transition="scale-transition" max-width="200" offset-y>
              <template #activator="{ props }">
                <VBtn v-bind="props" icon size="x-small" color="primary" variant="outlined">
                  <VTooltip activator="parent" location="top">Toggle to hide columns</VTooltip>
                  <VIcon icon="tabler-filter-cog" />
                </VBtn>
              </template>

              <VList>
                <VListItem
                  v-for="(col, index) in columns"
                  :key="col.key"
                  @click="toggleColumn(col.key)"
                  style="cursor: pointer"
                >
                  <VCheckbox
                    v-model="col.visible"
                    @click.stop
                    :label="col.label"
                    hide-details
                    dense
                  />
                </VListItem>
              </VList>
            </VMenu>
          </div>
        </div>
      </div>
    </VCardText>

    <VDivider />

    <!-- Table -->
    <VTable fixed-header>
      <thead>
        <tr>
          <!-- Conditionally render headers based on visibility -->
          <th v-if="isColumnVisible('product')" class="text-uppercase">Product</th>
          <th v-if="isColumnVisible('price')" class="text-uppercase">Price</th>
          <th v-if="isColumnVisible('description')" class="text-uppercase">Description</th>
          <th v-if="isColumnVisible('category')" class="text-uppercase">Category</th>
          <th v-if="isColumnVisible('rating')" class="text-uppercase">Rating</th>
          <th v-if="isColumnVisible('actions')">Actions</th>
        </tr>
      </thead>

      <tbody>
        <!-- Display paged data -->
        <tr v-for="item in pagedData" :key="item.id">
          <td v-if="isColumnVisible('product')">
            <div class="d-flex pt-3">
              <div class="pt-2 pb-5 pr-5">
                <VAvatar size="large">
                  <VImg v-if="item.image" :src="item.image" />
                </VAvatar>
              </div>
              {{ item?.title }}
            </div>
          </td>
          <td v-if="isColumnVisible('price')">{{ item?.price }}</td>
          <td v-if="isColumnVisible('description')">{{ item?.description }}</td>
          <td v-if="isColumnVisible('category')" class="text-no-wrap">{{ item?.category }}</td>
          <td v-if="isColumnVisible('rating')">{{ item?.rating?.rate || '-' }}</td>
          <td v-if="isColumnVisible('actions')" class="text-no-wrap">
            <!-- Action buttons: View, Edit, Delete -->
            <VBtn
              variant="tonal"
              color="primary"
              icon
              size="25"
              class="rounded"
              @click="$emit('view-product', item)"
            >
              <VTooltip activator="parent" location="top">View product</VTooltip>
              <VIcon size="18" icon="tabler-eye" />
            </VBtn>
            &nbsp;
            <VBtn
              variant="tonal"
              color="default"
              icon
              size="25"
              class="rounded"
              @click="$emit('edit-product', item)"
            >
              <VTooltip activator="parent" location="top">Update product</VTooltip>
              <VIcon size="18" icon="tabler-edit" />
            </VBtn>
            &nbsp;
            <VBtn
              variant="tonal"
              color="error"
              icon
              size="25"
              class="rounded"
              @click="$emit('delete-product', item.id)"
            >
              <VTooltip activator="parent" location="top">Delete product</VTooltip>
              <VIcon size="18" icon="tabler-trash" />
            </VBtn>
          </td>
        </tr>
      </tbody>

      <!-- Pagination and summary -->
      <template v-if="filteredData.length !== 0" #bottom>
        <VDivider />
        <div
          class="d-flex align-center justify-center justify-sm-space-between flex-wrap gap-3 px-6 py-5"
        >
          <p class="text-sm text-disabled mb-0">
            Showing {{ startEntry }} to {{ endEntry }} of {{ filteredData.length }} entries
          </p>

          <VPagination
            :model-value="currentPage"
            @update:model-value="page => emit('update:currentPage', page)"
            :length="totalPages"
            :total-visible="4"
          />
        </div>
      </template>
    </VTable>

    <!-- Loading spinner -->
    <div class="text-center mt-5 mb-5">
      <VProgressCircular v-if="fetchingData" indeterminate color="primary" />
    </div>

    <!-- No data message -->
    <div v-if="filteredData.length === 0 && !fetchingData" class="text-center">
      <br /><br />
      <p class="text-subtitle-1 font-weight-semibold">No Data Available</p>
      <br /><br />
    </div>
  </VCard>
</template>

<script setup>
import { computed, ref, watch } from "vue";

const props = defineProps({
  rawData: {
    type: Array,
    required: true,
  },
  searchData: {
    type: String,
    default: "",
  },
  currentPage: {
    type: Number,
    default: 1,
  },
  pageSize: {
    type: Number,
    default: 5,
  },
  fetchingData: Boolean,
  columns: {
    type: Array,
    required: true,
  },
});

const emit = defineEmits([
  "update:searchData",
  "update:currentPage",
  "add-new",
  "view-product",
  "edit-product",
  "delete-product",
]);

const toggleColumnsMenu = ref(false);

// Keep local reactive copy of columns for toggling visibility
const cols = ref(props.columns);

watch(
  () => props.columns,
  (newCols) => {
    cols.value = newCols;
  }
);

// Returns whether the column is visible based on columns prop
const isColumnVisible = (key) => {
  const col = props.columns.find((c) => c.key === key);
  return col ? col.visible : true;
};

// Filter data based on search input in title, description, or category
const filteredData = computed(() => {
  if (!props.searchData) return props.rawData;

  const search = props.searchData.toLowerCase();
  return props.rawData.filter((item) => {
    return (
      (item.title && item.title.toLowerCase().includes(search)) ||
      (item.description && item.description.toLowerCase().includes(search)) ||
      (item.category && item.category.toLowerCase().includes(search))
    );
  });
});

// Calculate total pages for pagination
const totalPages = computed(() =>
  Math.ceil(filteredData.value.length / props.pageSize)
);

// Get current page data slice
const pagedData = computed(() => {
  const start = (props.currentPage - 1) * props.pageSize;
  return filteredData.value.slice(start, start + props.pageSize);
});

// Calculate starting entry number of current page
const startEntry = computed(() => {
  if (filteredData.value.length === 0) return 0;
  return (props.currentPage - 1) * props.pageSize + 1;
});

// Calculate ending entry number of current page
const endEntry = computed(() => {
  return Math.min(props.currentPage * props.pageSize, filteredData.value.length);
});

// Toggle column visibility on checkbox click
const toggleColumn = (key) => {
  const col = props.columns.find((c) => c.key === key);
  if (col) {
    col.visible = !col.visible;
  }
};
</script>