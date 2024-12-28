<template>
    <Head>
      <title>Tambah Mata Pelajaran - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">
          <Link
            href="/admin/lessons"
            class="mb-3 border-0 shadow btn btn-md btn-primary"
            type="button"
          >
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>
          <div class="border-0 shadow card">
            <div class="card-body">
              <h5><i class="fa fa-bookmark"></i> Tambah Pelajaran</h5>
              <hr>
              <form @submit.prevent="submit">
                <!-- Nama Pelajaran -->
                <div class="mb-4">
                  <label>Nama Pelajaran</label>
                  <input
                    type="text"
                    class="form-control"
                    placeholder="Masukkan Nama Pelajaran"
                    v-model="form.title"
                  />

                  <!-- Error Message -->
                  <div v-if="errors.title" class="mt-2 alert alert-danger">
                    {{ errors.title }}
                  </div>
                </div>

                <!-- Buttons -->
                <button
                  type="submit"
                  class="border-0 shadow btn btn-md btn-primary me-2"
                  :disabled="loading"
                >
                  <span v-if="loading">
                    <i class="fa fa-spinner fa-spin"></i> Menyimpan...
                  </span>
                  <span v-else>Simpan</span>
                </button>
                <button
                  type="button"
                  class="border-0 shadow btn btn-md btn-warning"
                  @click="resetForm"
                  :disabled="loading"
                >
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
      // Define form state
      const form = reactive({
        title: "",
      });

      // Loading state
      const loading = ref(false);

      // Submit method
      const submit = () => {
        // Prevent duplicate submissions
        if (loading.value) return;

        loading.value = true;

        // Send data to server
        router.post(
          "/admin/lessons",
          { title: form.title },
          {
            onSuccess: () => {
              // Show success alert
              Swal.fire({
                title: "Success!",
                text: "Pelajaran Berhasil Disimpan!",
                icon: "success",
                showConfirmButton: false,
                timer: 2000,
              });

              // Reset form
              resetForm();
            },
            onError: () => {
              // Errors will be handled automatically via props
            },
            onFinish: () => {
              // Reset loading state
              loading.value = false;
            },
          }
        );
      };

      // Reset form method
      const resetForm = () => {
        form.title = "";
      };

      return {
        form,
        loading,
        submit,
        resetForm,
      };
    },
  };
  </script>

  <style>
  /* Tambahkan efek loading */
  button[disabled] {
    opacity: 0.6;
    cursor: not-allowed;
  }
  </style>
