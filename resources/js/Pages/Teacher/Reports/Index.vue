<script>
import { reactive, watch } from "vue";
import { Head, router } from "@inertiajs/vue3";
import LayoutTeacher from "../../../Layouts/Teacher.vue";

export default {
    layout: LayoutTeacher,
  props: {
    errors: Object,
    exams: Array,
    classrooms: Array,
    grades: Array,
  },
  setup(props) {
    const form = reactive({
      exam_id: "" || new URL(document.location).searchParams.get("exam_id"),
      classroom_id: "" || new URL(document.location).searchParams.get("classroom_id"),
    });

    const filteredClassrooms = reactive([]);

    // Watcher untuk memantau perubahan pada ujian
    watch(
      () => form.exam_id,
      (newExamId) => {
        if (newExamId) {
          const selectedExam = props.exams.find((exam) => exam.id === parseInt(newExamId));
          if (selectedExam) {
            // Muat ulang kelas berdasarkan ujian yang dipilih
            filteredClassrooms.splice(0, filteredClassrooms.length, ...selectedExam.classrooms);
            form.classroom_id = ""; // Reset kelas setiap kali ujian berubah
          }
        }
      },
      { immediate: true }
    );

    const filter = () => {
      router.get(
        "/teacher/reports/filter",
        { exam_id: form.exam_id, classroom_id: form.classroom_id },
        {
          preserveState: true,
          preserveScroll: true,
          onSuccess: () => {
            // Perbarui data kelas setelah filter berhasil
            const selectedExam = props.exams.find((exam) => exam.id === parseInt(form.exam_id));
            if (selectedExam) {
              filteredClassrooms.splice(0, filteredClassrooms.length, ...selectedExam.classrooms);
            }
          },
        }
      );
    };

    return {
      form,
      filter,
      filteredClassrooms,
    };
  },
};
</script>

<template>
  <Head>
    <title>Laporan Nilai Ujian - Aplikasi Ujian Online</title>
  </Head>
  <div class="mt-5 mb-5 container-fluid">
    <div class="row">
      <div class="col-md-12">
        <div class="mb-4 border-0 shadow card">
          <div class="card-body">
            <h5><i class="fa fa-filter"></i> Filter Nilai Ujian</h5>
            <hr>
            <form @submit.prevent="filter">
              <div class="row">
                <!-- Pilih Ujian -->
                <div class="col-md-6">
                  <label class="control-label">Ujian</label>
                  <select class="form-select" v-model="form.exam_id">
                    <option value="" disabled selected>Pilih Ujian</option>
                    <option v-for="(exam, index) in exams" :key="index" :value="exam.id">
                      {{ exam.title }} — {{ exam.lesson.title }}
                    </option>
                  </select>
                  <div v-if="errors.exam_id" class="mt-2 alert alert-danger">
                    {{ errors.exam_id }}
                  </div>
                </div>
                <!-- Pilih Kelas -->
                <div class="col-md-6">
                  <label class="control-label">Kelas</label>
                  <select class="form-select" v-model="form.classroom_id">
                    <option value="" disabled>Pilih Kelas</option>
                    <option v-for="(classroom, index) in filteredClassrooms" :key="index" :value="classroom.id">
                      {{ classroom.title }}
                    </option>
                  </select>
                </div>
              </div>
              <div class="mt-3 text-end">
                <button type="submit" class="btn btn-primary"><i class="fa fa-filter"></i> Filter</button>
              </div>
            </form>
          </div>
        </div>

        <div v-if="grades.length > 0" class="border-0 shadow card">
          <div class="card-body">
            <div class="row">
              <div class="col-md-9 col-12">
                <h5 class="mt-2"><i class="fa fa-chart-line"></i> Laporan Nilai Ujian</h5>
              </div>
              <div class="col-md-3 col-12">

                <a
                    :href="`/teacher/reports/export?exam_id=${form.exam_id}&classroom_id=${form.classroom_id || ''}`"
                    target="_blank"
                    class="text-white border-0 shadow btn btn-success btn-md w-100"
                >
                    <i class="fa fa-file-excel"></i> EXPORT EXCEL
                </a>

                <!--<a
                  :href="`/admin/reports/export?exam_id=${form.exam_id}&classroom_id=${form.classroom_id}`"
                  target="_blank"
                  class="text-white border-0 shadow btn btn-success btn-md w-100"
                >
                  <i class="fa fa-file-excel"></i> EXPORT EXCEL
                </a>-->
              </div>
            </div>
            <hr>
            <div class="table-responsive">
              <table class="table mb-0 rounded table-bordered table-centered table-nowrap">
                <thead class="thead-dark">
                  <tr>
                    <th style="width:5%">No.</th>
                    <th>Ujian</th>
                    <th>Sesi</th>
                    <th>Subsesi</th>
                    <th>Nama Siswa</th>
                    <th>Kelas</th>
                    <th>Pelajaran</th>
                    <th>Nilai</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(grade, index) in grades" :key="grade.id">
                    <td class="text-center fw-bold">{{ index + 1 }}</td>
                    <td>{{ grade.exam.title }}</td>
                    <td>{{ grade.exam_session.sesi.title }}</td>
                    <td>{{ grade.exam_session.title }}</td>
                    <td>{{ grade.student.name }}</td>
                    <td class="text-center">{{ grade.student.classroom.title }}</td>
                    <td>{{ grade.exam.lesson.title }}</td>
                    <td class="text-center fw-bold">{{ grade.grade }}</td>
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
