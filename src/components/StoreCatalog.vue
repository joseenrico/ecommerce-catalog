<script>
import SkeletonLoader from "./SkeletonLoader.vue";

export default {
  name: "EcommerceProduct",
  components: {
    SkeletonLoader,
  },

  data() {
    return {
      index: 0,
      products: {},
      loading: false,
      ProductAvailable: false,
    };
  },

  methods: {
    // Function to fetch data from API
    async GetAPI() {
      try {
        const url = `https://fakestoreapi.com/products/${this.index}`;
        const response = await fetch(url);

        // Handle network errors
        if (!response.ok) {
          throw new Error("Request Failed");
        }

        // Parse JSON response
        const result = await response.json();
        return result;
      } catch (error) {
        // Handle error messages
        this.error = error.message;
      }
    },

    // Function to fetch and display the next product
    async nextProduct() {
      this.loading = true;

      // Increment index or reset to 1
      if (this.index !== 20) {
        this.index++;
      } else {
        this.index = 1;
      }

      // Fetch data from API
      const data = await this.GetAPI();

      // Check if the product category is valid
      if (data.category === "men's clothing" || data.category === "women's clothing") {
        this.products = { data };
        this.ProductAvailable = true;
      } else {
        this.ProductAvailable = false;
      }

      this.loading = false;
    },
  },

  mounted() {
    this.nextProduct();
  },
  colorChange() {
    if (data.category === "men's clothing") {
      document.body.style.backgroundColor("--bgmen");
    } else if (data.category === "women's clothing") {
      document.body.style.backgroundColor("--bgwomen");
    } else {
      document.body.style.backgroundColor("--bgunavailable");
    }
  },
};
</script>
<template>
  <div
    class="container"
    :class="
      // Set background color based on product availability
      !ProductAvailable ? 'bg-unvailable' : products.data.category === 'men\'s clothing' ? 'bg-men' : 'bg-women'
    "
  >
    <!-- Loading Indicator -->
    <div v-if="loading">
      <SkeletonLoader />
    </div>
    <!-- Not Available Product -->
    <div class="card-unavailable" :class="ProductAvailable ? 'display-none' : 'display-yes'">
      <div class="row-unavailable">
        <h2 class="unavailable-message">This product is unavailable to show</h2>
        <button class="btn-unavailable" @click="nextProduct()">Next Product</button>
      </div>
    </div>
    <!-- Available Product -->
    <div class="catalog-card" :class="!ProductAvailable ? 'display-none' : 'display-yes'">
      <div class="catalog-left-col">
        <div class="img-catalog"><img :src="products?.data?.image" class="product-img" alt="Product Image" /></div>
      </div>
      <div class="catalog-right-col">
        <div class="catalog-top">
          <h1 class="product-title" :class="products?.data?.category === 'men\'s clothing' ? 'men' : 'women'">
            {{ products?.data?.title }}
          </h1>
          <div class="product-info">
            <!-- Product Category -->
            <p>{{ products?.data?.category }}</p>
            <div class="product-rate">
              <!-- Product Rating -->
              <p class="product-rating">{{ products?.data?.rating?.rate }} / 5</p>
              <!-- Rating Circles -->
              <div class="product-rating-circle">
                <div class="full-circle"></div>
                <div class="full-circle"></div>
                <div class="full-circle"></div>
                <div class="not-full-circle"></div>
                <div class="not-full-circle"></div>
              </div>
            </div>
          </div>
          <hr />
        </div>
        <div class="catalog-middle">
          <p class="product-desc">{{ products?.data?.description }}</p>
        </div>
        <div class="catalog-bottom">
          <div class="catalog-price">
            <h2 class="product-price" :class="products?.data?.category === 'men\'s clothing' ? 'men' : 'women'">${{ products?.data?.price }}</h2>
          </div>
          <div class="catalog-row">
            <button class="btn-available" :class="products?.data?.category === 'men\'s clothing' ? 'bgmen' : 'bgwomen'">Buy Now</button>
            <button @click="nextProduct()" class="btn-available btn-next" :class="products?.data?.category === 'men\'s clothing' ? 'border-men' : 'border-women'">Next Product</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
@import url("../assets/style/page.css");
</style>
