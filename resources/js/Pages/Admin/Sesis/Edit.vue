<template>
    <Head>
      <title>Edit Sesi - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">
          <!-- Button Kembali -->
          <Link
            href="/admin/sesis"
            class="mb-3 border-0 shadow btn btn-md btn-primary"
            type="button"
          >
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>
          <div class="border-0 shadow card">
            <div class="card-body">
              <h5><i class="fa fa-clone"></i> Edit Sesi</h5>
              <hr />
              <form @submit.prevent="submit">
                <!-- Nama Kelas -->
                <div class="mb-4">
                  <label>Nama sesi</label>
                  <input
                    type="text"
                    class="form-control"
                    placeholder="Masukkan nama sesi"
                    v-model="form.title"
                    :disabled="loading"
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
                  <span v-else>Update</span>
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
      sesi: Object,
    },

    setup(props) {
      // Define form state
      const form = reactive({
        title: props.sesi.title,
      });

      // Define loading state
      const loading = ref(false);

      // Submit method
      const submit = () => {
        if (!form.title.trim()) {
          Swal.fire({
            title: "Error!",
            text: "Nama kelas tidak boleh kosong.",
            icon: "error",
            timer: 2000,
            showConfirmButton: false,
          });
          return;
        }

        if (loading.value) return; // Prevent duplicate submissions
        loading.value = true;

        router.put(
          `/admin/sesis/${props.sesi.id}`,
          { title: form.title },
          {
            onSuccess: () => {
              Swal.fire({
                title: "Berhasil!",
                text: "Kelas berhasil diperbarui.",
                icon: "success",
                showCancelButton: true,
                confirmButtonText: "Tetap di halaman",
                cancelButtonText: "Kembali ke daftar",
              }).then((result) => {
                if (result.isDismissed) {
                  router.get("/admin/sesis");
                }
              });
            },
            onError: () => {
              // Error handling handled by props.errors
            },
            onFinish: () => {
              loading.value = false;
            },
          }
        );
      };

      // Reset form method
      const resetForm = () => {
        form.title = props.sesi.title;
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
  button[disabled] {
    opacity: 0.6;
    cursor: not-allowed;
  }
  </style>
