<template>
    <Head>
      <title>Import Pengguna - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">
          <!-- Tombol Kembali -->
          <Link href="/admin/admin-manager" class="mb-3 border-0 shadow btn btn-md btn-primary me-3" type="button">
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>
          <!-- Download Contoh Format -->
          <a href="/assets/excel/Pengguna.xls" target="_blank" class="mb-3 text-white border-0 shadow btn btn-md btn-success">
            <i class="fa fa-file-excel me-2"></i> Contoh Format
          </a>
          <div class="border-0 shadow card">
            <div class="card-body">
              <h5><i class="fa fa-user"></i> Import Pengguna Sebagai Guru</h5>
              <hr>
              <form @submit.prevent="submit">
                <!-- Input File -->
                <div class="mb-4">
                  <label>File Excel</label>
                  <input type="file" class="form-control" @change="handleFileUpload" />
                  <div v-if="errors.file" class="mt-2 alert alert-danger">
                    {{ errors.file }}
                  </div>
                  <div v-if="errors[0]" class="mt-2 alert alert-danger">
                    {{ errors[0] }}
                  </div>
                </div>

                <!-- Tombol -->
                <button type="submit" class="border-0 shadow btn btn-md btn-primary me-2">Upload</button>
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
  import { reactive } from 'vue';
  import Swal from 'sweetalert2';

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
        file: null,
      });

      // Handle file upload
      const handleFileUpload = (event) => {
        form.file = event.target.files[0];
      };

      // Reset form
      const resetForm = () => {
        form.file = null;
      };

      // Submit form
      const submit = () => {
        const formData = new FormData();
        formData.append('file', form.file);

        router.post('/admin/admin-manager/import', formData, {
          onSuccess: () => {
            Swal.fire({
              title: 'Success!',
              text: 'Data guru berhasil diimpor.',
              icon: 'success',
              timer: 2000,
              showConfirmButton: false,
            });
          },
        });
      };

      return {
        form,
        handleFileUpload,
        resetForm,
        submit,
      };
    },
  };
  </script>

  <style>
  /* Tambahkan gaya sesuai kebutuhan */
  </style>
