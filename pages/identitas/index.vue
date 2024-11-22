<template>
  <div class="container-fluid" :class="{ 'shift-right': menuVisible }">
    <h2 class="text-start my-4 p-3">{{ siswa?.fullname }}</h2>
    <div class="col-md-12">
      <ul class="list-group list-group-flush p-3">
        <li v-if="siswa" class="list-group-item p-3 m-1">ID: {{ siswa?.id }}</li>
        <li v-if="siswa" class="list-group-item p-3 m-1">NIS: {{ siswa?.Nis }}</li>
        <li v-if="siswa" class="list-group-item p-3 m-1">Alamat: {{ siswa?.alamat }}</li>
        <li v-if="siswa" class="list-group-item p-3 m-1">Jenis Kelamin: {{ siswa?.jenis_kelamin }}</li>
        <span v-else class="p-3">Sedang memuat...</span>
      </ul>
    </div>
  </div>
</template>

<script setup>
const supabase = useSupabaseClient();
const siswa = ref();
const user = useSupabaseUser();
const menuVisible = ref(false);

const getProfile = async () => {
  const { data, error } = await supabase
    .from('profile')
    .select()
    .eq('user_id', user.value.id)
    .single();

  if (data) siswa.value = data;
};

onMounted(() => {
  getProfile();
});
</script>

<style scoped>
.container-fluid {
  background-color: #f4f4f4;
  border-radius: 12px;
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.1);
  padding: 30px;
  min-height: 85vh;
  transition: transform 0.3s ease;
  max-width: 100%; /* Memastikan lebar kontainer tidak lebih dari layar */
  width: 100%;
}

body.shift-right .container-fluid {
  transform: translateX(250px);
}

.list-group-item {
  background-color: #fff;
  border-radius: 10px;
  padding: 20px;
  font-size: 1.1rem;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.3s ease-in-out;
  max-width: 1500px; /* Menambahkan batasan lebar maksimal pada list item */
  width: 100%; /* Memastikan lebar maksimal tidak melebihi 100% dari kolom */
  margin: 10px auto; /* Menyusun item ke tengah */
}

/* Responsif untuk ukuran layar lebih kecil */
@media (max-width: 992px) {
  .container-fluid {
    padding: 20px;
  }
  .list-group-item {
    padding: 15px;
  }
}

@media (max-width: 768px) {
  .container-fluid {
    padding: 15px;
  }
  .list-group-item {
    padding: 10px;
  }
}

@media (max-width: 576px) {
  .list-group-item {
    font-size: 1rem;
    padding: 10px;
  }
}
</style>
