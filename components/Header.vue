<template>
  <nav class="navbar fixed">
    <div class="container-fluid d-flex align-items-center justify-content-center">
      <span class="navbar-brand mb-0 h1 text-center">
        Selamat Datang Di Tabungan {{ userRole === 'guru' ? 'WaliKelas' : 'Siswa' }}
      </span>
    </div>
  </nav>
</template>

<script setup>
const user = useSupabaseUser()  
const client = useSupabaseClient()

const userRole = ref('')  

async function getRole() {
  let { data, error } = await client
    .from('profile')
    .select('role')
    .eq('user_id', user.value.id)
    .single()

  if (data) {
    userRole.value = data.role  
  }
}

onMounted(() => {
  getRole() 
})
</script>

<style scoped>
.navbar {
  background: #1A9EA7;
  height: 15vh;
}

.navbar-brand {
  color: white;
  font-size: 40px;
  font-weight: bold;
  text-align: center;
}

@media (max-width: 768px) {
  .navbar {
    height: 10vh; 
  }

  .navbar-brand {
    font-size: 24px; 
  }
}

@media (max-width: 576px) {
  .navbar {
    height: 8vh;
  }

  .navbar-brand {
    font-size: 18px; 
  }
}
</style>
