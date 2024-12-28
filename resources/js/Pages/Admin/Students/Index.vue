<template>
    <Head>
      <title>Siswa - Aplikasi Ujian Online</title>
    </Head>
    <div class="container mt-4 mb-5">
      <!-- Header -->
      <div class="mb-3 row align-items-center">
        <div class="col-md-8">
          <h5 class="fw-bold text-primary">Daftar Siswa</h5>
        </div>
      </div>

      <!-- Toolbar -->
      <div class="mb-2 row">
        <div class="col-md-6">
          <div class="gap-2 d-flex">
            <Link href="/admin/students/create" class="btn btn-sm btn-primary">
              <i class="fa fa-plus-circle"></i> Tambah
            </Link>
            <Link href="/admin/students/import" class="text-white btn btn-sm btn-success">
              <i class="fa fa-file-excel"></i> Import
            </Link>
            <Link href="/admin/students/exam-cards" class="text-white btn btn-sm btn-warning">
              <i class="fa fa-id-card"></i> Cetak Kartu
            </Link>
            <Link href="/admin/students/attendance-list" class="text-white btn btn-sm btn-warning">
              <i class="fa fa-list-alt"></i> Daftar Hadir
            </Link>
          </div>
        </div>
        <!-- Search and Filter -->
        <div class="gap-2 col-md-6 d-flex">
          <!-- Search -->
          <form @submit.prevent="handleSearch" class="flex-grow-1">
            <div class="input-group input-group-sm">
              <input
                type="text"
                class="form-control"
                v-model="search"
                placeholder="Cari siswa..."
              />
              <button class="btn btn-outline-secondary" type="submit">
                <i class="fa fa-search"></i>
              </button>
            </div>
          </form>
          <!-- Filter -->
          <select
            class="form-select form-select-sm"
            v-model="selectedClassroom"
            @change="filterByClassroom"
          >
            <option value="">Semua Kelas</option>
            <option v-for="classroom in classrooms" :key="classroom.id" :value="classroom.id">
              {{ classroom.title }}
            </option>
          </select>
        </div>
      </div>

      <!-- Tabel Siswa -->
      <div class="shadow-sm card">
        <div class="p-2 card-body">
          <div class="table-responsive">
            <table class="table align-middle table-sm table-bordered table-hover">
              <thead class="text-center table-dark">
                <tr>
                  <th style="width: 5%">No</th>
                  <th style="width: 10%">NIS</th>
                  <th style="width: 10%">NISN</th>
                  <th>Nama</th>
                  <th style="width: 10%">Kelas</th>
                  <th style="width: 10%">Jenis Kelamin</th>
                  <th style="width: 15%">Aksi</th>
                </tr>
              </thead>
              <tbody>
                <!-- Empty State -->
                <tr v-if="students.data.length === 0">
                  <td colspan="7" class="text-center text-muted">Tidak ada data siswa tersedia.</td>
                </tr>
                <!-- Data Rows -->
                <tr v-for="(student, index) in students.data" :key="student.id">
                  <td class="text-center">
                    {{ index + 1 + (students.current_page - 1) * students.per_page }}
                  </td>
                  <td>{{ student.nis }}</td>
                  <td>{{ student.nisn }}</td>
                  <td>{{ student.name }}</td>
                  <td class="text-center">{{ student.classroom?.title || "-" }}</td>
                  <td class="text-center">
                    <span class="badge" :class="student.gender === 'L' ? 'bg-info' : 'bg-warning'">
                      {{ student.gender === "L" ? "Laki-laki" : "Perempuan" }}
                    </span>
                  </td>
                  <td class="text-center">
                    <div class="btn-group btn-group-sm">
                      <Link
                        :href="`/admin/students/${student.id}/edit`"
                        class="btn btn-sm btn-info"
                        title="Edit"
                      >
                        <i class="fa fa-pencil-alt"></i>
                      </Link>
                      <button
                        @click.prevent="destroy(student.id)"
                        class="btn btn-sm btn-danger"
                        title="Hapus"
                      >
                        <i class="fa fa-trash"></i>
                      </button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
          <!-- Pagination -->
          <Pagination :links="students.links" align="end" @navigate="handlePagination" />
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
      students: Object,
      classrooms: Array,
      filters: Object,
    },

    setup(props) {
      const search = ref(props.filters.q || "");
      const selectedClassroom = ref(props.filters.classroom || "");

      const handleSearch = () => {
        router.get("/admin/students", { q: search.value, classroom: selectedClassroom.value });
      };

      const filterByClassroom = () => {
        router.get("/admin/students", { classroom: selectedClassroom.value, q: search.value });
      };

      const handlePagination = (url) => {
        router.get(url, { q: search.value, classroom: selectedClassroom.value });
      };

      const destroy = (id) => {
        Swal.fire({
          title: "Yakin ingin menghapus?",
          text: "Data yang dihapus tidak dapat dikembalikan!",
          icon: "warning",
          showCancelButton: true,
          confirmButtonColor: "#3085d6",
          cancelButtonColor: "#d33",
          confirmButtonText: "Ya, Hapus!",
        }).then((result) => {
          if (result.isConfirmed) {
            router.delete(`/admin/students/${id}`, {
              onSuccess: () =>
                Swal.fire("Berhasil!", "Siswa berhasil dihapus.", "success"),
            });
          }
        });
      };

      return {
        search,
        selectedClassroom,
        handleSearch,
        filterByClassroom,
        handlePagination,
        destroy,
      };
    },
  };
  </script>

  <style scoped>
  .table th,
  .table td {
    padding: 6px;
    font-size: 0.85rem;
  }

  .btn-group-sm .btn {
    padding: 4px 6px;
    font-size: 0.75rem;
  }

  .badge {
    font-size: 0.75rem;
  }

  .form-select-sm,
  .form-control-sm {
    font-size: 0.85rem;
  }
  </style>
