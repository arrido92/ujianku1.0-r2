<template>
    <Head>
      <title>Edit Siswa - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">
          <!-- Button Kembali -->
          <Link href="/admin/students" class="mb-3 border-0 shadow btn btn-md btn-primary">
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>
          <div class="border-0 shadow card">
            <div class="card-body">
              <h5><i class="fa fa-user"></i> Edit Siswa</h5>
              <hr />
              <form @submit.prevent="submit">
                <div class="row">
                    <!-- NIS -->
                  <div class="col-md-6">
                    <div class="mb-4">
                      <label>NIS</label>
                      <input
                        type="text"
                        class="form-control"
                        placeholder="Masukkan NIS Siswa"
                        v-model="form.nis"
                      />
                      <div v-if="errors.nis" class="mt-2 alert alert-danger">{{ errors.nis }}</div>
                    </div>
                  </div>
                  <!-- NISN -->
                  <div class="col-md-6">
                    <div class="mb-4">
                      <label>NISN</label>
                      <input
                        type="text"
                        class="form-control"
                        placeholder="Masukkan NISN Siswa"
                        v-model="form.nisn"
                      />
                      <div v-if="errors.nisn" class="mt-2 alert alert-danger">{{ errors.nisn }}</div>
                    </div>
                  </div>
                  <!-- Nama Lengkap -->
                  <div class="col-md-6">
                    <div class="mb-4">
                      <label>Nama Lengkap</label>
                      <input
                        type="text"
                        class="form-control"
                        placeholder="Masukkan Nama Siswa"
                        v-model="form.name"
                      />
                      <div v-if="errors.name" class="mt-2 alert alert-danger">{{ errors.name }}</div>
                    </div>
                  </div>
                </div>

                <div class="row">
                  <!-- Kelas -->
                  <div class="col-md-6">
                    <div class="mb-4">
                      <label>Kelas</label>
                      <select class="form-select" v-model="form.classroom_id">
                        <option disabled value="">Pilih Kelas</option>
                        <option
                          v-for="(classroom, index) in classrooms"
                          :key="index"
                          :value="classroom.id"
                        >
                          {{ classroom.title }}
                        </option>
                      </select>
                      <div v-if="errors.classroom_id" class="mt-2 alert alert-danger">
                        {{ errors.classroom_id }}
                      </div>
                    </div>
                  </div>
                  <!-- Jenis Kelamin -->
                  <div class="col-md-6">
                    <div class="mb-4">
                      <label>Jenis Kelamin</label>
                      <select class="form-select" v-model="form.gender">
                        <option value="L">Laki - Laki</option>
                        <option value="P">Perempuan</option>
                      </select>
                      <div v-if="errors.gender" class="mt-2 alert alert-danger">{{ errors.gender }}</div>
                    </div>
                  </div>
                </div>

                <div class="row">
                  <!-- Password -->
                  <div class="col-md-6">
                    <div class="mb-4">
                      <label>Password</label>
                      <input
                        type="password"
                        class="form-control"
                        placeholder="Masukkan Password (Opsional)"
                        v-model="form.password"
                      />
                      <div v-if="errors.password" class="mt-2 alert alert-danger">{{ errors.password }}</div>
                    </div>
                  </div>
                  <!-- Konfirmasi Password -->
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
                    <i class="fa fa-spinner fa-spin"></i> Memperbarui...
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
      classrooms: Array,
      student: Object,
    },

    setup(props) {
      const loading = ref(false);

      // Define form with initial values
      const form = reactive({
        nis: props.student.nis,
        nisn: props.student.nisn,
        name: props.student.name,
        classroom_id: props.student.classroom_id,
        gender: props.student.gender,
        password: "",
        password_confirmation: "",
      });

      // Reset form to initial values
      const resetForm = () => {
        form.nis = props.student.nis;
        form.nisn = props.student.nisn;
        form.name = props.student.name;
        form.classroom_id = props.student.classroom_id;
        form.gender = props.student.gender;
        form.password = "";
        form.password_confirmation = "";
      };

      // Submit method
      const submit = () => {
        if (loading.value) return; // Prevent duplicate submissions
        loading.value = true;

        router.put(
          `/admin/students/${props.student.id}`,
          {
            nis: form.nis,
            nisn: form.nisn,
            name: form.name,
            classroom_id: form.classroom_id,
            gender: form.gender,
            password: form.password,
            password_confirmation: form.password_confirmation,
          },
          {
            onSuccess: () => {
              Swal.fire({
                title: "Success!",
                text: "Siswa berhasil diperbarui.",
                icon: "success",
                showConfirmButton: false,
                timer: 2000,
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

      return {
        form,
        submit,
        resetForm,
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
