<template>
  <q-layout>
    <q-page-container class="product-main-container">
      <div class="product-header">
        <h4>Products</h4>
        <div class="buttons">
          <q-btn flat to="/cart" class="cart-btn">Cart</q-btn>
          <q-btn flat @click="logout" class="logout-btn">Log Out</q-btn>
        </div>
      </div>
      <div class="product-cards-container q-pa-md row items-start q-gutter-md">
        <q-card v-for="(product, index) in products" :key="index" class="product-card">
          <q-img :src="product.image">
            <div class="absolute-bottom">
              <div class="text-h6">{{ product.name }}</div>
              <div class="text-subtitle1">${{ product.price }}</div>
            </div>
          </q-img>

          <q-card-actions>
            <q-btn flat @click="addToCart(product)">Add to cart +</q-btn>
          </q-card-actions>
        </q-card>
      </div>
    </q-page-container>
  </q-layout>
</template>

<script lang="ts">
import { useQuasar } from 'quasar'
import yeezy from 'src/assets/yeezy.jpg'
import airjordan from 'src/assets/airjordan.jpeg'
import adidas from 'src/assets/adidas.jpg'
import airmax270 from 'src/assets/airmax270.jpg'
import rsx from 'src/assets/rsx.jpg'
import reebok from 'src/assets/reebok.jpg'
import nb574 from 'src/assets/nb574.jpg'
import converse from 'src/assets/converse.jpg'
import vans from 'src/assets/vans.jpg'
import asics from 'src/assets/asics.jpg'
import fila from 'src/assets/fila.jpeg'
import saucony from 'src/assets/saucony.jpg'
import hoka from 'src/assets/hoka.jpg'
import brooks from 'src/assets/brooks.jpg'
import hovr from 'src/assets/hovr.jpg'
import mizuno from 'src/assets/mizuno.jpg'

interface Product {
  name: string
  price: string
  image: string
}

export default {
  setup() {
    const $q = useQuasar()

    const products: Product[] = [
      { name: 'Yeezy Foam Runner (Clogs)', price: '37,000', image: yeezy },
      { name: 'Air Jordan 1', price: '50,000', image: airjordan },
      { name: 'Adidas Ultraboost', price: '45,000', image: adidas },
      { name: 'Nike Air Max 270', price: '55,000', image: airmax270 },
      { name: 'Puma RS-X', price: '40,000', image: rsx },
      { name: 'Reebok Classic', price: '35,000', image: reebok },
      { name: 'New Balance 574', price: '38,000', image: nb574 },
      { name: 'Converse Chuck Taylor', price: '30,000', image: converse },
      { name: 'Vans Old Skool', price: '32,000', image: vans },
      { name: 'Asics Gel Lyte III', price: '42,000', image: asics },
      { name: 'Fila Disruptor', price: '28,000', image: fila },
      { name: 'Saucony Jazz Original', price: '39,000', image: saucony },
      { name: 'Hoka One One', price: '50,000', image: hoka },
      { name: 'Brooks Ghost 14', price: '48,000', image: brooks },
      { name: 'Under Armour HOVR', price: '47,000', image: hovr },
      { name: 'Mizuno Wave Rider', price: '46,000', image: mizuno },
    ]

    const addToCart = (product: Product) => {
      const accessToken = localStorage.getItem('access_token')
      if (!accessToken) {
        $q.notify({
          type: 'warning',
          message: 'Please log in to add items to your cart.',
          timeout: 3000,
        })
        return
      }

      const cartKey = `cart_${accessToken}`
      const cart: Product[] = JSON.parse(localStorage.getItem(cartKey) || '[]')

      cart.push(product)
      localStorage.setItem(cartKey, JSON.stringify(cart))

      $q.notify({
        type: 'positive',
        message: 'Product added to cart!',
        timeout: 3000,
      })
    }
    const logout = () => {
      localStorage.removeItem('access_token')
      $q.notify({
        type: 'positive',
        message: 'Logged out successfully!',
        timeout: 3000,
      })
      window.location.reload()
    }

    return { products, addToCart, logout }
  },
}
</script>

<style>
.product-main-container {
  width: 100%;
  padding: 50px;
}
.product-cards-container {
  width: 100%;
  display: flex;
  justify-content: center;
}
.product-card {
  width: 100%;
  max-width: 350px;
}
.product-header {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.buttons {
  display: flex;
  gap: 10px;
}
.cart-btn,
.logout-btn {
  background: none;
  height: 50px;
}
@media only screen and (max-width: 768px) {
  .product-main-container {
    padding: 20px;
  }
  .buttons {
    gap: 5px;
  }
  .cart-btn,
  .logout-btn {
    border: 1px solid #1d1d1f;
    height: 30px;
    border-radius: 0;
  }
}
</style>
