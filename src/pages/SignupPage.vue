<template>
  <q-layout>
    <q-page-container class="main-container">
      <div class="q-pa-md" style="max-width: 500px">
        <q-form @submit="onSubmit" @reset="onReset" class="form-container">
          <h4>Sign up</h4>
          <q-input
            filled
            v-model="name"
            label="Full Name"
            class="input-container"
            lazy-rules
            :rules="[(val) => (val && val.length > 0) || 'Please type something']"
          />

          <q-input
            filled
            v-model="email"
            label="Email Address"
            class="input-container"
            lazy-rules
            :rules="[(val) => (val && val.length > 0) || 'Please type something']"
          />

          <q-input
            filled
            v-model="password"
            type="password"
            label="Create Password"
            hint="*must contain at least 8 character"
            class="input-container"
            lazy-rules
            :rules="[(val) => (val && val.length >= 8) || 'Password must be at least 8 characters']"
          />

          <p class="text-sign">
            Already have an account? <span class="sign" @click="goToSignIn">Sign in</span>
          </p>

          <div class="submit-container">
            <q-btn label="Register" type="submit" color="primary" />
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

    const name = ref('')
    const email = ref('')
    const password = ref('')

    const goToSignIn = async () => {
      try {
        await router.push('/login')
      } catch (err) {
        console.error('Navigation error:', err)
      }
    }

    const onSubmit = async () => {
      try {
        const response = await fetch('https://chrono-backend.onrender.com/users', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            fullName: name.value,
            email: email.value,
            password: password.value,
          }),
        })

        if (!response.ok) {
          throw new Error(`Error: ${response.status}`)
        }

        const data = await response.json()
        $q.notify({
          color: 'green-4',
          textColor: 'white',
          icon: 'cloud_done',
          message: 'Registration successful!',
        })

        console.log('Response data:', data)

        // Reset the form
        onReset()

        // Redirect to login
        await router.push('/login')
      } catch (error) {
        console.error('Submission error:', error)
        $q.notify({
          color: 'red-5',
          textColor: 'white',
          icon: 'error',
          message: 'Registration failed. Please try again.',
        })
      }
    }

    const onReset = () => {
      name.value = ''
      email.value = ''
      password.value = ''
    }

    return {
      name,
      email,
      password,
      goToSignIn,
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
