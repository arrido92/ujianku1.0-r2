<template>
    <Head>
      <title>Dashboard Teacher - Aplikasi Ujian Online</title>
    </Head>
    <div class="container mt-4">
      <h4 class="mb-4 text-center fw-bold">Dashboard Teacher</h4>

      <!-- Ringkasan Aktivitas -->
      <div class="mb-4 row g-2">
        <div class="col-6 col-md-3" v-for="(item, index) in summaryData" :key="index">
          <div class="p-3 text-center shadow-sm rounded-3 bg-light">
            <h6 class="mb-1 text-muted">{{ item.title }}</h6>
            <h3 class="fw-bold text-primary">{{ item.value }}</h3>
          </div>
        </div>
      </div>
      <div class="mb-2 row">
      <div class="col-md-6">
      <!-- Ujian yang Sedang Berlangsung -->
      <div class="mb-4 shadow-sm card">
        <div class="py-2 text-white card-header bg-primary">
          <h6 class="mb-0 fw-bold">Ujian yang Sedang Berlangsung</h6>
        </div>
        <div class="p-3 card-body">
          <ul v-if="ongoingExams.length" class="list-group list-group-flush">
            <li
              v-for="exam in ongoingExams"
              :key="exam.id"
              class="list-group-item d-flex justify-content-between align-items-center"
            >
              <span>{{ exam.title }} - {{ exam.lesson.title }}</span>
              <span class="text-white badge bg-info">Sedang Berlangsung</span>
            </li>
          </ul>
          <p v-else class="text-center text-muted">Tidak ada ujian yang sedang berlangsung.</p>
        </div>
      </div>
    </div>
      <!-- Statistik Nilai -->
      <div class="col-md-6">
      <div class="mb-4 shadow-sm card">
        <div class="py-2 text-white card-header bg-success">
          <h6 class="mb-0 fw-bold">Statistik Nilai Rata-rata</h6>
        </div>
        <div class="p-3 card-body">
          <table class="table mb-0 text-center table-sm table-bordered table-striped">
            <thead class="table-dark">
              <tr>
                <th class="fw-bold">Nama Ujian</th>
                <th class="fw-bold">Rata-rata Nilai</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(grade, index) in examGrades" :key="grade.exam_id || index">
                <td>{{ grade.exam_title || "Ujian Tidak Diketahui" }}</td>
                <td>{{ grade.average_grade ? grade.average_grade.toFixed(2) : "Belum Ada Nilai" }}</td>
              </tr>
              <tr v-if="examGrades.length === 0">
                <td colspan="2" class="text-muted">Tidak ada data nilai.</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
      </div>
    </div>
      <!-- Grafik Donat Status Penyelesaian Ujian -->
      <div class="row justify-content-center">
        <div class="col-md-6">
          <div class="shadow-sm card">
            <div class="py-2 text-white card-header bg-warning">
              <h6 class="mb-0 text-center fw-bold">Status Penyelesaian Ujian</h6>
            </div>
            <div class="p-3 card-body">
              <canvas id="completionChart" class="mx-auto" style="max-height: 300px"></canvas>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>

<script>
import { Head } from "@inertiajs/vue3";
import LayoutTeacher from "../../../Layouts/Teacher.vue";
import { onMounted, onBeforeUnmount, ref } from "vue";
import Chart from "chart.js/auto";

export default {
  layout: LayoutTeacher,
  props: {
    totalExams: Number,
    totalQuestions: Number,
    totalParticipants: Number,
    ongoingExams: Array,
    pendingEssayCorrections: Number,
    examGrades: Array,
    completionStatus: Object,
  },
  setup(props) {
    const summaryData = [
      { title: "Total Ujian", value: props.totalExams },
      { title: "Total Soal", value: props.totalQuestions },
      { title: "Total Peserta", value: props.totalParticipants },
      { title: "Essay Perlu Dikoreksi", value: props.pendingEssayCorrections },
    ];

    const chartInstance = ref(null);

    const renderChart = () => {
      const ctx = document.getElementById("completionChart").getContext("2d");

      // Hancurkan instance chart sebelumnya jika ada
      if (chartInstance.value) {
        chartInstance.value.destroy();
      }

      // Buat chart baru
      chartInstance.value = new Chart(ctx, {
        type: "doughnut",
        data: {
          labels: ["Selesai", "Belum Selesai"],
          datasets: [
            {
              data: [props.completionStatus.completed, props.completionStatus.notCompleted],
              backgroundColor: ["#28a745", "#dc3545"],
              hoverOffset: 4,
            },
          ],
        },
        options: {
          responsive: true,
          plugins: {
            legend: {
              display: true,
              position: "bottom",
              labels: {
                font: {
                  size: 12,
                },
                boxWidth: 12,
              },
            },
          },
        },
      });
    };

    // Render chart saat halaman dimuat
    onMounted(() => {
      renderChart();
    });

    // Hancurkan chart saat keluar dari halaman
    onBeforeUnmount(() => {
      if (chartInstance.value) {
        chartInstance.value.destroy();
      }
    });

    return { summaryData };
  },
};
</script>


  <style scoped>
  h4 {
    font-size: 1.4rem;
    color: #333;
  }

  .card {
    border-radius: 10px;
  }

  .card-header {
    font-size: 1rem;
    text-align: center;
  }

  .table-sm th,
  .table-sm td {
    padding: 0.5rem;
    font-size: 0.85rem;
  }

  .table-bordered {
    border-color: #ddd;
  }

  .bg-warning {
    background-color: #ffc107 !important;
  }

  .text-muted {
    font-size: 0.9rem;
  }
  </style>
