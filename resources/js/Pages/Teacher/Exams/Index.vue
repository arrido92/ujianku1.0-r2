<template>
    <Head>
      <title>Ujian Saya - Dashboard Guru</title>
    </Head>
    <div class="container mt-5">
      <h3 class="mb-4 text-primary fw-bold">Daftar Ujian Saya</h3>

      <!-- Toolbar -->
      <div class="mb-3 row">
        <div class="col-md-3">
          <Link
            href="/teacher/exams/create"
            class="shadow btn btn-sm btn-primary"
          >
            <i class="fa fa-plus-circle"></i> Tambah Ujian
          </Link>
        </div>
        <div class="col-md-9">
          <form @submit.prevent="handleSearch">
            <div class="input-group">
              <input
                type="text"
                v-model="search"
                class="form-control form-control-sm"
                placeholder="Cari ujian..."
              />
              <button class="btn btn-outline-secondary btn-sm" type="submit">
                <i class="fa fa-search"></i>
              </button>
            </div>
          </form>
        </div>
      </div>

      <!-- Tabel Ujian -->
      <div class="table-responsive">
        <table class="table text-center table-sm table-bordered">
          <thead class="table-dark">
            <tr>
              <th class="border-0" style="width:1%">#</th>
              <th class="border-0" style="width:40%">Detail Ujian</th>
              <th class="border-0" style="width:9%">Kelas</th>
              <th class="border-0" style="width:5%">Soal</th>
              <th class="border-0" style="width:15%">Komponen Soal</th>
              <th class="border-0" style="width:15%">Aksi</th>
            </tr>
          </thead>
          <tbody>
            <tr v-if="exams.data.length === 0">
              <td colspan="6" class="fw-bold">Tidak ada ujian ditemukan.</td>
            </tr>
            <tr v-else v-for="(exam, index) in exams.data" :key="exam.id">
              <td>{{ index + 1 + (exams.current_page - 1) * exams.per_page }}</td>
              <td>
                <dl class="text-start fw">
                <dt>{{ exam.title }}</dt>
                <dt>Mapel: {{ exam.lesson?.title || 'Tidak ada' }}</dt>
                <dt>Acak Soal: <span :class="{'text-white badge bg-info me-1' : exam.random_question === 'Y',
                    'text-white badge bg-warning me-1' : exam.random_question === 'N',
                    }">{{ exam.random_question }}</span>, Acak Jawaban: <span :class="{'text-white badge bg-info me-1' : exam.random_answer === 'Y',
                    'text-white badge bg-warning me-1' : exam.random_answer === 'N',
                    }">{{ exam.random_answer }}</span></dt>
                <dt>Tampil Nilai: <span :class="{'text-white badge bg-info me-1' : exam.show_answer === 'Y',
                    'text-white badge bg-warning me-1' : exam.show_answer === 'N',
                    }">{{ exam.show_answer }}</span></dt>
                </dl>
              </td>
              <td>
                <span
                  v-for="classroom in exam.classrooms"
                  :key="classroom.id"
                  class="badge bg-info me-1"
                >
                  {{ classroom.title }}
                </span>
              </td>
              <td>{{ exam.questions?.length || 0 }}</td>
              <td class="text-start">
                <ul class="mb-0 list-unstyled">
                <li v-for="(component, idx) in parseGradingComponents(exam.grading_components)" :key="idx">
                    {{ component.type }} = {{ component.weight }}%
                </li>
                </ul>
                </td>
              <td><dl>
                <dt><Link
                  :href="`/teacher/exams/${exam.id}`"
                  class="btn btn-sm btn-primary me-1"
                >
                  <i class="fa fa-eye"></i>
                </Link>
                <Link
                          :href="`/teacher/exams/${exam.id}/edit`"
                          class="border-0 shadow btn btn-sm btn-info me-2"
                        >
                          <i class="fa fa-pencil-alt"></i>
                        </Link>
                <button
                  @click.prevent="destroy(exam.id)"
                  class="btn btn-sm btn-danger"
                >
                  <i class="fa fa-trash"></i>
                </button></dt><br />
                <dt><Link
                    v-if="exam.has_essay"
                    :href="`/teacher/exams/${exam.id}/corrections`"
                    class="border-0 shadow btn btn-sm btn-warning"
                    >
                    <i class="fa fa-edit"></i> Koreksi Essay
                 </Link></dt>
                </dl>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Pagination -->
      <Pagination :links="exams.links" align="end" />
    </div>
  </template>

  <script>
import LayoutTeacher from "../../../Layouts/Teacher.vue";
  import { Head, Link } from "@inertiajs/vue3";
  import Pagination from "../../../Components/Pagination.vue";
  import { ref } from "vue";
  import { router } from "@inertiajs/vue3";
  import Swal from "sweetalert2";

  export default {
    layout: LayoutTeacher,
    components: { Head, Link, Pagination },
    props: { exams: Object },
    setup() {
      const search = ref("");

      const handleSearch = () => {
        router.get("/teacher/exams", { q: search.value });
      };

      const destroy = (id) => {
        Swal.fire({
          title: "Hapus Ujian?",
          text: "Data tidak dapat dikembalikan!",
          icon: "warning",
          showCancelButton: true,
          confirmButtonText: "Ya, Hapus!",
          cancelButtonText: "Batal",
        }).then((result) => {
          if (result.isConfirmed) {
            router.delete(`/teacher/exams/${id}`, {
              onSuccess: () => {
                Swal.fire("Berhasil!", "Ujian berhasil dihapus.", "success");
              },
            });
          }
        });
      };

    // Fungsi untuk memformat grading_components
    const parseGradingComponents = (gradingComponents) => {
      if (!gradingComponents) return [];
      try {
        const components = JSON.parse(gradingComponents);
        return components.map((component) => ({
          type: component.type === "single" ? "PG" : component.type === "multiple" ? "PG Kompleks" : "Essay",
          weight: component.weight,
        }));
      } catch (error) {
        return [];
      }
    };

      return { search, handleSearch, parseGradingComponents, destroy };
    },
  };
  </script>

  <style scoped>
  table {
    font-size: 0.875rem;
  }
  </style>
