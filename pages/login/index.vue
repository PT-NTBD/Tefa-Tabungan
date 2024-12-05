<template>
  <div class="tinggi container-fluid">
    <div class="row justify-content-center">
      <div class="col-11 col-sm-8 col-md-6 col-lg-4">
        <div class="container shadow p-3" style="background-color: #D9D9D9;">
          <form>
            <div class="mb-3">
              <h2 class="text-center">Login</h2>
            </div>

            <!-- Email Field -->
            <div class="mb-3 position-relative">
              <input 
                v-model="email" 
                type="text" 
                class="form-control icon"  
                placeholder="Username">
              <i class="bi bi-person-fill position-absolute top-50 end-0 translate-middle-y me-3"></i>
            </div>

            <!-- Password Field -->
            <div class="mb-4 position-relative">
              <input 
                v-model="password" 
                :type="isPasswordVisible ? 'text' : 'password'" 
                class="form-control icon-gembok" 
                placeholder="Password">
              <i 
                @click="togglePasswordVisibility"
                :class="isPasswordVisible ? 'bi bi-eye-fill' : 'bi bi-eye-slash-fill'" 
                class="position-absolute top-50 end-0 translate-middle-y me-3"
                style="cursor: pointer;"></i>
            </div>

            <!-- Error Message -->
            <div v-if="errorMessage" class="mb-3 text-danger text-center">
              {{ errorMessage }}
            </div>

            <!-- Login Button -->
            <div class="mb-3">
              <button @click="handleLogin" type="button" class="button w-100">Masuk</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  layout: 'login',
})

const client = useSupabaseClient()
const email = ref("")
const password = ref("")
const isPasswordVisible = ref(false)
const errorMessage = ref("")  // Store error message

// Function to handle login
async function handleLogin() {
  const { data, error } = await client.auth.signInWithPassword({
    email: email.value,
    password: password.value,
  })

  if (error) {
    if (error.message.includes("invalid email")) {
      errorMessage.value = "Email yang anda masukkan salah"
    } else if (error.message.includes("incorrect password")) {
      errorMessage.value = "Password yang anda masukkan salah"
    } else {
      errorMessage.value = "Periksa lebih teliti !!!"
    }
    setTimeout(() => {
      errorMessage.value = ""
    }, 1000)
    return
  }

  if (data) {
    getProfileRole(data.user.id)
  }
}

async function getProfileRole(id) {
  const { data, error } = await client
    .from('profile')
    .select('role')
    .eq('user_id', id)
    .single()
  if (data) {
    if (data.role == 'siswa') navigateTo('/murid')
    else navigateTo('/dashboard')
  }
}

function togglePasswordVisibility() {
  isPasswordVisible.value = !isPasswordVisible.value
}
</script>

<style scoped>
.tinggi {
  height: 100vh;
  padding-block: 10vh;
}

.button {
  border: none;
  color: white;
  background: #1A9EA7;
  padding: 10px 15px;
  font-size: 16px;
  text-align: center;
  cursor: pointer;
}

@media (max-width: 768px) {
  .tinggi {
    padding-block: 5vh;
  }

  .container {
    padding: 15px;
  }

  .form-control {
    font-size: 14px;
    padding: 10px 15px;
  }

  .button {
    font-size: 14px;
    padding: 8px 10px;
  }
}

.icon {
  background-color: white;
  padding-left: 40px;
  padding-right: 40px;
}

.icon-gembok {
  background-color: white;
  padding-left: 40px;
  padding-right: 40px;
}
</style>