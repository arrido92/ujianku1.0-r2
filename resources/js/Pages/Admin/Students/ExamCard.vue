<template>
    <Head>
      <title>Kartu Ujian - Aplikasi Ujian Online</title>
    </Head>
      <!-- Button Kembali -->
      <Link href="/admin/students" class="mb-3 border-0 shadow btn btn-md btn-primary">
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">

      <!-- Menu Filter dan Download -->
      <div class="gap-2 mb-4 d-flex justify-content-end align-items-center">
        <!-- Filter Kelas -->
        <select
          v-model="selectedClassroom"
          @change="filterByClassroom"
          class="border-0 shadow form-select"
          style="max-width: 200px;"
        >
          <option value="">Semua Kelas</option>
          <option v-for="classroom in classrooms" :key="classroom.id" :value="classroom.id">
            {{ classroom.title }}
          </option>
        </select>

        <!-- Download PDF -->
        <button @click="downloadPdf" class="btn btn-danger">
          <i class="fa fa-file-pdf"></i> Download PDF
        </button>
      </div>

      <!-- Preview Kartu Ujian -->
      <div v-if="students.length" class="row">
        <div class="mb-4 col-md-6" v-for="student in students" :key="student.id">
          <ExamCard :student="student" />
        </div>
      </div>

      <!-- Jika Tidak Ada Data -->
      <div v-else class="text-center">
        <p class="text-danger">Tidak ada data siswa untuk ditampilkan.</p>
      </div>
    </div>
</div>
</div>
  </template>

  <script>
  import ExamCard from "../../../Components/ExamCard.vue";
  import { ref } from "vue";
  import { Head, router, Link } from "@inertiajs/vue3";
  import LayoutAdmin from "../../../Layouts/Admin.vue";

  export default {
    layout: LayoutAdmin,
    components: {
      ExamCard,
      Head,
      Link,
    },
    props: {
      students: Array, // Data siswa untuk preview
      classrooms: Array, // Data kelas untuk filter
      filters: Object, // Filter dari backend
    },
    setup(props) {
      const selectedClassroom = ref(props.filters.classroom || ""); // Sinkron dengan filter dari backend

      // Filter berdasarkan kelas
      const filterByClassroom = () => {
        router.get("/admin/students/exam-cards", {
          classroom: selectedClassroom.value,
        });
      };

      // Download PDF dengan filter kelas
      const downloadPdf = () => {
        const params = selectedClassroom.value
          ? `?classroom=${selectedClassroom.value}`
          : "";
        window.open ( `/admin/students/exam-cards-pdf${params}`, '_blank');
      };

      return {
        selectedClassroom,
        filterByClassroom,
        downloadPdf,
      };
    },
  };
  </script>

  <style scoped>
  .container {
    padding-top: 20px;
  }

  button.btn,
  select.form-select {
    margin-left: 10px;
  }

  .d-flex.gap-2 {
    gap: 10px;
  }

  .row .col-md-6 {
    display: flex;
    justify-content: center;
  }
  </style>
