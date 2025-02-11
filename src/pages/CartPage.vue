<template>
  <q-layout>
    <q-page-container class="cart-main-container">
      <div class="header">
        <h4>My Cart</h4>
        <q-btn label="Back to Products" class="back-button" @click="goToProducts" />
      </div>
      <div v-if="cartItems.length" class="cart-container">
        <div v-for="(item, index) in cartItems" :key="index" class="my-card">
          <div>
            <div class="item-text text-h6">{{ item.name }}</div>
            <div class="price-text text-subtitle1">${{ item.price }}</div>
          </div>
          <q-icon name="delete" size="25px" class="delete-icon" @click="removeFromCart(index)" />
        </div>
        <div class="total-price">Total: {{ totalPrice }}</div>
        <q-btn label="Place Order" class="place-order-button" color="primary" @click="placeOrder" />
      </div>
      <div v-else>
        <p>Your cart is empty!</p>
      </div>
    </q-page-container>
  </q-layout>
</template>

<script lang="ts">
import axios from 'axios'

interface CartItem {
  name: string
  price: string
  image?: string
}

export default {
  data() {
    return {
      cartItems: [] as CartItem[],
    }
  },
  computed: {
    totalPrice(): string {
      const total = this.cartItems.reduce((sum, item) => {
        const price = parseFloat(item.price.replace(/[^\d.]/g, '')) || 0
        return sum + price
      }, 0)
      return `$${total.toFixed(2)}`
    },
    numericTotalPrice(): number {
      return this.cartItems.reduce((sum, item) => {
        const price = parseFloat(item.price.replace(/[^\d.]/g, '')) || 0
        return sum + price
      }, 0)
    },
  },
  mounted() {
    const accessToken = localStorage.getItem('access_token')
    if (!accessToken) {
      alert('Please log in to view your cart.')
      return
    }

    const cartKey = `cart_${accessToken}`
    this.cartItems = JSON.parse(localStorage.getItem(cartKey) || '[]') as CartItem[]
  },
  methods: {
    removeFromCart(index: number) {
      const accessToken = localStorage.getItem('access_token')
      if (!accessToken) return

      const cartKey = `cart_${accessToken}`
      this.cartItems.splice(index, 1)
      localStorage.setItem(cartKey, JSON.stringify(this.cartItems))
    },
    goToProducts() {
      this.$router.push('/products')
    },
    async placeOrder() {
      const accessToken = localStorage.getItem('access_token')
      const email = localStorage.getItem('email')

      if (!accessToken || !email) {
        this.$q.notify({
          type: 'negative',
          message: 'Please log in to place an order.',
          position: 'top',
          timeout: 3000,
        })
        return
      }

      const orderData = {
        email: email,
        amount: this.numericTotalPrice, // Assuming amount is in cents
        items: this.cartItems.map((item) => item.name).join(', '),
      }

      try {
        await axios.post('https://chrono-backend.onrender.com/orders', orderData, {
          headers: {
            Authorization: `Bearer ${accessToken}`,
          },
        })
        this.$q.notify({
          type: 'positive',
          message: 'Order placed successfully!',
          position: 'top',
          timeout: 3000,
        })
        this.cartItems = []
        localStorage.removeItem(`cart_${accessToken}`)
      } catch (error) {
        console.error('Error placing order:', error)
        this.$q.notify({
          type: 'negative',
          message: 'Failed to place the order. Please try again later.',
          position: 'top',
          timeout: 3000,
        })
      }
    },
  },
}
</script>

<style>
.cart-main-container {
  width: 100%;
  padding: 50px;
}
.header {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.cart-container {
  width: 100%;
}
.my-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 800px;
  padding: 20px;
  border-bottom: 1px solid #1d1d1f20;
  margin-left: auto;
  margin-right: auto;
}
.delete-icon {
  cursor: pointer;
  color: #f44336;
}
.total-price {
  text-align: center;
  font-size: 2rem;
  font-weight: 500;
  margin-top: 10px;
}
.place-order-button {
  display: block;
  margin: 20px auto;
}
.back-button {
  border: none;
  background: none;
  height: 40px;
}
@media only screen and (max-width: 768px) {
  .cart-main-container {
    padding: 20px;
  }
  .back-button {
    font-size: 0.6rem;
  }
  .my-card {
    width: 100%;
  }
  .item-text {
    font-size: 1rem;
  }
  .price-text {
    font-size: 0.8rem;
  }
  .total-price {
    font-size: 1.4rem;
  }
}
</style>
