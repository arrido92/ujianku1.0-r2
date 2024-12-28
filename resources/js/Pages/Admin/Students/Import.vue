<template>
    <Head>
      <title>Import Siswa - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">
          <!-- Buttons -->
          <Link href="/admin/students" class="mb-3 border-0 shadow btn btn-md btn-primary me-3">
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>
          <a href="/assets/excel/FormatStudents.xls" target="_blank" class="mb-3 text-white border-0 shadow btn btn-md btn-success">
            <i class="fa fa-file-excel me-2"></i> Contoh Format
          </a>

          <!-- Import Form -->
          <div class="border-0 shadow card">
            <div class="card-body">
              <h5><i class="fa fa-user"></i> Import Siswa</h5>
              <hr />
              <form @submit.prevent="submit">
                <div class="mb-4">
                  <label>File Excel</label>
                  <input
                    type="file"
                    class="form-control"
                    accept=".xls,.xlsx,.csv"
                    @change="handleFileChange"
                  />
                  <!-- Error messages -->
                  <div v-if="errors.file" class="mt-2 alert alert-danger">
                    {{ errors.file }}
                  </div>
                  <div v-if="errors[0]" class="mt-2 alert alert-danger">
                    {{ errors[0] }}
                  </div>
                </div>

                <!-- Buttons -->
                <button type="submit" class="border-0 shadow btn btn-md btn-primary me-2" :disabled="loading">
                  <span v-if="loading">
                    <i class="fa fa-spinner fa-spin"></i> Mengunggah...
                  </span>
                  <span v-else>Upload</span>
                </button>
                <button type="reset" class="border-0 shadow btn btn-md btn-warning" @click="resetForm" :disabled="loading">
                  Reset
                </button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>

  <script>
  import LayoutAdmin from "../../../Layouts/Admin.vue";
  import { Head, Link, router } from "@inertiajs/vue3";
  import { reactive, ref } from "vue";
  import Swal from "sweetalert2";

  export default {
    layout: LayoutAdmin,

    components: {
      Head,
      Link,
    },

    props: {
      errors: Object,
    },

    setup() {
      const form = reactive({
        file: null,
      });
      const loading = ref(false);

      const handleFileChange = (event) => {
        form.file = event.target.files[0];
      };

      const resetForm = () => {
        form.file = null;
      };

      const submit = () => {
        if (!form.file) {
          Swal.fire({
            title: "Error!",
            text: "Silakan pilih file sebelum mengunggah.",
            icon: "error",
            timer: 2000,
            showConfirmButton: false,
          });
          return;
        }

        if (loading.value) return;

        loading.value = true;

        const formData = new FormData();
        formData.append("file", form.file);

        router.post(
          "/admin/students/import",
          formData,
          {
            onSuccess: () => {
              Swal.fire({
                title: "Success!",
                text: "Import siswa berhasil disimpan.",
                icon: "success",
                timer: 2000,
                showConfirmButton: false,
              });
              resetForm();
            },
            onError: () => {
              Swal.fire({
                title: "Error!",
                text: "Terjadi kesalahan saat mengunggah file.",
                icon: "error",
                timer: 2000,
                showConfirmButton: false,
              });
            },
            onFinish: () => {
              loading.value = false;
            },
          }
        );
      };

      return {
        form,
        handleFileChange,
        resetForm,
        submit,
        loading,
      };
    },
  };
  </script>

  <style>
  button[disabled] {
    opacity: 0.6;
    cursor: not-allowed;
  }
  </style>
