<template>
    <Head>
      <title>Ujian - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-8">
          <div class="row">
            <!-- Tombol Tambah -->
            <div class="mb-2 col-md-3 col-12">
              <Link
                href="/admin/exams/create"
                class="border-0 shadow btn btn-md btn-primary w-100"
                type="button"
              >
                <i class="fa fa-plus-circle"></i> Tambah
              </Link>
            </div>

            <!-- Form Pencarian -->
            <div class="mb-2 col-md-9 col-12">
              <form @submit.prevent="handleSearch">
                <div class="input-group">
                  <input
                    type="text"
                    class="border-0 shadow form-control"
                    v-model="search"
                    placeholder="Masukkan kata kunci dan tekan Enter..."
                  />
                  <button class="border-0 shadow btn btn-outline-secondary" type="submit">
                    <i class="fa fa-search"></i>
                  </button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>

      <!-- Tabel Data Ujian -->
      <div class="mt-1 row">
        <div class="col-md-12">
          <div class="border-0 shadow card">
            <div class="card-body">

              <!-- Table -->
              <div class="table-responsive">
                <table class="table text-center table-sm table-bordered">
                  <thead class="thead-dark">
                    <tr>
                      <th class="border-0" style="width:1%">#</th>
                      <th class="border-0" style="width:63%">Detail Ujian</th>
                      <th class="border-0" style="width:5%">Kelas</th>
                      <th class="border-0" style="width:5%">Soal</th>
                      <th class="border-0" style="width:15%">Komponen Soal</th>
                      <th class="border-0" style="width:10%">Aksi</th>
                    </tr>
                  </thead>
                  <tbody>
                      <!-- Empty State -->
              <tr v-if="exams.data.length === 0" class="text-center">
                <td class="fw-bold" colspan="6">Tidak ada data ujian yang ditemukan.</td>
              </tr>
                    <tr v-else v-for="(exam, index) in exams.data" :key="exam.id">
                      <td class="text-center fw-bold">
                        {{ index + 1 + (exams.current_page - 1) * exams.per_page }}
                      </td>
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
                      <td class="text-center">
                        <span
                          v-for="(classroom, idx) in exam.classrooms"
                          :key="idx"
                          class="text-white badge bg-info me-1"
                        >
                          {{ classroom.title }}
                        </span>
                      </td>
                      <td class="text-center">{{ exam.questions?.length || 0 }}</td>
                      <td class="text-start">
                        <ul class="mb-0 list-unstyled">
                          <li v-for="(component, idx) in parseGradingComponents(exam.grading_components)" :key="idx">
                            {{ component.type }} = {{ component.weight }}%
                          </li>
                        </ul>
                      </td>
                      <td class="text-center"><dl>
                       <dt> <Link :href="`/admin/exams/${exam.id}`" class="border-0 shadow btn btn-sm btn-primary me-2" type="button"><i class="fa fa-plus-circle"></i></Link>
                        <!-- Tombol Edit -->
                        <Link
                          :href="`/admin/exams/${exam.id}/edit`"
                          class="border-0 shadow btn btn-sm btn-info me-2"
                        >
                          <i class="fa fa-pencil-alt"></i>
                        </Link>
                        <!-- Tombol Hapus -->
                        <button
                          @click.prevent="destroy(exam.id)"
                          class="border-0 shadow btn btn-sm btn-danger me-2"
                        >
                          <i class="fa fa-trash"></i>
                        </button>
                    </dt><br>
                        <!-- Tombol Koreksi Essay -->
                         <dt>
                        <Link
                            v-if="exam.has_essay"
                            :href="`/admin/exams/${exam.id}/corrections`"
                            class="border-0 shadow btn btn-sm btn-warning"
                        >
                            <i class="fa fa-edit"></i> Koreksi Essay
                        </Link>
                    </dt>
                    </dl>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <!-- Pagination -->
              <Pagination :links="exams.links" align="end" />
            </div>
          </div>
        </div>
      </div>
    </div>
</template>
<script>
import LayoutAdmin from "../../../Layouts/Admin.vue";
import Pagination from "../../../Components/Pagination.vue";
import { Head, Link, router } from "@inertiajs/vue3";
import { ref } from "vue";
import Swal from "sweetalert2";

export default {
  layout: LayoutAdmin,

  components: {
    Head,
    Link,
    Pagination,
  },

  props: {
    exams: Object,
  },

  setup() {
    const search = ref("");

    const handleSearch = () => {
      router.get("/admin/exams", {
        q: search.value.trim(),
      });
    };

    const destroy = (id) => {
      Swal.fire({
        title: "Apakah Anda yakin?",
        text: "Data yang dihapus tidak dapat dikembalikan!",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Ya, hapus!",
        cancelButtonText: "Batal",
      }).then((result) => {
        if (result.isConfirmed) {
          router.delete(`/admin/exams/${id}`, {
            onSuccess: () => {
              Swal.fire({
                title: "Berhasil!",
                text: "Ujian berhasil dihapus.",
                icon: "success",
                timer: 2000,
                showConfirmButton: false,
              });
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

    return {
      search,
      handleSearch,
      destroy,
      parseGradingComponents,
    };
  },
};
</script>
<style scoped>
  table {
    font-size: 0.875rem;
  }
  </style>
