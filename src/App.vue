<template>
  <div id="app">
    <header>
      <logo class="logo"></logo>

      <div class="cart__wrapper" v-if="cart.products.length > 0">
        <label class="cartToggleLabel" for="cartToggle" v-bind:data-cart-qty="cart.products.length"></label>

        <input id="cartToggle" type="checkbox">

        <div class="cart">
          <ul class="cart__products">
            <cart-item
              v-for="(cartItem, index) of cart.products"
              v-bind:key="index"
              v-bind:product="cartItem"
              v-on:removeFromCart="removeFromCart(cartItem)"
            ></cart-item>
          </ul>

          <div class="cart__footer">
            <h2>subtotal</h2>
            <div class="installments">{{ cart.subtotal.installments }}x <strong>{{ cart.subtotal.installmentValue | currency }}</strong></div>
            <div class="cash">ou <em>{{ cart.subtotal.value | currency }}</em> à vista</div>
          </div>
        </div>
      </div>
    </header>

    <!-- product list -->
    <div class="ui products">
      <product
        v-for="(product, index) of products" v-bind:key="index"
        v-bind:product="product"
        v-on:addToCart="addToCart(product)"
        v-on:toggleFavorite="toggleFavorite(product)"
      >

        <!-- product tags -->
        <template slot="tags" v-if="product.tags">
          <tag v-for="(tag, index) of product.tags" v-bind:key="index">{{ tag.text }}</tag>
        </template>
      </product>
    </div>
  </div>
</template>

<style lang="scss">
@import 'reset';

body {
  font-family: 'Montserrat', sans-serif;
  background-color: #f3f3f3;
  font-size: 16px;
}

#app { padding-top: 90px }

header {
  position: absolute;
  top: 0;
  width: 100%;
  background: #ffcd00;
  z-index: 100;
}

.logo { padding: 10px 0 5px 17px }

.cartToggleLabel {
  position: absolute;
  top: 27px;
  right: 27px;
  font-size: 2rem;
  font-weight: 900;
  padding-left: 1.25em;

  &:before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 1em;
    height: 0.125em;
    border-top: 0.375em double #000;
    border-bottom: 0.125em solid #000;
  }

  &:after {
    content: attr(data-cart-qty);
    position: absolute;
    top: 9px;
    left: -12.5px;
    display: inline-block;
    width: 25.5px;
    height: 25.5px;
    border-radius: 50%;
    background-color: #d62a33;
    color: white;
    font-size: 1.3rem;
    text-align: center;
    vertical-align: middle;
    line-height: 25.5px;
  }
}

#cartToggle, .cart { display: none }
#cartToggle:checked + .cart { display: block }

.cart__footer {
  text-align: right;
  padding: 30px 20px;
  font-weight: 700;

  h2 {
    border-bottom: 1px solid black;
    padding-bottom: 5px;
  }

  .installments,
  .cash
  { line-height: 1.5em }

  .installments { padding-top: 15px }
}

.ui.products {
  margin: 10px 7px;

  .product + .product { margin-top: 10px }
}
</style>

<script>
import {db} from './firebase'

import Vue from 'vue'
import VueFire from 'vuefire'

import CartItem from '@/components/CartItem'
import Logo from '@/components/Logo'
import Product from '@/components/Product'
import Tag from '@/components/Tag'

Vue.use(VueFire)

export default {
  name: 'app',

  components: { CartItem, Logo, Product, Tag },
  data () {
    return {
      cart: {
        subtotal: {
          installments: 1,
          installmentValue: 0,
          value: 0
        },
        products: []
      },
      products: {}
    }
  },

  firebase: {
    products: { source: db.ref('products') }
  },

  methods: {
    addToCart (product) {
      if (this.cart.products.indexOf(product) === -1) this.cart.products.push(product)
      this.updateTotals()
    },
    removeFromCart (product) {
      this.cart.products.splice(this.cart.products.indexOf(product), 1)
      this.updateTotals()
    },
    toggleFavorite (product) {
      let index = this.products.indexOf(product)
      if (index !== -1) this.products[index].isFavorite = !this.products[index].isFavorite
    },
    updateTotals () {
      let installments = 1
      let subtotal = 0

      for (let product of this.cart.products) {
        if (product.price.installments > installments) {
          installments = product.price.installments
        }

        subtotal += product.price.value
      }

      this.cart.subtotal.installments = installments
      this.cart.subtotal.installmentValue = subtotal / installments
      this.cart.subtotal.value = subtotal
    }
  }
}
</script>
