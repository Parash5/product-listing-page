<template>
  <div class="container">
    <div class="header">
      <ul class="category-list">
        <li class="category-item" @click="displayAllProducts">All Items</li>
        <li class="category-item" @click="displayProductByCategory('electronics')">Electronics</li>
        <li class="category-item" @click="displayProductByCategory('jewelery')">Jewelry</li>
        <li class="category-item" @click="displayProductByCategory('men\'s clothing')">
          Men's Clothing
        </li>
        <li class="category-item" @click="displayProductByCategory('women\'s clothing')">
          Women's Clothing
        </li>
      </ul>

      <!-- ------------------------------------------------------------- -->
      <div class="search-container">
        <input
          v-model="searchQuery"
          class="search-input"
          placeholder="Search..."
          type="text"
          @input="handleInput"
          @keypress.enter="searchProducts"
        />
        <button class="search-button" @click="searchProducts">
          <i class="fas fa-search"></i>
        </button>
      </div>
      <!-- ------------------------------------------------------------- -->
      <div class="sort-container">
        <select v-model="sortOption" @change="sortProducts">
          <option value="bestMatch">Best Match</option>
          <option value="priceLowToHigh">Price: Low to High</option>
          <option value="priceHighToLow">Price: High to Low</option>
        </select>
      </div>
    </div>
    <div class="autocomplete" v-if="showAutocomplete" ref="autocomplete">
      <div class="autocomplete-dropdown">
        <div
          class="autocomplete-item"
          v-for="suggestion in autocompleteSuggestions"
          :key="suggestion"
          @click="selectSuggestion(suggestion)"
        >
          {{ suggestion }}
        </div>
      </div>
    </div>
    <!-- ------------------------------------------------------------- -->
    <div class="product-list" v-if="filteredProducts.length">
      <div class="product-card" v-for="product in filteredProducts" :key="product.id">
        <img class="product-image" :src="product.image" :alt="product.title" />
        <div class="product-details">
          <h3 class="product-title">{{ product.title }}</h3>
          <p class="product-category">{{ product.category }}</p>
          <p class="product-price">$ {{ product.price }}</p>
          <p class="product-rating">Rating: {{ product.rating.rate }}</p>
          <button class="add-to-cart-button" @click="addToCart(product)">Add to Cart</button>
          <p v-if="product.addedToCart" class="success-message">Added to cart!</p>
        </div>
      </div>
    </div>
    <div v-else>No products found.</div>
    <div class="footer">
      <p>&copy; 2024 Parasshopping. All rights reserved.</p>
    </div>
  </div>
</template>
<!-- ------------------------------------------------------------- -->
<script>
import axios from 'axios'

export default {
  name: 'ProductListingPage',
  data() {
    return {
      products: [],
      filteredProducts: [],
      searchQuery: '',
      sortOption: 'bestMatch',
      showAutocomplete: false,
      autocompleteSuggestions: []
    }
  },
  methods: {
    async fetchProducts() {
      try {
        const response = await axios.get('https://fakestoreapi.com/products')
        this.products = response.data.map((product) => ({ ...product, addedToCart: false }))
        this.filteredProducts = this.products
      } catch (error) {
        console.error('Error fetching products:', error)
      }
    },
    displayAllProducts() {
      this.filteredProducts = this.products
    },
    displayProductByCategory(category) {
      this.filteredProducts = this.products.filter((product) => product.category === category)
    },
    searchProducts() {
      this.filteredProducts = this.products.filter((product) =>
        product.title.toLowerCase().includes(this.searchQuery.toLowerCase())
      )
    },
    sortProducts() {
      switch (this.sortOption) {
        case 'priceLowToHigh':
          this.filteredProducts.sort((a, b) => a.price - b.price)
          break
        case 'priceHighToLow':
          this.filteredProducts.sort((a, b) => b.price - a.price)
          break
        default:
          break
      }
    },
    async fetchSuggestions(query) {
      try {
        const response = await axios.get(`https://fakestoreapi.com/products?title_like=${query}`)
        this.autocompleteSuggestions = response.data.slice(0, 5).map((product) => product.title)
        this.showAutocomplete = true
      } catch (error) {
        console.error('Error fetching autocomplete suggestions:', error)
      }
    },
    handleInput() {
      if (this.searchQuery.length > 0) {
        this.fetchSuggestions(this.searchQuery)
      } else {
        this.showAutocomplete = false
      }
    },
    selectSuggestion(suggestion) {
      this.searchQuery = suggestion
      this.showAutocomplete = false
      this.searchProducts()
    },
    addToCart(product) {
      const index = this.filteredProducts.findIndex((p) => p.id === product.id)
      if (index !== -1) {
        this.filteredProducts[index].addedToCart = true
      }
    },
    hideAutocompleteOnClick(event) {
      if (!this.$refs.autocomplete.contains(event.target)) {
        this.showAutocomplete = false
      }
    },
    handleKeyPress(event) {
      if (event.key === 'Enter') {
        this.searchProducts()
      }
    }
  },
  mounted() {
    this.fetchProducts()

    document.body.addEventListener('click', this.hideAutocompleteOnClick)
    document.body.addEventListener('keypress', this.handleKeyPress)
  },
  beforeUnmount() {
    document.body.removeEventListener('click', this.hideAutocompleteOnClick)
    document.body.removeEventListener('keypress', this.handleKeyPress)
  }
}
</script>
<style src="@/components/ProductListingPage.css" scoped></style>
<style src="@/components/Media@.css" scoped></style>
