<template>
    <Head>
      <title>Profil Saya - Aplikasi Ujian Online</title>
    </Head>
    <div class="container mt-5">
      <h4 class="fw-bold">Profil Saya</h4>
      <div class="mt-4 shadow card">
        <div class="card-body">
          <form @submit.prevent="updateProfile">
            <div class="mb-3">
              <label for="name" class="form-label">Nama</label>
              <input
                type="text"
                id="name"
                v-model="form.name"
                class="form-control"
                :class="{ 'is-invalid': errors.name }"
              />
              <div v-if="errors.name" class="invalid-feedback">
                {{ errors.name }}
              </div>
            </div>
            <div class="mb-3">
              <label for="email" class="form-label">Email</label>
              <input
                type="email"
                id="email"
                v-model="form.email"
                class="form-control"
                :class="{ 'is-invalid': errors.email }"
              />
              <div v-if="errors.email" class="invalid-feedback">
                {{ errors.email }}
              </div>
            </div>
            <div class="mb-3">
              <label for="password" class="form-label">Kata Sandi Baru (Opsional)</label>
              <input
                type="password"
                id="password"
                v-model="form.password"
                class="form-control"
                :class="{ 'is-invalid': errors.password }"
              />
              <div v-if="errors.password" class="invalid-feedback">
                {{ errors.password }}
              </div>
            </div>
            <div class="mb-3">
              <label for="password_confirmation" class="form-label">Konfirmasi Kata Sandi</label>
              <input
                type="password"
                id="password_confirmation"
                v-model="form.password_confirmation"
                class="form-control"
                :class="{ 'is-invalid': errors.password_confirmation }"
              />
              <div v-if="errors.password_confirmation" class="invalid-feedback">
                {{ errors.password_confirmation }}
              </div>
            </div>
            <button type="submit" class="btn btn-primary">Perbaharui Data</button>
          </form>
        </div>
      </div>
    </div>
  </template>

  <script>
  import LayoutTeacher from "../../../Layouts/Teacher.vue";
  import { reactive } from "vue";
  import axios from "axios";
  import Swal from "sweetalert2";

  export default {
    layout: LayoutTeacher,
    props: {
      user: Object,
      errors: Object,
    },
    setup(props) {
      const form = reactive({
        name: props.user.name,
        email: props.user.email,
        password: "",
        password_confirmation: "",
      });

      const updateProfile = () => {
        axios
          .post("/teacher/profile/update", form)
          .then((response) => {
            Swal.fire({
              title: "Berhasil",
              text: "Profil berhasil diperbarui.",
              icon: "success",
              timer: 2000,
              showConfirmButton: false,
            });
            form.password = "";
            form.password_confirmation = "";
          })
          .catch((error) => {
            Swal.fire({
              title: "Gagal",
              text: error.response?.data?.message || "Terjadi kesalahan saat memperbarui profil.",
              icon: "error",
            });
          });
      };

      return { form, updateProfile };
    },
  };
  </script>

  <style scoped>
  .container {
    max-width: 600px;
  }
  .card {
    border-radius: 8px;
    overflow: hidden;
  }
  </style>
