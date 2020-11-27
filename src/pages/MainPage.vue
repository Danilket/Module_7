<template>
  <main class="content container">
    <div class="content__top content__top--catalog">
      <h1 class="content__title">Каталог</h1>
      <span class="content__info"> 152 товара </span>
    </div>

    <div class="content__catalog">
      <ProductFilter
        :color.sync="filterColor"
        :price-from.sync="filterPriceFrom"
        :price-to.sync="filterPriceTo"
        :category-id.sync="filterCategoryId"
      />

      <section class="catalog">
        <div class="loader" v-if="productsLoading">
          <div class="container-loader">
            <div class="item-1"><div></div></div>
            <div class="item-2"><div></div></div>
            <div class="item-3"><div></div></div>
            <div class="item-4"><div></div></div>
            <div class="item-5"><div></div></div>
            <div class="item-6"><div></div></div>
            <div class="item-7"><div></div></div>
            <div class="item-8"><div></div></div>
            <div class="item-9"><div></div></div>
          </div>
        </div>
        <div v-if="productsLoadingFailed">
          Произошла ошибка при загрузке товаров
          <button @click="Products()">Попробовать еще раз</button>
        </div>
        <ProductList :products="products"> </ProductList>
        <BasePagination
          v-model="page"
          :count="countProducts"
          :per-page="productsPerPage"
        />
      </section>
    </div>
  </main>
</template>

<style>
@import "/../css/loader.css";
</style>

<script>
import ProductList from '@/components/ProductList';
import BasePagination from '@/components/BasePagination';
import ProductFilter from '@/components/ProductFilter';
import axios from 'axios';
import { API_BASE_URL } from '@/config';

export default {
  components: { ProductList, BasePagination, ProductFilter },
  data() {
    return {
      filterPriceFrom: 0,
      filterPriceTo: 0,
      filterCategoryId: 0,
      filterColor: 0,

      page: 1,
      productsPerPage: 3,

      productsData: null,

      productsLoading: false,
      productsLoadingFailed: false,
    };
  },
  computed: {
    products() {
      return this.productsData
        ? this.productsData.items.map((product) => {
            return {
              ...product,
              image: product.image.file.url,
            };
          })
        : [];
    },
    countProducts() {
      return this.productsData ? this.productsData.pagination.total : 0;
    },
  },
  methods: {
    Products() {
      this.productsLoading = true;
      this.productsLoadingFailed = false;
      clearTimeout(this.loadProductsTimer);
      this.loadProductsTimer = setTimeout(() => {
        axios
          .get(API_BASE_URL + `/api/products`, {
            params: {
              page: this.page,
              limit: this.productsPerPage,
              categoryId: this.filterCategoryId,
              minPrice: this.filterPriceFrom,
              maxPrice: this.filterPriceTo,
              colorId: this.filterColor,
            },
          })
          .then((response) => (this.productsData = response.data))
          .catch(() => (this.productsLoadingFailed = true))
          .then(() => (this.productsLoading = false));
      }, 1000);
    },
  },
  watch: {
    page() {
      this.Products();
    },
    filterPriceFrom() {
      this.Products();
    },
    filterPriceTo() {
      this.Products();
    },
    filterCategoryId() {
      this.Products();
    },
    filterColor() {
      this.Products();
    },
  },
  created() {
    this.Products();
  },
};
</script>