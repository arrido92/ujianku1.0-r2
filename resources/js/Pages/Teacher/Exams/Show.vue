<template>
    <Head>
      <title>Detail Ujian - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">
          <Link href="/teacher/exams" class="mb-3 border-0 shadow btn btn-md btn-primary">
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>

          <div class="mb-4 border-0 shadow card">
            <div class="card-body">
              <h5><i class="fa fa-edit"></i> Detail Ujian</h5>
              <hr />
              <div class="table-responsive">
                <table class="table mb-0 rounded table-bordered table-centered table-nowrap table-striped">
                  <tbody>
                    <tr>
                      <td style="width:30%" class="fw-bold">Nama Ujian</td>
                      <td>{{ exam.title }}</td>
                    </tr>
                    <tr>
                      <td class="fw-bold">Mata Pelajaran</td>
                      <td>{{ exam.lesson.title }}</td>
                    </tr>
                    <tr>
                      <td class="fw-bold">Kelas</td>
                      <td>
                        <span v-for="(classroom, index) in exam.classrooms" :key="index">
                          {{ classroom.title }}
                          <span v-if="index < exam.classrooms.length - 1">, </span>
                        </span>
                      </td>
                    </tr>
                    <tr>
                      <td class="fw-bold">Jumlah Soal</td>
                      <td>{{ exam.questions.data.length }}</td>
                    </tr>
                    <tr>
                      <td class="fw-bold">Komponen Soal</td>
                      <td>
                          <span v-for="(component, idx) in parseGradingComponents(exam.grading_components)" :key="idx" class="text-white badge bg-info me-1">
                            {{ component.type }} = {{ component.weight }}%
                          </span>
                        </td>
                    </tr>
                    <tr>
                      <td class="fw-bold">Durasi (Menit)</td>
                      <td>{{ exam.duration }} Menit</td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>

          <div class="border-0 shadow card">
            <div class="card-body">
              <h5><i class="fa fa-question-circle"></i> Soal Ujian</h5>
              <hr />
              <Link :href="`/teacher/exams/${exam.id}/questions/create`" class="border-0 shadow btn btn-sm btn-primary me-2">
                <i class="fa fa-plus-circle"></i> Tambah Soal
              </Link>
              <Link :href="`/teacher/exams/${exam.id}/questions/import-excel`" class="text-white border-0 shadow btn btn-sm btn-success me-2">
                <i class="fa fa-file-archive"></i> Import ZIP
              </Link>
              <Link :href="`/teacher/exams/${exam.id}/questions/import-word`" class="text-white border-0 shadow btn btn-sm btn-info">
                <i class="fa fa-file-word"></i> Import Word
              </Link>


              <div class="mt-3 table-responsive">
                <table class="table mb-0 rounded table-bordered table-nowrap">
                  <thead class="thead-dark">
                    <tr>
                      <th class="border-0" style="width:1%">#</th>
                      <th class="border-0">Soal</th>
                      <th class="border-0" style="width:15%">Aksi</th>
                    </tr>
                  </thead>
                  <tbody>
                    <template v-for="(group, groupKey) in groupedQuestions" :key="groupKey">
                      <tr>
                        <td colspan="3" class="text-left bg-light fw-bold">{{ group.title }}</td>
                      </tr>
                      <tr v-for="(question, index) in group.questions" :key="groupKey + '-' + index">
                        <td class="text-center fw-bold">
                          {{ index + 1 }}
                        </td>
                        <td>
                          <div class="fw-bold" v-html="question.question"></div>
                          <template v-if="groupKey !== 'essay'">
                            <hr />
                            <ol type="A">
                              <li :class="{ 'text-success fw-bold': isCorrectAnswer(question, '1') }">
                                <div v-html="question.option_1"></div>
                              </li>
                              <li :class="{ 'text-success fw-bold': isCorrectAnswer(question, '2') }">
                                <div v-html="question.option_2"></div>
                              </li>
                              <li :class="{ 'text-success fw-bold': isCorrectAnswer(question, '3') }">
                                <div v-html="question.option_3"></div>
                              </li>
                              <li :class="{ 'text-success fw-bold': isCorrectAnswer(question, '4') }">
                                <div v-html="question.option_4"></div>
                              </li>
                            </ol>
                          </template>
                        </td>
                        <td class="text-center">
                          <Link :href="`/teacher/exams/${exam.id}/questions/${question.id}/edit`" class="border-0 shadow btn btn-sm btn-info me-1">
                            <i class="fa fa-pencil-alt"></i>
                          </Link>
                          <button @click.prevent="destroy(exam.id, question.id)" class="border-0 btn btn-sm btn-danger">
                            <i class="fa fa-trash"></i>
                          </button>
                        </td>
                      </tr>
                    </template>

                    <!-- Static Essay Section -->
                    <template v-if="essayQuestions.length > 0">
                      <tr>
                        <td colspan="3" class="text-left bg-light fw-bold">Essay</td>
                      </tr>
                      <tr v-for="(question, index) in essayQuestions" :key="'essay-' + index">
                        <td class="text-center fw-bold">
                          {{ index + 1 }}
                        </td>
                        <td>
                          <div class="fw-bold" v-html="question.question"></div>
                        </td>
                        <td class="text-center">
                          <Link :href="`/teacher/exams/${exam.id}/questions/${question.id}/edit`" class="border-0 shadow btn btn-sm btn-info me-2">
                            <i class="fa fa-pencil-alt"></i>
                          </Link>
                          <button @click.prevent="destroy(exam.id, question.id)" class="border-0 btn btn-sm btn-danger">
                            <i class="fa fa-trash"></i>
                          </button>
                        </td>
                      </tr>
                    </template>
                  </tbody>
                </table>
              </div>
              <Pagination :links="exam.questions.links" align="end" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>

  <script>
  import LayoutTeacher from '../../../Layouts/Teacher.vue';
  import Pagination from '../../../Components/Pagination.vue';
  import { Head, Link, router } from '@inertiajs/vue3';
  import Swal from 'sweetalert2';

  export default {
    layout: LayoutTeacher,
    components: {
      Head,
      Link,
      Pagination,
    },
    props: {
      errors: Object,
      exam: Object,
    },
    computed: {
      groupedQuestions() {
        const groups = {
          single: { title: 'PG Tunggal', questions: [] },
          multiple: { title: 'PG Kompleks', questions: [] },
        };

        this.exam.questions.data.forEach((question) => {
          if (groups[question.answer_type]) {
            groups[question.answer_type].questions.push(question);
          }
        });

        return Object.values(groups).filter(group => group.questions.length > 0);
      },
      essayQuestions() {
        return this.exam.questions.data.filter((question) => question.answer_type === 'essay');
      },
    },
    methods: {
      isCorrectAnswer(question, option) {
        if (question.answer_type === 'single') {
          return question.answer === option;
        } else if (question.answer_type === 'multiple') {
          return question.complex_answers && question.complex_answers.includes(option);
        }
        return false;
      },
      destroy(examId, questionId) {
        Swal.fire({
          title: 'Apakah Anda yakin?',
          text: 'Soal yang dihapus tidak dapat dikembalikan!',
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Ya, hapus!',
          cancelButtonText: 'Batal',
        }).then((result) => {
          if (result.isConfirmed) {
            router.delete(`/teacher/exams/${examId}/questions/${questionId}/destroy`, {
              onSuccess: () => {
                Swal.fire('Berhasil!', 'Soal berhasil dihapus.', 'success');
              },
            });
          }
        });
      },
    },
    setup() {
     // Fungsi untuk memformat grading_components
     const parseGradingComponents = (gradingComponents) => {
      if (!gradingComponents) return [];
      try {
        const components = JSON.parse(gradingComponents);
        return components.map((component) => ({
          type: component.type === "single" ? "PG" : component.type === "multiple" ? "PG Kompleks" : "Essay",
          weight: component.weight,
        }));
      } catch (error) {
        return [];
      }
    };

    return {
      parseGradingComponents,
    };
}
  };
  </script>

<style>
.table-centered {
  text-align: center;
  vertical-align: middle;
}
.text-success {
  color: green !important;
  font-weight: bold;
}
.bg-light {
  background-color: #f8f9fa;
}
.table-centered {
    text-align: center;
    vertical-align: middle;
  }

  .table-striped tr:nth-child(even) {
    background-color: #f8f9fa;
  }

  .btn {
    font-size: 0.875rem;
  }
</style>
