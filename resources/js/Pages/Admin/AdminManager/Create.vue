<template>
    <Head>
      <title>Tambah Admin - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">
          <!-- Button Kembali -->
          <Link
            href="/admin/admin-manager"
            class="mb-3 border-0 shadow btn btn-md btn-primary"
            type="button"
          >
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>
          <div class="border-0 shadow card">
            <div class="card-body">
              <h5><i class="fa fa-clone"></i> Tambah Admin</h5>
              <hr />
              <form @submit.prevent="submit">
                <!-- Nama -->


                <div class="mb-4">
                  <label>Nama</label>
                  <input
                    type="text"
                    class="form-control"
                    placeholder="Masukkan Nama"
                    v-model="form.name"
                    :disabled="loading"
                  />
                  <!-- Error Message -->
                  <div v-if="errors.name" class="mt-2 alert alert-danger">
                    {{ errors.name }}
                  </div>
                </div>

                <div class="row">
                <div class="col-md-6">
                <div class="mb-4">
                  <label>Email</label>
                  <input
                    type="email"
                    class="form-control"
                    placeholder="Masukkan Email"
                    v-model="form.email"
                    :disabled="loading"
                  />
                  <!-- Error Message -->
                  <div v-if="errors.email" class="mt-2 alert alert-danger">
                    {{ errors.email }}
                  </div>
                </div>
                </div>
                <!-- Jenis Kelamin -->
                <div class="col-md-6">
                <div class="mb-4">
                  <label>Hak Akses</label>
                  <select class="form-select" v-model="form.role">
                    <option value="admin">Administrator</option>
                    <option value="teacher">Guru</option>
                  </select>
                  <div v-if="errors.role" class="mt-2 alert alert-danger">
                    {{ errors.role }}
                  </div>
                </div>
                </div>
                </div>

                 <!-- Password -->
                 <div class="row">
                  <div class="col-md-6">
                    <div class="mb-4">
                      <label>Password</label>
                      <input
                        type="password"
                        class="form-control"
                        placeholder="Masukkan Password"
                        v-model="form.password"
                      />
                      <div v-if="errors.password" class="mt-2 alert alert-danger">
                        {{ errors.password }}
                      </div>
                    </div>
                  </div>
                  <div class="col-md-6">
                    <div class="mb-4">
                      <label>Konfirmasi Password</label>
                      <input
                        type="password"
                        class="form-control"
                        placeholder="Masukkan Konfirmasi Password"
                        v-model="form.password_confirmation"
                      />
                    </div>
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
        name: "",
        email: "",
        role: "",
        password: "",
        password_confirmation: "",
      });

      // Define loading state
      const loading = ref(false);

      // Submit method
      const submit = () => {
        if (loading.value) return; // Prevent duplicate submissions
        loading.value = true;

        router.post(
          "/admin/admin-manager",
          { name: form.name,
            email: form.email,
            role: form.role,
            password: form.password,
            password_confirmation: form.password_confirmation,
           },
          {
            onSuccess: () => {
              Swal.fire({
                title: "Berhasil!",
                text: "Kelas berhasil disimpan.",
                icon: "success",
                showCancelButton: true,
                confirmButtonText: "Tambah lagi",
                cancelButtonText: "Kembali ke daftar",
              }).then((result) => {
                if (result.isConfirmed) {
                  resetForm();
                } else {
                  router.get("/admin/admin-manager");
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
        form.name = "";
        form.email = "";
        form.role = "";
        form.password = "";
        form.password_confirmation = "";
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
