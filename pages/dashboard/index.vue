<template>
  <div class="container-fluid" :class="{ 'shift-right': menuVisible }">
    <div class="row justify-content-center">
      <!-- Konten Dashboard -->
      <div v-if="userRole == 'guru'" class="col-12 col-md-4 col-lg-3 m-3 d-flex justify-content-center align-items-center siswaa text-center">
        <nuxt-link to="/siswa" class="test">Siswa<i class="ms-3 bi bi-person-fill zoom"></i></nuxt-link>
      </div>

      <div v-if="userRole == 'guru'" class="col-12 col-md-4 col-lg-3 m-3 d-flex justify-content-center align-items-center tabungann text-center">
        <nuxt-link to="/tabungan" class="test">Tabungan<i class="ms-3 bi bi-bar-chart-fill zoom"></i></nuxt-link>
      </div>

      <div v-if="userRole == 'siswa'" class="col-12 col-md-4 col-lg-3 m-3 d-flex justify-content-center align-items-center siswaa text-center">
        <nuxt-link to="/siswa" class="test">Profil<i class="ms-3 bi bi-person-fill zoom"></i></nuxt-link>
      </div>

      <div v-if="userRole == 'guru'" class="col-12 col-md-4 col-lg-3 m-3 d-flex justify-content-center align-items-center saldo text-center">
        <nuxt-link to="/rekapan" class="test">Rekapan<i class="ms-3 bi bi-pie-chart-fill zoom"></i></nuxt-link>
      </div>

      <div v-if="userRole == 'siswa'" class="col-12 col-md-4 col-lg-3 m-3 d-flex justify-content-center align-items-center saldo text-center">
        <nuxt-link to="/siswa" class="test">Saldo<i class="ms-3 bi bi-pie-chart-fill zoom"></i></nuxt-link>
      </div>
    </div>
  </div>
</template>

<script setup>
const client = useSupabaseClient();
const user = useSupabaseUser();
const userRole = ref("");
const menuVisible = ref(false);

async function getRole() {
  let { data, error } = await client.from("profile").select("role").eq("user_id", user.value.id).single();
  if (error) throw error;
  if (data) userRole.value = data.role;
}

onMounted(() => {
  getRole();
});
</script>

<style scoped>
a {
  text-decoration: none;
}

.test {
  text-align: center;
  color: black;
  font-weight: bold;
}

.zoom {
  font-size: 80px;
}

.siswaa {
  font-size: 50px;
  min-height: 150px;
  background-color: #bcdf5b;
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 8px 8px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s;
}

.siswaa:hover {
  transform: scale(1.1);
}

.tabungann {
  font-size: 50px;
  min-height: 150px;
  background-color: #5e99cf;
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 8px 8px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s;
}

.tabungann:hover {
  transform: scale(1.1);
}

.saldo {
  font-size: 50px;
  min-height: 150px;
  background-color: #bf4e0e;
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 8px 8px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s;
}

.saldo:hover {
  transform: scale(1.1);
}
.container-fluid {
  min-height: 85vh;
  transition: transform 0.3s ease;
}


body.shift-right .container-fluid {
  transform: translateX(130px);
}

@media (max-width: 768px) {
  .zoom {
    font-size: 50px;
  }
  .siswaa,
  .tabungann,
  .saldo {
    font-size: 20px;
  }
}

@media (min-width: 768px) and (max-width: 992px) {
  .zoom {
    font-size: 60px;
  }
  .siswaa,
  .tabungann,
  .saldo {
    font-size: 25px;
  }
}

@media (min-width: 992px) {
  .zoom {
    font-size: 70px;
  }
  .siswaa,
  .tabungann,
  .saldo {
    font-size: 30px;
  }
}
</style>
