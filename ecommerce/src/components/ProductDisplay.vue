<template>
  <div class="app-container" :class="getBackgroundClass()" :style="getBackgroundStyle()">
    <div class="product-card">
      <!-- LOADING STATE - SKELETON -->
      <div v-if="isLoading" class="loading-container">
        <!-- Skeleton Image -->
        <div>
          <div class="skeleton skeleton-image"></div>
        </div>

        <!-- Skeleton Details -->
        <div>
          <div class="skeleton skeleton-text" style="width: 40%;"></div>
          <div class="skeleton skeleton-title"></div>
          <div class="skeleton skeleton-text" style="width: 50%;"></div>
          <div class="skeleton skeleton-price"></div>
          <div class="skeleton skeleton-description"></div>
          <div class="skeleton skeleton-description"></div>
          <div class="skeleton skeleton-description"></div>
          <div class="skeleton skeleton-description" style="width: 80%;"></div>
          <div class="skeleton-buttons">
            <div class="skeleton skeleton-button"></div>
            <div class="skeleton skeleton-button"></div>
          </div>
        </div>
      </div>

      <!-- PRODUCT AVAILABLE STATE -->
      <div v-else-if="categoryType === 'men' || categoryType === 'women'" class="product-content">
        <!-- Product Image -->
        <div class="product-image-container">
          <img :src="product.image" :alt="product.title" class="product-image" />
        </div>

        <!-- Product Details -->
        <div class="product-details">
          <!-- Top Section -->
          <div class="product-top-section">
            <h1 class="product-title" :class="getTitleClass()">
              {{ product.title }}
            </h1>

            <!-- Category and Rating - space between with border bottom -->
            <div class="product-meta">
              <span class="product-category">{{ product.category }}</span>
              <div class="product-rating">
                <span>{{ product.rating?.rate || 0 }}/5</span>
                <span class="rating-stars" :class="getTitleClass()">
                  <span 
                    v-for="star in 5" 
                    :key="star" 
                    class="star" 
                    :class="star <= Math.round(product.rating?.rate || 0) ? 'filled' : 'empty'"
                  ></span>
                </span>
              </div>
            </div>

            <p class="product-description">
              {{ product.description }}
            </p>
          </div>

          <!-- Bottom Section -->
          <div class="product-bottom-section">
            <div class="product-price" :class="getPriceClass()">
              ${{ product.price }}
            </div>

            <div class="product-actions">
              <button :class="getBuyButtonClass()">
                Buy now
              </button>
              <button :class="getNextButtonClass()" @click="nextProduct">
                Next product
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- PRODUCT UNAVAILABLE STATE -->
      <div v-else class="unavailable-container">
        <img 
          src="../assets/sad-face.png" 
          alt="Sad face" 
          class="sad-face-image"
        />
        <p class="unavailable-text">This product is unavailable to show</p>
        <button class="btn-next-gray" @click="nextProduct">
          Next product
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductDisplay',
  data() {
    return {
      index: 1,               // Integer. Mulai dari 1. Penentu ID produk.
      product: {},            // Object. Menyimpan respon JSON dari API.
      isLoading: false,       // Boolean. Mengontrol tampilan Loader vs Konten.
      categoryType: ''        // String. Nilai: 'men', 'women', atau 'unavailable'.
    }
  },
  mounted() {
    // Memanggil fetchProduct pertama kali saat aplikasi dibuka
    this.fetchProduct();
  },
  methods: {
    /**
     * Method: fetchProduct()
     * Algoritma untuk mengambil data produk dari API dan menentukan kategorinya
     */
    async fetchProduct() {
      try {
        // 1. Set loading state
        this.isLoading = true;

        // 2. HTTP GET ke API
        const response = await fetch(`https://fakestoreapi.com/products/${this.index}`);
        const data = await response.json();

        // 3. Logika Kategorisasi (Pattern Recognition)
        if (data.category === "men's clothing") {
          // IF category is "men's clothing"
          this.categoryType = 'men';
          this.product = data;
        } else if (data.category === "women's clothing") {
          // ELSE IF category is "women's clothing"
          this.categoryType = 'women';
          this.product = data;
        } else {
          // ELSE (electronics, jewelery, dll)
          this.categoryType = 'unavailable';
          this.product = {};
        }

        // 4. Set loading selesai
        this.isLoading = false;
      } catch (error) {
        console.error('Error fetching product:', error);
        this.isLoading = false;
        this.categoryType = 'unavailable';
      }
    },

    /**
     * Method: nextProduct()
     * Algoritma untuk berpindah ke produk berikutnya
     */
    nextProduct() {
      // 1. Increment index
      this.index++;

      // 2. Edge Case Handling: Reset ke 1 jika melebihi 20
      if (this.index > 20) {
        this.index = 1;
      }

      // 3. Panggil kembali fetchProduct()
      this.fetchProduct();
    },

    /**
     * Helper methods untuk conditional class binding
     */
    getBackgroundClass() {
      if (this.categoryType === 'men') return 'bg-blue';
      if (this.categoryType === 'women') return 'bg-pink';
      return 'bg-gray';
    },

    getBackgroundStyle() {
      return {};
    },

    getTitleClass() {
      return this.categoryType === 'men' ? 'text-blue' : 'text-purple';
    },

    getPriceClass() {
      return this.categoryType === 'men' ? 'text-blue' : 'text-purple';
    },

    getBuyButtonClass() {
      return this.categoryType === 'men' ? 'btn-buy-blue' : 'btn-buy-purple';
    },

    getNextButtonClass() {
      return this.categoryType === 'men' ? 'btn-next-blue' : 'btn-next-purple';
    }
  }
}
</script>

<style scoped>
/* Component-specific styles jika diperlukan */
/* Semua global styles sudah ada di page.css */
.product-card {
  z-index: 1;
  position: relative;
}
</style>
