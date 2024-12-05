<template>
  <div class="container-fluid">
    <div class="row">
      <h2 class="text-center my-4">REKAPAN DATA PERBULAN</h2>
      <div class="col-lg-12">
        <div class="warna my-3" style="border-radius: 5px;">
          <h5 style="color: white; text-align: center;">
            JUMLAH TABUNGAN SAAT INI: {{ jumlah_tabungan.toLocaleString("id-ID", { style: "currency", currency: "IDR" }) }}
          </h5>
        </div>

        <div class="table-responsive">
          <table class="table table-striped">
            <thead>
              <tr>
                <th>No</th>
                <th>Bulan</th>
                <th>Tahun</th>
                <th>Pemasukan</th>
                <th>Penarikan</th>
                <th>Saldo</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(rekapan, i) in rekapData" :key="i">
                <td>{{ i + 1 }}</td>
                <td>{{ rekapan.bulan }}</td>
                <td>{{ rekapan.tahun }}</td>
                <td>{{ rekapan.pemasukan.toLocaleString("id-ID", { style: "currency", currency: "IDR" }) }}</td>
                <td>{{ rekapan.penarikan.toLocaleString("id-ID", { style: "currency", currency: "IDR" }) }}</td>
                <td>{{ rekapan.saldo.toLocaleString("id-ID", { style: "currency", currency: "IDR" }) }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>

const supabase = useSupabaseClient();
const rekapData = ref([]);

const jumlah_tabungan = computed(() => {
  return rekapData.value.reduce((total, rekapan) => {
    return total + rekapan.saldo;
  }, 0);
});

const fetchRekapData = async () => {
  try {
    const [pemasukanData, penarikanData] = await Promise.all([
      supabase.from("pemasukan").select("tgl, nominal"),
      supabase.from("penarikan").select("tgl, nominal"),
    ]);

    if (pemasukanData.error || penarikanData.error) {
      throw new Error("Error fetching data");
    }

    const monthlyData = {};
    let previousSaldo = 0; 

    pemasukanData.data.forEach((item) => {
      const date = new Date(item.tgl);
      const year = date.getFullYear();
      const month = date.getMonth();
      const key = `${year}-${month}`;

      if (!monthlyData[key]) {
        monthlyData[key] = {
          bulan: getMonthName(month),
          tahun: year,
          pemasukan: 0,
          penarikan: 0,
          saldo: 0,
        };
      }
      monthlyData[key].pemasukan += item.nominal;
    });

    penarikanData.data.forEach((item) => {
      const date = new Date(item.tgl);
      const year = date.getFullYear();
      const month = date.getMonth();
      const key = `${year}-${month}`;

      if (!monthlyData[key]) {
        monthlyData[key] = {
          bulan: getMonthName(month),
          tahun: year,
          pemasukan: 0,
          penarikan: 0,
          saldo: 0,
        };
      }
      monthlyData[key].penarikan += item.nominal;
    });

    Object.values(monthlyData).forEach((data) => {
      data.saldo = previousSaldo + data.pemasukan - data.penarikan;
      previousSaldo = data.saldo; 
    });

    rekapData.value = Object.values(monthlyData).sort((a, b) => {
      const dateA = new Date(`${a.tahun}-${a.bulan}-01`);
      const dateB = new Date(`${b.tahun}-${b.bulan}-01`);
      return dateB - dateA; 
    });
  } catch (error) {
    console.error("Terjadi kesalahan saat mengambil data rekap:", error.message);
  }
};

const getMonthName = (monthNumber) => {
  const months = [
    "Januari",
    "Februari",
    "Maret",
    "April",
    "Mei",
    "Juni",
    "Juli",
    "Agustus",
    "September",
    "Oktober",
    "November",
    "Desember",
  ];
  return months[monthNumber];
};

onMounted(() => {
  fetchRekapData();
});
</script>

<style scoped>
.table-responsive {
  overflow-x: auto;
}
h2 {
  color: black;
}
.table-striped th,
.table-striped td {
  white-space: nowrap;
}
@media (max-width: 576px) {
  .table-responsive {
    font-size: 12px;
  }
  .btn {
    padding: 12px;
    font-size: 14px;
  }
}
@media (min-width: 992px) {
  .float-lg-end {
    float: right;
  }
}
.warna {
  background: #1a9ea7;
}
</style>