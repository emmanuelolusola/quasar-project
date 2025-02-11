<template>
  <q-layout>
    <q-page-container class="main-container">
      <div class="q-pa-md" style="max-width: 500px">
        <q-form @submit="onSubmit" @reset="onReset" class="form-container">
          <h4>Sign in</h4>
          <q-input
            filled
            v-model="email"
            label="Enter email address"
            class="input-container"
            lazy-rules
            :rules="[(val) => (val && val.length > 0) || 'Please type something']"
          />

          <q-input
            filled
            type="password"
            v-model="password"
            label="Enter password"
            hint="*must contain at least 8 character"
            class="input-container"
            lazy-rules
            :rules="[(val) => (val && val.length > 0) || 'Please type your password']"
          />

          <p class="text-sign">
            Don't have an account?
            <span class="sign" @click="goToSignup">Sign up</span>
          </p>

          <div class="submit-container">
            <q-btn label="Log in" type="submit" color="primary" />
          </div>
        </q-form>
      </div>
    </q-page-container>
  </q-layout>
</template>

<script lang="ts">
import { useQuasar } from 'quasar'
import { ref } from 'vue'
import { useRouter } from 'vue-router'

export default {
  setup() {
    const $q = useQuasar()
    const router = useRouter()

    const email = ref('')
    const password = ref('')

    const goToSignup = async () => {
      try {
        await router.push('/register')
      } catch (err) {
        console.error('Navigation error:', err)
      }
    }

    const goToProducts = async () => {
      try {
        await router.push('/products')
      } catch (err) {
        console.error('Navigation error:', err)
      }
    }

    const onSubmit = async () => {
      try {
        const response = await fetch('https://chrono-backend.onrender.com/auth/login', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            email: email.value,
            password: password.value,
          }),
        })

        if (response.ok) {
          const data = await response.json()
          const { access_token } = data

          // Store the access_token and email in localStorage
          localStorage.setItem('access_token', access_token)
          localStorage.setItem('email', email.value)

          // Navigate to /products page
          await goToProducts()

          $q.notify({
            color: 'green-4',
            textColor: 'white',
            icon: 'cloud_done',
            message: 'Login successful!',
          })
        } else {
          const err = 'Invalid Login'
          $q.notify({
            color: 'red-5',
            textColor: 'white',
            icon: 'error',
            message: err,
          })
        }
        // eslint-disable-next-line @typescript-eslint/no-unused-vars
      } catch (error) {
        $q.notify({
          color: 'red-5',
          textColor: 'white',
          icon: 'error',
          message: 'An error occurred. Please try again later.',
        })
      }
    }

    const onReset = () => {
      email.value = ''
      password.value = ''
    }

    return {
      email,
      password,
      goToSignup,
      goToProducts,
      onSubmit,
      onReset,
    }
  },
}
</script>

<style>
.main-container {
  background-color: #1d1d1f;
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
.form-container {
  background-color: #f5f5f7;
  padding: 40px;
  border-radius: 20px;
}
.input-container {
  width: 400px;
  margin-bottom: 10px;
}
.submit-container {
  margin-top: 1rem;
}
h4 {
  margin-bottom: 2rem;
  margin-top: 0;
}
.text-sign {
  text-align: right;
  margin-top: 2rem;
}
.sign {
  color: #1976d2;
  text-decoration: underline;
  cursor: pointer;
}
.next-btn {
  cursor: pointer;
  color: #1976d2;
  text-decoration: underline;
}
@media only screen and (max-width: 768px) {
  .input-container {
    width: 280px;
  }
  .form-container {
    padding: 20px;
  }
  h4 {
    margin-top: 1rem;
    margin-bottom: 1rem;
    font-size: 1.4rem;
  }
}
</style>
