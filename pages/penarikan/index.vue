<template>
  <div class="container-fluid p-5">
    <div class="row mb-5">
      <h1>TABUNGAN > PENARIKAN</h1>
    </div>
    <div class="row justify-content-end">
      <div class="col-12 col-md-5 p-3">
        <form @submit.prevent="getData">
          <input v-model="keyword" type="search" class="form-control" placeholder="Search ...">
        </form>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <table class="table table-bordered table-responsive">
          <thead>
            <tr>
              <th scope="col">ID</th>
              <th scope="col">WAKTU</th>
              <th scope="col">Nama</th>
              <th scope="col">Nominal</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(visitor, i) in filteredVisitors" :key="i">
              <td>{{ i + 1 }}.</td>
              <td>{{ visitor.tgl }}</td>
              <td>{{ visitor.siswa.nama }}</td>
              <td>{{ visitor.nominal }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup>
const supabase = useSupabaseClient();
const keyword = ref('');
const visitors = ref([]);

// Fungsi untuk mengambil data
const getData = async () => {
  const { data } = await supabase
    .from('penarikan')
    .select('*, siswa(*)')
    .order('tgl', { ascending: false });
    
  if (data) visitors.value = data;
}

// Filter berdasarkan pencarian
const filteredVisitors = computed(() => {
  if (!keyword.value) return visitors.value;
  return visitors.value.filter(visitor => {
    return visitor.siswa.nama.toLowerCase().includes(keyword.value.toLowerCase()) ||
          visitor.nominal.toString().includes(keyword.value);
  });
});

onMounted(() => {
  getData();
});
</script>

<style scoped>
.table {
  text-align: center;
  width: 100%; 
}

.form-control {
  font-size: 14px;
}

.container-fluid {
  max-height: 85vh;
  overflow: auto;
}

.table-responsive {
  overflow-x: auto;
}

@media (max-width: 768px) {
  .col-12 {
    padding-left: 0.5rem;
    padding-right: 0.5rem;
  }
  
  .form-control {
    font-size: 14px; /* Menyesuaikan ukuran font input */
  }

  .table th, .table td {
    font-size: 14px; /* Menurunkan ukuran font pada tabel di perangkat kecil */
  }
}

/* Mengatur padding dan margin untuk layar lebih kecil */
@media (max-width: 576px) {
  .container-fluid {
    padding-left: 1rem;
    padding-right: 1rem;
  }
  
  .table th, .table td {
    font-size: 12px; /* Mengurangi ukuran font untuk layar lebih kecil */
  }
}
</style>
