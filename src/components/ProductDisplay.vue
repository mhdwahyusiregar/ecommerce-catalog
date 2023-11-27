<template>
  <div class="container">
    <div v-if="isLoading" class="card">
      <div class="product-container">
        <div class="loader-container">
          <div class="loader"></div>
        </div>
      </div>
    </div>

    <div v-else class="container" :class="getContainerClasses()">
      <div class="overlay">
        <img src="../assets/bg-shape.svg" alt="background overlay" />
      </div>
      <section class="card">
        <div v-if="!isProductAvailable" class="product-unavailable">
          <div class="overlay">
            <img src="../assets/bg-sad.svg" alt="unavailable product" />
          </div>
          <div class="product-details">
            <p>This product is unavailable to show</p>
            <div class="button-container">
              <button
                type="button"
                @click="getSingleProduct()"
                class="btn-next"
              >
                Next Product
              </button>
            </div>
          </div>
        </div>
        <div v-else class="product-container">
          <div class="product-image">
            <img :src="product.data.image" alt="product ecommerce" />
          </div>
          <div class="product-details">
            <div class="product-title">
              <h3 :class="getTitleClasses()">
                {{ product.data.title }}
              </h3>
              <div class="product-sub-title">
                <span>{{ product.data.category }}</span>
                <div class="product-rating">
                  <span>{{ product.data.rating.rate }}/5</span>
                  <div class="rating-circles">
                    <span
                      v-for="circle in 5"
                      :key="circle"
                      :class="getRatingCircleClasses(circle)"
                    ></span>
                  </div>
                </div>
              </div>
              <div class="description">
                <p>{{ product.data.description }}</p>
              </div>
            </div>
            <div class="price-color">
              <span :class="getPriceClasses()">
                ${{ product.data.price }}
              </span>
              <div class="button-container">
                <button type="button" :class="getBuyButtonClasses()">
                  Buy Now
                </button>
                <button
                  type="button"
                  @click="getSingleProduct()"
                  :class="getNextButtonClasses()"
                >
                  Next Product
                </button>
              </div>
            </div>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductDisplay',
  data() {
    return {
      isLoading: false,
      id: 0,
      isProductAvailable: false,
      product: {},
    };
  },
  methods: {
    async dataAPI() {
      const response = await fetch(
        `https://fakestoreapi.com/products/${this.id}`,
      );
      const result = await response.json();
      return result;
    },
    async getSingleProduct() {
      this.isLoading = true;

      this.id == 20 ? (this.id = 1) : this.id++;

      let data = await this.dataAPI();
      if (
        data.category === "men's clothing" ||
        data.category === "women's clothing"
      ) {
        this.product = { data };
        this.isProductAvailable = true;
      } else {
        this.isProductAvailable = false;
      }

      this.isLoading = false;
    },
    getContainerClasses() {
      return {
        'bg-light-gray': !this.isProductAvailable,
        'bg-light-blue font-blue':
          this.isProductAvailable &&
          this.product.data.category === "men's clothing",
        'bg-light-pink font-magenta':
          this.isProductAvailable &&
          this.product.data.category !== "men's clothing",
      };
    },
    getTitleClasses() {
      return {
        'font-navy title': this.product.data.category === "men's clothing",
        'font-pink-dark title': this.product.data.category !== "men's clothing",
      };
    },
    getRatingCircleClasses(circle) {
      return {
        circle: true,
        'bg-navy':
          this.product.data.category === "men's clothing" &&
          circle <= Math.round(this.product.data.rating.rate),
        'bg-pink-dark':
          this.product.data.category !== "men's clothing" &&
          circle <= Math.round(this.product.data.rating.rate),
      };
    },
    getPriceClasses() {
      return {
        'font-navy': this.product.data.category === "men's clothing",
        'font-pink-dark': this.product.data.category !== "men's clothing",
        price: true,
      };
    },
    getBuyButtonClasses() {
      return {
        'bg-navy': this.product.data.category === "men's clothing",
        'bg-pink-dark': this.product.data.category !== "men's clothing",
        'btn-buy': true,
      };
    },
    getNextButtonClasses() {
      return {
        'border-btn-navy font-navy':
          this.product.data.category === "men's clothing",
        'border-btn-pink-dark font-pink-dark':
          this.product.data.category !== "men's clothing",
        'btn-next': true,
      };
    },
  },
  mounted() {
    this.getSingleProduct();
  },
};
</script>

<style>
@import '../assets/style/page.css';
</style>
