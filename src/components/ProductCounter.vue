<template>
  <form class="form" action="#" method="POST" @submit.prevent="addToCart">
    <b class="item__price" v-if="productData"
      >{{ product.price | numberFormat }} ₽
    </b>

    <fieldset class="form__block">
      <legend class="form__legend">Цвет:</legend>
      <ul class="colors">
        <li
          class="colors__item"
          v-for="color in product.colors"
          :key="color.id"
        >
          <label class="colors__label">
            <input class="colors__radio sr-only" type="radio" />
            <span
              class="colors__value"
              :style="{ 'background-color': color.code }"
            >
            </span>
          </label>
        </li>
      </ul>
    </fieldset>

    <fieldset class="form__block">
      <legend class="form__legend">Объемб в ГБ:</legend>

      <ul class="sizes sizes--primery">
        <li class="sizes__item">
          <label class="sizes__label">
            <input
              class="sizes__radio sr-only"
              type="radio"
              name="sizes-item"
              value="32"
            />
            <span class="sizes__value"> 32gb </span>
          </label>
        </li>
        <li class="sizes__item">
          <label class="sizes__label">
            <input
              class="sizes__radio sr-only"
              type="radio"
              name="sizes-item"
              value="64"
            />
            <span class="sizes__value"> 64gb </span>
          </label>
        </li>
        <li class="sizes__item">
          <label class="sizes__label">
            <input
              class="sizes__radio sr-only"
              type="radio"
              name="sizes-item"
              value="128"
              checked=""
            />
            <span class="sizes__value"> 128gb </span>
          </label>
        </li>
      </ul>
    </fieldset>

    <div class="item__row">
      <div class="form__counter">
        <button
          type="button"
          aria-label="Убрать один товар"
          @click="countMinus"
        >
          <svg width="12" height="12" fill="currentColor">
            <use xlink:href="#icon-minus"></use>
          </svg>
        </button>

        <input type="text" v-model.number="productAmount" />

        <button
          type="button"
          aria-label="Добавить один товар"
          @click="countPlus"
        >
          <svg width="12" height="12" fill="currentColor">
            <use xlink:href="#icon-plus"></use>
          </svg>
        </button>
      </div>

      <button
        class="button button--primery"
        type="submit"
        :disabled="productAddSending"
      >
        В корзину
      </button>
    </div>

    <div v-show="productAdded">Товар добавлен в корзину</div>
    <div v-show="productAddSending">Добавляем товар в корзину</div>
  </form>
</template>

<script>
import numberFormat from "@/helpers/numberFormat";
import axios from 'axios'
import {API_BASE_URL} from "@/config"
import { mapActions } from 'vuex'

export default {
  data() {
    return {
      productAmount: 1,

      productData: null,
      productLoading: false,
      productLoadingFailed: false,

      productAdded: false,
      productAddSending: false
    };
  },
  filters: {
    numberFormat,
  },
  computed: {
    product(){
      return this.productData;
    },
    category(){
      return this.productData.category;
    },
  },
  methods: {
    loadProduct(){
      this.productLoading = true;
      this.productLoadingFailed = false;
      axios.get(API_BASE_URL + '/api/products/' + this.$route.params.id)
        .then(response => this.productData = response.data)
        .catch(() => this.productLoadingFailed = true)
        .then(() => this.productLoading = false)
    },
    ...mapActions(['addProductToCart']),
    addToCart(){
      this.productAdded = false;
      this.productAddSending = true;

      this.addProductToCart({productId: this.product.id, amount: this.productAmount})
       .then(() => {
          this.productAdded = true;
          this.productAddSending = false;
        })
      },
      countPlus() {
        this.productAmount++;
      },
      countMinus() {
        if (this.productAmount > 1) {
        this.productAmount--;
      }
    },
  },
  created(){
    this.loadProduct()
  }
};
</script>