<template>
    <Head>
      <title>Edit Pengguna - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">
          <Link href="/admin/admin-manager" class="mb-3 border-0 shadow btn btn-md btn-primary" type="button">
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>
          <div class="border-0 shadow card">
            <div class="card-body">
              <h5><i class="fa fa-user"></i> Edit Pengguna</h5>
              <hr>
              <form @submit.prevent="submit">

                <!-- Nama Guru -->
                <div class="mb-4">
                  <label>Nama Lengkap</label>
                  <input type="text" class="form-control" placeholder="Masukkan Nama" v-model="form.name" />
                  <div v-if="errors.name" class="mt-2 alert alert-danger">
                    {{ errors.name }}
                  </div>
                </div>

                <!-- Email -->
                <div class="row">
                    <div class="col-md-6">
                <div class="mb-4">
                  <label>Email</label>
                  <input type="text" class="form-control" placeholder="Masukkan Email" v-model="form.email" />
                  <div v-if="errors.email" class="mt-2 alert alert-danger">
                    {{ errors.email }}
                  </div>
                </div>
                </div>
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
                      <label>Password Baru (Opsional)</label>
                      <input
                        type="password"
                        class="form-control"
                        placeholder="Masukkan Password Baru"
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

                <!-- Tombol -->
                <button type="submit" class="border-0 shadow btn btn-md btn-primary me-2">Update</button>
                <button type="reset" class="border-0 shadow btn btn-md btn-warning" @click="resetForm">Reset</button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>

  <script>
  import LayoutAdmin from '../../../Layouts/Admin.vue';
  import { Head, Link, router } from '@inertiajs/vue3';
  import { reactive, watch } from 'vue';
  import Swal from 'sweetalert2';

  export default {
    layout: LayoutAdmin,

    components: {
      Head,
      Link,
    },

    props: {
      errors: Object,
      users: Object, // Data guru yang diambil dari backend
    },

    setup(props) {
      // Inisialisasi form dengan data yang diterima dari props
      const form = reactive({
        name: props.users.name,
        email: props.users.email,
        role: props.users.role,
        password: '', // Optional
        password_confirmation: '', // Optional
      });

      const resetForm = () => {
        form.name = props.users.name;
        form.email = props.users.email;
        form.role = props.users.role;
        form.password = '';
        form.password_confirmation = '';
      };

      const submit = () => {
    console.log("Submitting form data:", form); // Log data sebelum dikirim
    router.put(`/admin/admin-manager/${props.users.id}`, form, {
        onSuccess: () => {
            console.log("User updated successfully!");
            Swal.fire({
                title: 'Success!',
                text: 'Data pengguna berhasil diperbarui.',
                icon: 'success',
                timer: 2000,
                showConfirmButton: false,
            });
        },
        onError: (error) => {
            console.error("Error updating user:", error); // Log error
            Swal.fire({
                title: 'Error!',
                text: 'Terjadi kesalahan saat memperbarui data.',
                icon: 'error',
                timer: 2000,
                showConfirmButton: false,
            });
        },
    });
};


      return {
        form,
        resetForm,
        submit,
      };
    },
  };
  </script>

  <style>
  /* Tambahkan gaya sesuai kebutuhan */
  </style>
