<template>
    <Head>
      <title>Sesi Kelas - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <!-- Toolbar -->
      <div class="row">
        <div class="col-md-8">
          <div class="row">
            <!-- Button Tambah -->
            <div class="mb-2 col-md-3 col-12">
              <Link
                href="/admin/sesis/create"
                class="border-0 shadow btn btn-md btn-primary w-100"
                type="button"
              >
                <i class="fa fa-plus-circle"></i> Tambah
              </Link>
            </div>
            <!-- Search Form -->
            <div class="mb-2 col-md-9 col-12">
              <form @submit.prevent="handleSearch">
                <div class="input-group">
                  <input
                    type="text"
                    class="border-0 shadow form-control"
                    v-model="search"
                    placeholder="Masukkan kata kunci dan tekan Enter..."
                  />
                  <button
                    class="border-0 shadow btn btn-outline-secondary"
                    type="submit"
                  >
                    <i class="fa fa-search"></i>
                  </button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>

      <!-- Tabel Kelas -->
      <div class="mt-1 row">
        <div class="col-md-12">
          <div class="border-0 shadow card">
            <div class="card-body">
              <div class="table-responsive">
                <table class="table mb-0 rounded table-bordered table-centered table-nowrap">
                  <thead class="thead-dark">
                    <tr>
                      <th class="border-0 rounded-start" style="width:5%">No.</th>
                      <th class="border-0">Nama Sesi</th>
                      <th class="border-0 rounded-end" style="width:15%">Aksi</th>
                    </tr>
                  </thead>
                  <tbody>
                    <!-- Empty State -->
                    <tr v-if="sesis.data.length === 0">
                      <td colspan="3" class="text-center fw-bold">
                        Tidak ada data tersedia.
                      </td>
                    </tr>
                    <!-- Data Rows -->
                    <tr v-else v-for="(sesi, index) in sesis.data" :key="sesi.id">
                      <td class="text-center fw-bold">
                        {{ index + 1 + (sesis.current_page - 1) * sesis.per_page }}
                      </td>
                      <td>{{ sesi.title }}</td>
                      <td class="text-center">
                        <!-- Edit Button -->
                        <Link
                          :href="`/admin/sesis/${sesi.id}/edit`"
                          class="border-0 shadow btn btn-sm btn-info me-2"
                        >
                          <i class="fa fa-pencil-alt"></i>
                        </Link>
                        <!-- Delete Button -->
                        <button
                          @click.prevent="destroy(sesi.id)"
                          class="border-0 shadow btn btn-sm btn-danger"
                        >
                          <i class="fa fa-trash"></i>
                        </button>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <!-- Pagination -->
              <Pagination :links="sesis.links" align="end" />
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
      sesis: Object,
    },

    setup() {
      // Search state
      const search = ref(new URLSearchParams(window.location.search).get("q") || "");

      // Handle search
      const handleSearch = () => {
        if (!search.value.trim()) return; // Prevent empty search
        router.get("/admin/sesis", { q: search.value.trim() });
      };

      // Handle delete
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
            router.delete(`/admin/sesis/${id}`, {
              onSuccess: () => {
                Swal.fire({
                  title: "Berhasil!",
                  text: "Kelas berhasil dihapus.",
                  icon: "success",
                  timer: 2000,
                  showConfirmButton: false,
                });
              },
              onError: () => {
                Swal.fire({
                  title: "Gagal!",
                  text: "Terjadi kesalahan saat menghapus data.",
                  icon: "error",
                  timer: 2000,
                  showConfirmButton: false,
                });
              },
            });
          }
        });
      };

      return {
        search,
        handleSearch,
        destroy,
      };
    },
  };
  </script>

  <style>
  .table-centered {
    text-align: center;
    vertical-align: middle;
  }
  button[disabled] {
    opacity: 0.6;
    cursor: not-allowed;
  }
  </style>
