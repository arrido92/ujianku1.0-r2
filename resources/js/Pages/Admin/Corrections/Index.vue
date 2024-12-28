<template>
    <Head>
      <title>Koreksi Essay - {{ exam.title }}</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">
          <Link href="/admin/exams" class="mb-3 btn btn-primary">
            <i class="fa fa-arrow-left"></i> Kembali
          </Link>

            <h5 style="text-align: center;">KOREKSI SOAL ESSAY - {{ exam.title }}</h5>

          <div class="border-0 shadow card">
            <div class="card-body">
              <div class="table-responsive">
                <table class="table mb-0 rounded table-bordered table-centered table-nowrap">
                  <thead class="thead-dark">
                    <tr class="border-0">
                      <th class="border-0 rounded-start" style="width:5%">No</th>
                      <th class="border-0" style="width:20%">Siswa</th>
                      <th class="border-0" style="width:30%">Soal</th>
                      <th class="border-0" style="width:30%">Jawaban</th>
                      <th class="border-0 rounded-end" style="width:15%">Koreksi Nilai</th>

                    </tr>
                  </thead>
                  <tbody>
                    <!-- Empty State -->
                    <tr v-if="answers.length === 0" class="text-center">
                      <td colspan="6" class="fw-bold">Tidak ada data jawaban yang ditemukan.</td>
                    </tr>
                    <!-- Data -->
                    <tr v-else v-for="(answer, index) in answers" :key="answer.id">
                      <td class="text-center">{{ index + 1 }}</td>
                      <td class="text-wrap">{{ answer.student.name }}</td>
                      <td v-html="answer.question.question" class="text-wrap"></td>
                      <td class="text-wrap">{{ answer.answer }}</td>
                      <td>
                        <dl>
                            <dt><input
                          type="number"
                          class="form-control form-control-sm"
                          v-model="answer.score"
                          min="0"
                          max="100"
                        /></dt>
                        <dt><br><button
                          class="btn btn-sm btn-success"
                          @click="updateScore(answer.id, answer.score)"
                        >
                          Simpan
                        </button></dt>
                      </dl>
                      </td>

                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>

  <script>
  import LayoutAdmin from "../../../Layouts/Admin.vue";
  import { Head, Link } from "@inertiajs/vue3";
  import Swal from "sweetalert2";
  import axios from "axios";

  export default {
    layout: LayoutAdmin,
    components: {
      Head,
      Link,
    },
    props: {
      exam: Object,
      answers: Array,
    },
    setup() {
      const updateScore = (answerId, score) => {
        Swal.fire({
          title: "Konfirmasi",
          text: "Yakin ingin menyimpan nilai ini?",
          icon: "question",
          showCancelButton: true,
          confirmButtonText: "Ya, Simpan",
          cancelButtonText: "Batal",
        }).then((result) => {
          if (result.isConfirmed) {
            axios
              .put(`/admin/corrections/${answerId}`, { score })
              .then((response) => {
                if (response.data.success) {
                  Swal.fire({
                    title: "Berhasil!",
                    text: response.data.message,
                    icon: "success",
                    timer: 2000,
                    showConfirmButton: false,
                  });
                }
              })
              .catch((error) => {
                Swal.fire({
                  title: "Gagal!",
                  text: "Terjadi kesalahan saat menyimpan nilai.",
                  icon: "error",
                });
                console.error(error.response?.data || error);
              });
          }
        });
      };

      return {
        updateScore,
      };
    },
  };
  </script>

  <style scoped>
  .table th,
  .table td {
    vertical-align: middle;
    text-align: center;
  }

  .table-responsive {
    overflow-x: auto;
  }

  .text-wrap {
    word-wrap: break-word;
    white-space: pre-wrap;
  }
  </style>
