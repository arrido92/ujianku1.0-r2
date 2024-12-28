<template>
    <Head>
      <title>Tambah Sesi Ujian - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">
          <Link href="/admin/exam_sessions" class="mb-3 border-0 shadow btn btn-md btn-primary" type="button">
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>
          <div class="border-0 shadow card">
            <div class="card-body">
              <h5><i class="fas fa-stopwatch"></i> Tambah Sesi</h5>
              <hr>
              <form @submit.prevent="submit">
                <div class="row">
                  <div class="col-md-4">
                    <div class="mb-4">
                      <label>Sesi</label>
                      <select
                        class="form-select"
                        v-model="form.sesi_id"
                        :class="{ 'is-invalid': errors.sesi_id }"
                      >
                        <option value="" disabled>Pilih Sesi</option>
                        <option v-for="(sesi, index) in sesis" :key="index" :value="sesi.id">
                          {{ sesi.title }}
                        </option>
                      </select>
                      <div v-if="errors.sesi_id" class="invalid-feedback">
                        {{ errors.sesi_id }}
                      </div>
                    </div>
                  </div>

                  <div class="col-md-4">
                    <div class="mb-4">
                      <label>Sub Sesi</label>
                      <input
                        type="text"
                        class="form-control"
                        placeholder="Masukkan Nama Sesi"
                        v-model="form.title"
                        :class="{ 'is-invalid': errors.title }"
                      />
                      <div v-if="errors.title" class="invalid-feedback">
                        {{ errors.title }}
                      </div>
                    </div>
                  </div>

                  <div class="col-md-4">
                    <div class="mb-4">
                      <label>Ujian</label>
                      <select
                        class="form-select"
                        v-model="form.exam_id"
                        @change="loadClassrooms"
                        :class="{ 'is-invalid': errors.exam_id }"
                      >
                        <option value="" disabled>Pilih Ujian</option>
                        <option v-for="(exam, index) in exams" :key="index" :value="exam.id">
                          {{ exam.title }}
                        </option>
                      </select>
                      <div v-if="errors.exam_id" class="invalid-feedback">
                        {{ errors.exam_id }}
                      </div>
                    </div>
                  </div>
                </div>

                <!-- Pilih Kelas -->
                <div class="mb-4">
                  <label for="classrooms">Pilih Kelas</label>
                  <select
                    v-model="form.classrooms"
                    multiple
                    class="form-select"
                    :class="{ 'is-invalid': errors.classrooms }"
                  >
                    <option v-for="(classroom, index) in filteredClassrooms" :key="index" :value="classroom.id">
                      {{ classroom.title }}
                    </option>
                  </select>
                  <div v-if="errors.classrooms" class="invalid-feedback">
                    {{ errors.classrooms }}
                  </div>
                </div>

                <div class="row">
                  <div class="col-md-6">
                    <div class="mb-4">
                      <label>Waktu Mulai</label>
                      <Datepicker
                        v-model="form.start_time"
                        :class="{ 'is-invalid': errors.start_time }"
                      />
                      <div v-if="errors.start_time" class="invalid-feedback">
                        {{ errors.start_time }}
                      </div>
                    </div>
                  </div>
                  <div class="col-md-6">
                    <div class="mb-4">
                      <label>Waktu Selesai</label>
                      <Datepicker
                        v-model="form.end_time"
                        :class="{ 'is-invalid': errors.end_time }"
                      />
                      <div v-if="errors.end_time" class="invalid-feedback">
                        {{ errors.end_time }}
                      </div>
                    </div>
                  </div>
                </div>

                <div class="d-flex justify-content-end">
                  <button
                    type="submit"
                    class="border-0 shadow btn btn-md btn-primary me-2"
                    :disabled="isSubmitting"
                  >
                    <span v-if="isSubmitting" class="spinner-border spinner-border-sm me-2"></span>
                    Simpan
                  </button>
                  <button
                    type="reset"
                    class="border-0 shadow btn btn-md btn-warning"
                    @click="resetForm"
                  >
                    Reset
                  </button>
                </div>
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
  import { ref } from "vue";
  import Swal from "sweetalert2";
  import Datepicker from "@vuepic/vue-datepicker";
  import "@vuepic/vue-datepicker/dist/main.css";

  export default {
    layout: LayoutAdmin,
    components: {
      Head,
      Link,
      Datepicker,
    },
    props: {
      exams: Array,
      sesis: Array,
      errors: Object,
    },
    data() {
      return {
        form: {
          title: "",
          exam_id: "",
          sesi_id: "",
          classrooms: [],
          start_time: null,
          end_time: null,
        },
        filteredClassrooms: [],
        isSubmitting: false,
      };
    },
    methods: {
      loadClassrooms() {
        const selectedExam = this.exams.find((exam) => exam.id === this.form.exam_id);
        this.filteredClassrooms = selectedExam?.classrooms || [];
      },
      async submit() {
        this.isSubmitting = true;

        // Konfirmasi sebelum submit
        const confirmResult = await Swal.fire({
          title: "Konfirmasi",
          text: "Apakah Anda yakin ingin menyimpan sesi ujian ini?",
          icon: "warning",
          showCancelButton: true,
          confirmButtonText: "Ya, Simpan!",
          cancelButtonText: "Batal",
        });

        if (!confirmResult.isConfirmed) {
          this.isSubmitting = false;
          return;
        }

        this.$inertia.post(
          "/admin/exam_sessions",
          this.form,
          {
            onSuccess: () => {
              Swal.fire({
                title: "Berhasil!",
                text: "Sesi ujian berhasil disimpan.",
                icon: "success",
                timer: 2000,
                showConfirmButton: false,
              });
              this.resetForm();
            },
            onError: () => {
              Swal.fire({
                title: "Gagal!",
                text: "Terjadi kesalahan saat menyimpan data.",
                icon: "error",
                confirmButtonText: "OK",
              });
            },
            onFinish: () => {
              this.isSubmitting = false;
            },
          }
        );
      },
      async resetForm() {
        const confirmResult = await Swal.fire({
          title: "Konfirmasi",
          text: "Apakah Anda yakin ingin mengosongkan semua input?",
          icon: "warning",
          showCancelButton: true,
          confirmButtonText: "Ya, Reset!",
          cancelButtonText: "Batal",
        });

        if (confirmResult.isConfirmed) {
          this.form = {
            title: "",
            exam_id: "",
            sesi_id: "",
            classrooms: [],
            start_time: null,
            end_time: null,
          };
          this.filteredClassrooms = [];
          Swal.fire({
            title: "Direset!",
            text: "Form berhasil direset.",
            icon: "success",
            timer: 1500,
            showConfirmButton: false,
          });
        }
      },
    },
  };
  </script>

  <style>
  .is-invalid {
    border-color: #dc3545 !important;
  }

  .invalid-feedback {
    display: block;
    color: #dc3545;
  }
  </style>
