<template>
  <div v-if="$store.state.productLoading">Загрузка корзины...</div>
  <div v-else-if="$store.state.productLoadingFailed">
    Произошла ошибка при загрузке корзины
  </div>
  <main class="content container" v-else>
    <div class="content__top">
      <ul class="breadcrumbs">
        <li class="breadcrumbs__item">
          <router-link class="breadcrumbs__link" :to="{ name: 'main' }">
            Каталог
          </router-link>
        </li>
        <li class="breadcrumbs__item">
          <a class="breadcrumbs__link"> Корзина </a>
        </li>
      </ul>

      <h1 class="content__title">Корзина</h1>
      <span class="content__info"> {{ totalCounter }} товара </span>
    </div>

    <section class="cart">
      <form class="cart__form form" action="#" method="POST">
        <div class="cart__field">
          <ul class="cart__list">
            <CartItem
              v-for="item in products"
              :key="item.productId"
              :item="item"
            />
          </ul>
        </div>

        <div class="cart__block">
          <p class="cart__desc">
            Мы&nbsp;посчитаем стоимость доставки на&nbsp;следующем этапе
          </p>
          <p class="cart__price">
            Итого: <span>{{ totalPrice | numberFormat }} ₽</span>
          </p>

          <button class="cart__button button button--primery" type="submit">
            Оформить заказ
          </button>
        </div>
      </form>
    </section>
  </main>
</template>

<script>
import numberFormat from "@/helpers/numberFormat"
import { mapGetters, mapMutations, mapActions } from "vuex"
import CartItem from "@/components/CartItem"
import axios from 'axios'
import {API_BASE_URL} from "@/config"

export default {
  data() {
    return{
      productData: null,
      productLoading: false,
      productLoadingFailed: false
    }
  },
  filters: {numberFormat},
  components: {CartItem},
  computed: {
    ...mapGetters({products: 'cartDetailProducts', totalPrice: 'cartTotalPrice', totalCounter: 'cartCounter'}),
  },
  methods: {
    //  ...mapActions(['loadCart']),
  },

};
</script>