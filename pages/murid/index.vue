<template>
  <div class="container-fluid" :class="{ 'shift-right': menuVisible }">
    <div class="row justify-content-center">
      <!-- Profil Section -->
      <div class="col-12 col-md-3 m-5 d-flex justify-content-center align-items-center profil text-center">
        <nuxt-link to="/identitas" class="test">
          Profil 
          <i class="ms-2 bi bi-person-fill zoom"></i>
        </nuxt-link>
      </div>
      <div class="col-12 col-md-3 m-5 d-flex justify-content-center align-items-center riwayat text-center">
        <nuxt-link to="/riwayats" class="test">
          Saldo
          <i class="ms-2 bi bi-pie-chart-fill zoom"></i>
        </nuxt-link>
      </div>
    </div>
  </div>
</template>

<script setup>


const client = useSupabaseClient();
const totalBalance = ref({ saldo: 0 });
const siswa = ref();
const user = useSupabaseUser();
const menuVisible = ref(false);

const getProfile = async () => {
  const { data, error } = await client
    .from('profile')
    .select()
    .eq('user_id', user.value.id)
    .single();
  
  if (data) {
    siswa.value = data;
  }
};

const fetchTotalBalance = async () => {
  if (siswa.value && siswa.value.id) {
    const { data, error } = await client
      .from('siswa')
      .select('saldo') 
      .eq('id', siswa.value.id)
      .single();
    
    if (data) {
      totalBalance.value = data;
    }
  }
};

onMounted(() => {
  getProfile();
  setTimeout(() => {
    fetchTotalBalance();
  }, 1000); 
});
</script>

<style scoped>

.container-fluid {
  min-height: 85vh;
  transition: transform 0.3s ease;
}

body.shift-right .container-fluid {
  transform: translateX(130px);
}

a {
  text-decoration: none;
}

.test {
  text-align: center;
  color: black;
  font-weight: bold;
}

.zoom {
  font-size: 5rem; 
  transition: transform 0.3s ease;
}
.zoom:hover {
  transform: scale(1.1);  
}

.profil, .riwayat {
  font-size: 1.5rem; 
  min-height: 150px;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 15px;
  border-radius: 8px;
  transition: transform 0.3s;
  box-shadow: 0 8px 8px rgba(0, 0, 0, 0.1);
}

.profil:hover {
  transform: scale(1.1);
}
.riwayat:hover {
  transform: scale(1.1);
}
.profil {
  background-color: #BCDF5B;
}

.riwayat {
  background-color: #BF4E0E;
}


@media (max-width: 992px) {
  .zoom {
    font-size: 5rem; 
  }
  .profil, .riwayat {
    font-size: 1.25rem; 
  }
}

@media (max-width: 768px) {
  .zoom {
    font-size: 6rem; 
  }
  .profil, .riwayat {
    font-size: 1.1rem; 
  }
}

@media (max-width: 576px) {
  .zoom {
    font-size: 7rem; 
  }
  .profil, .riwayat {
    font-size: 1rem;
    min-height: 120px; 
  }
}
</style>