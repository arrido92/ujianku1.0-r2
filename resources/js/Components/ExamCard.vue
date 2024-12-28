<template>
    <div class="exam-card">
      <div class="card-header">
        <div class="logo" style="width: 17%">
          <img v-if="appSettings.logo" :src="appSettings.logo" alt="Logo" />
        </div>
        <div class="header-text" style="width: 66%">
          <h2>KARTU PESERTA</h2>
          <h3>{{ appSettings.nama_ujian }}</h3>
          <h3>{{ appSettings.madrasah }}</h3>
          <p>{{ appSettings.alamat }}, Desa. {{ appSettings.desa }}</p>
        </div>
        <div class="logo" style="width: 17%">
          <img v-if="appSettings.logo_instansi" :src="appSettings.logo_instansi" alt="Logo" />
        </div>
      </div>
      <div class="card-body">
        <table class="info-table">
          <tr>
            <td>Nama</td>
            <td>: {{ student.name }}</td>
          </tr>
          <tr>
            <td>NISN</td>
            <td>: {{ student.nisn }}</td>
          </tr>
          <tr>
            <td>Password</td>
            <td>: {{ student.password }}</td>
          </tr>
          <tr>
            <td>Kelas</td>
            <td>: {{ student.classroom?.title || "Tidak ada" }}</td>
          </tr>
          <tr>
            <td>Alamat Ujian</td>
            <td>: {{ appSettings.web_ujian }}</td>
          </tr>
        </table>
      </div>
      <div class="card-footer">
        <p>{{ appSettings.kota }}, {{ currentDate }}</p>
        <div class="ttd">
          <img v-if="appSettings.ttd" :src="appSettings.ttd" alt="ttd" />
          <p><strong>{{ appSettings.kamad }}</strong></p>
          <p>NIP. {{ appSettings.nip }}</p>
        </div>
      </div>
    </div>
  </template>

  <script>
  import { ref, onMounted } from "vue";
  import axios from "axios";

  export default {
    props: {
      student: Object,
    },
    setup() {
      const appSettings = ref({
        logo: null,
        nama_ujian: null,
        madrasah: null,
        alamat: null,
        desa: null,
        logo_instansi: null,
        web_ujian: null,
        kota: null,
        ttd: null,
        kamad: null,
        nip: null,
      });

      const loadAppSettings = async () => {
        try {
          const response = await axios.get("/admin/api/app-settings");
          appSettings.value = {
            ...response.data,
            logo: response.data.logo ? `/storage/${response.data.logo}` : null,
            logo_instansi: response.data.logo_instansi ? `/storage/${response.data.logo_instansi}` : null,
            ttd: response.data.ttd ? `/storage/${response.data.ttd}` : null,
          };
        } catch (error) {
          console.error("Gagal memuat data appSettings:", error);
        }
      };

      onMounted(() => {
        loadAppSettings();
      });

      const currentDate = ref(new Date().toLocaleDateString("id-ID", { year: "numeric", month: "long", day: "numeric" }));

      return {
        appSettings,
        currentDate,
      };
    },
  };
  </script>

  <style scoped>
  .exam-card {
    width: 100%;
    max-width: 550px;
    margin: 10px auto;
    border: 2px solid #000;
    padding: 20px;
    font-family: Arial, sans-serif;
    box-sizing: border-box;
  }

  .card-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 5px;

  }

  .logo img {
    max-height: 60px;
  }

  .header-text {
    text-align: center;
    flex-grow: 1;
  }

  .header-text h2 {
    font-size: 18px;
    margin: 0;
  }

  .header-text h3 {
    font-size: 16px;
    margin: 5px 0 0;
  }
  .header-text p {
    font-size: 10px;
    margin: 0;
  }

  .card-body {
    margin-bottom: 1px;
  }

  .info-table {
    width: 70%;
    border-collapse: collapse;
  }

  .info-table td {
    padding: 5px 10px;
    font-size: 14px;
    vertical-align: top;

  }

  .card-footer {
    text-align: right;
  }

  .card-footer p {
    margin: 5px 0;
  }
  .card-footer img {
    max-height: 70px;
  }

  .ttd {
    margin-top: 20px;
    text-align: right;
  }

  .ttd p {
    margin: 5px 0;
  }
  </style>
