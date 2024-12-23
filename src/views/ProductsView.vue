<template>
  <v-container class="pa-4">
    <v-row>
      <!-- Sidebar (Categories and Filters) -->
      <v-col cols="12" md="3">
        <v-card>
          <v-card-title>
            <h3>Filter Products</h3>
          </v-card-title>
          <v-card-text>
            <v-select
              v-model="selectedCategory"
              :items="categories"
              outlined
              dense
              label="Category"
            ></v-select>

            <!-- Price Range Filter -->
            <v-row class="mt-4">
              <v-col cols="6">
                <v-text-field
                  v-model="minPrice"
                  label="From"
                  type="number"
                  outlined
                  dense
                  min="0"
                  :max="maxPrice"
                ></v-text-field>
              </v-col>
              <v-col cols="6">
                <v-text-field
                  v-model="maxPrice"
                  label="To"
                  type="number"
                  outlined
                  dense
                  :min="minPrice"
                  max="1000"
                ></v-text-field>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>

      <!-- Products List -->
      <v-col cols="12" md="9">
        <v-row>
          <v-col
            v-for="product in paginatedProducts"
            :key="product.id"
            cols="12" md="4"
          >
            <v-card class="product-card">
              <v-img :src="product.image" aspect-ratio="16/9"></v-img>
              <v-card-title>{{ product.name }}</v-card-title>
              <v-card-subtitle>{{ product.category.name }}</v-card-subtitle>
              <v-card-text>
                <p>{{ product.description }}</p>
                <h3>${{ product.price }}</h3>
              </v-card-text>
              <v-card-actions>
                <v-btn color="primary" text>Add to Cart</v-btn>
                <router-link :to="`/products/${product.id}`">
                  <v-btn color="secondary" text>Details</v-btn>
                </router-link>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>

        <!-- Pagination -->
        <v-pagination
          v-model="page"
          :length="totalPages"
          :total-visible="5"
          class="mt-4"
        ></v-pagination>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "ProductListing",
  data() {
    return {
      categories: ["Electronics", "Furniture", "Clothing", "Books"],
      products: Array.from({ length: 50 }, (_, i) => ({
        id: i + 1,
        name: `Product ${i + 1}`,
        description: `Description for product ${i + 1}`,
        price: (Math.random() * 1000).toFixed(2),
        category: {
          id: Math.floor(Math.random() * 4) + 1,
          name: ["Electronics", "Furniture", "Clothing", "Books"][
            Math.floor(Math.random() * 4)
          ],
        },
        image: "https://via.placeholder.com/300",
      })),
      selectedCategory: null,
      minPrice: 0,
      maxPrice: 1000,
      page: 1,
      itemsPerPage: 9, // Number of products per page
    };
  },
  computed: {
    filteredProducts() {
      return this.products.filter(product => {
        const isCategoryMatch = this.selectedCategory
          ? product.category.id === this.selectedCategory
          : true;
        const isPriceInRange =
          product.price >= this.minPrice && product.price <= this.maxPrice;
        return isCategoryMatch && isPriceInRange;
      });
    },
    totalPages() {
      return Math.ceil(this.filteredProducts.length / this.itemsPerPage);
    },
    paginatedProducts() {
      const start = (this.page - 1) * this.itemsPerPage;
      const end = start + this.itemsPerPage;
      return this.filteredProducts.slice(start, end);
    },
  },
};
</script>

<style scoped>
.product-card {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 500px;
}
</style>
