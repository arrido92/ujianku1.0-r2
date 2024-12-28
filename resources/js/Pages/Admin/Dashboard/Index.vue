<template>
    <Head>
      <title>Dashboard - Aplikasi Ujian Online</title>
    </Head>
    <div class="container mt-5">
      <h4 class="fw-bold">Dashboard Admin</h4>

      <!-- Stat Cards -->
      <div class="mb-4 row">
        <StatCard title="Kelas" :count="classrooms" icon="bi bi-building" color="info" />
        <StatCard title="Siswa" :count="students" icon="bi bi-people" color="success" />
        <StatCard title="Guru" :count="teachers" icon="bi bi-person-badge" color="primary" />
        <StatCard title="Ujian" :count="exams" icon="bi bi-pencil-square" color="tertiary" />
        <StatCard title="Sesi Ujian" :count="exam_sessions" icon="bi bi-stopwatch" color="danger" />
      </div>

      <!-- Statistik Ujian Berdasarkan Mata Pelajaran dan Statistik Performa Siswa -->
      <div class="mt-4 row">
        <div class="mb-3 col-md-6 col-lg-4">
          <h5>Statistik Ujian Berdasarkan Mapel</h5>
          <canvas id="examSubjectChart"></canvas>
        </div>
        <div class="mb-3 col-md-6 col-lg-8">
          <h5>Statistik Performa Siswa</h5>
          <canvas id="studentPerformanceChart"></canvas>
        </div>
      </div>

      <!-- Daftar Ujian Terakhir dan Aktivitas Terkini -->
      <div class="mt-4 row">
        <div class="col-md-6">
          <h5>Daftar Ujian Terakhir</h5>
          <table class="table table-bordered table-centered">
            <thead>
              <tr>
                <th>Nama Ujian</th>
                <th>Pengajar</th>
                <th>Waktu</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="exam in latestExams" :key="exam.id">
                <td>{{ exam?.title || "Tidak Tersedia" }}</td>
                <td>{{ exam?.creator?.name || "Administrator" }}</td>
                <td>{{ exam?.created_at ? new Date(exam.created_at).toLocaleString() : "Tidak Tersedia" }}</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="col-md-6">
          <h5>Aktivitas Terkini</h5>
          <ul class="list-group">
            <li v-for="activity in recentActivity" :key="activity.id" class="list-group-item">
              {{ activity?.name || "Tidak Tersedia" }} ({{ activity?.role || "Tidak Tersedia" }}) -
              {{ activity?.last_login ? new Date(activity.last_login).toLocaleString() : "Tidak Tersedia" }}
            </li>
          </ul>
        </div>
      </div>
      <!-- Grafik Soal dan Siswa dengan Performa Terbaik -->
      <div class="mt-4 row">
        <div class="col-md-8">
          <div class="shadow card">
            <div class="card-body">
              <h5 class="text-center">Kalender Ujian</h5>
              <div id="calendar" style="height: 300px;"></div>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <h5>Siswa dengan Performa Terbaik</h5>
          <ul class="list-group">
            <li v-for="student in topStudents" :key="student.id" class="list-group-item">
              {{ student.student.name }} - {{ student.grade }}
            </li>
          </ul>
        </div>
      </div>


      <!-- Grafik Donat Status Penyelesaian Ujian -->
      <div class="mt-4 row">
        <div v-for="(data, index) in completionData" :key="index" class="mb-3 col-md-6 col-lg-4">
          <div class="shadow card">
            <div class="text-white card-header bg-primary">
              <h6>{{ data.exam_title }} - {{ data.session_sesi_title }} - {{ data.session_title }}</h6>
            </div>
            <div class="card-body">
              <canvas :id="'completionChart-' + index"></canvas>
            </div>
            <ul class="list-group list-group-flush">
              <li class="list-group-item">Belum Mulai: {{ data.not_started }}</li>
              <li class="list-group-item">Sedang Dikerjakan: {{ data.in_progress }}</li>
              <li class="list-group-item">Selesai: {{ data.completed }}</li>
              <li class="list-group-item">Total Peserta: {{ data.total_participants }}</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </template>
<script>
import { Head } from "@inertiajs/vue3";
import LayoutAdmin from "../../../Layouts/Admin.vue";
import StatCard from "../../../Components/StatCard.vue";
import { Calendar } from "@fullcalendar/core";
import dayGridPlugin from "@fullcalendar/daygrid";
import interactionPlugin from "@fullcalendar/interaction";
import Chart from "chart.js/auto";

export default {
  layout: LayoutAdmin,
  components: { Head, StatCard },
  props: {
    students: Number,
    teachers: Number,
    exams: Number,
    exam_sessions: Number,
    classrooms: Number,
    completionData: Array,
    averageGrades: Array,
    examBySubjects: Object,
    recentActivity: Array,
    latestExams: Array,
    studentPerformance: Object,
    questionStatistics: Array,
    topStudents: Array,
    calendarEvents: Array,
  },
  mounted() {
    // Grafik Statistik Ujian Berdasarkan Mata Pelajaran
    const ctx1 = document.getElementById("examSubjectChart").getContext("2d");
    new Chart(ctx1, {
      type: "pie",
      data: {
        labels: Object.keys(this.examBySubjects),
        datasets: [
          {
            data: Object.values(this.examBySubjects),
            backgroundColor: ["#4CAF50", "#FF9800", "#F44336", "#2196F3"],
          },
        ],
      },
    });

    // Grafik Statistik Performa Siswa
    const ctx2 = document.getElementById("studentPerformanceChart").getContext("2d");
    new Chart(ctx2, {
      type: "bar",
      data: {
        labels: ["Rata-rata", "Nilai Tinggi", "Nilai Sedang", "Nilai Rendah"],
        datasets: [
          {
            label: "Jumlah",
            data: [
              this.studentPerformance.average || 0,
              this.studentPerformance.high || 0,
              this.studentPerformance.medium || 0,
              this.studentPerformance.low || 0,
            ],
            backgroundColor: ["#4CAF50", "#FFEB3B", "#FFC107", "#F44336"],
          },
        ],
      },
    });
    // Grafik Statistik Soal
    /**const ctx3 = document.getElementById("questionChart").getContext("2d");
    new Chart(ctx3, {
      type: "bar",
      data: {
        labels: this.questionStatistics.map((q) => q.type),
        datasets: [
          {
            label: "Jumlah Soal",
            data: this.questionStatistics.map((q) => q.count),
            backgroundColor: "#2196F3",
          },
        ],
      },
    });*/

    // Kalender Ujian
    const calendarEl = document.getElementById("calendar");
    const calendar = new Calendar(calendarEl, {
      plugins: [dayGridPlugin, interactionPlugin],
      initialView: "dayGridMonth",
      height: 300, // Tinggi kalender diperkecil
      events: this.calendarEvents.map((event) => ({
        title: event.exam.title,
        start: event.start_time,
        end: event.end_time,
      })),
    });
    calendar.render();

    // Grafik Donat Status Penyelesaian Ujian
    this.completionData.forEach((data, index) => {
      const ctx = document.getElementById(`completionChart-${index}`).getContext("2d");
      new Chart(ctx, {
        type: "doughnut",
        data: {
          labels: ["Belum Mulai", "Sedang Dikerjakan", "Selesai"],
          datasets: [
            {
              data: [data.not_started, data.in_progress, data.completed],
              backgroundColor: ["#F44336", "#FF9800", "#4CAF50"],
              hoverOffset: 4,
            },
          ],
        },
      });
    });
  },
};
</script>
<style scoped>
.card {
  border-radius: 10px;
  overflow: hidden;
}
.table-centered {
  text-align: center;
  vertical-align: middle;
}
#calendar {
  margin-top: 15px;
}
</style>
