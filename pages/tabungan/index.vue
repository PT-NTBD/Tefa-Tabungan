<template>
  <div class="p container-fluid p-5">
    <div class="row mb-5">
      <H1>TABUNGAN</H1>
    </div>
    <form @submit.prevent="kirimData">
      <div class="row mb-3">
        <label
          for="Kategori"
          class="kat d-flex mb-3 p-3 col-sm-2 col-form-label"
          >Kategori</label
        >
        <div class="col-sm-10">
          <select
            class="form-select form-select-lg"
            aria-label="Default select example"
            v-model="kategori"
          >
            <option selected disabled></option>
            <option value="pemasukan">Pemasukan</option>
            <option value="penarikan">Penarikan</option>
          </select>
        </div>
      </div>
      <div class="row mb-3">
        <label for="tabungan" class="nama d-flex mb-3 p-3 col-sm-2 col-form-label">Nama</label>
        <div class="col-sm-10">
          <select class="form-select form-select-lg" v-model="form.id_siswa">
            <option v-for="s in siswa" :key="s.id" :value="s.id">{{ s.nama }}</option>
          </select>
        </div>
      </div>
      <div class="row mb-3">
        <label for="masukan nominal" class="nom d-flex mb-3 p-3 col-sm-2 col-form-label">Jumlah nominal</label>
        <div class="col-sm-10">
          <input v-model="form.nominal" type="number" class="no form-control" id="nominal" />
        </div>
      </div>
      <div class="position-absolute top-70 end-0 p-5">
        <button type="submit" class="btn btn-primary ms-5">simpan</button>
      </div>
    </form>
  </div>
</template>

<script setup>
const supabase = useSupabaseClient();

const siswa = ref([])
const kategori = ref('')

const form = ref({
  id_siswa: null,
  nominal: 0,
});

const kirimData = async () => {
  if (kategori.value == "pemasukan") {
    const { error } = await supabase.from("pemasukan").insert([form.value]);
    if (error) throw error;
    else navigateTo("/pemasukan");
  }
  if (kategori.value == "penarikan") {
    const { error } = await supabase.from("penarikan").insert([form.value]);
    if (error) throw error;
    else navigateTo("/penarikan");
  }
};

const getSiswa = async () => {
  const { data, error } = await supabase.from('siswa').select()
  if (error) throw error
  if (data) siswa.value = data
}

onMounted(() => {
  getSiswa()
})
</script>
<style scope>
.p{
  min-height: 85vh;
}
.btn {
  width: 10rem;
}
</style>
