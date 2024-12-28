<template>
    <Head>
      <title>Edit Ujian - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
      <div class="row">
        <div class="col-md-12">
          <Link href="/admin/exams" class="mb-3 border-0 shadow btn btn-md btn-primary" type="button">
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>
          <div class="border-0 shadow card">
            <div class="card-body">
              <h5><i class="fa fa-edit"></i> Edit Ujian</h5>
              <hr />
              <form @submit.prevent="submit">
                <!-- Nama Ujian -->
                <div class="mb-4">
                  <label>Nama Ujian</label>
                  <input
                    type="text"
                    class="form-control"
                    placeholder="Masukkan Nama Ujian"
                    v-model="form.title"
                  />
                  <div v-if="errors.title" class="mt-2 alert alert-danger">
                    {{ errors.title }}
                  </div>
                </div>

                <!-- Mata Pelajaran -->
                <div class="mb-4">
                  <label>Mata Pelajaran</label>
                  <select class="form-select" v-model="form.lesson_id">
                    <option v-for="(lesson, index) in lessons" :key="index" :value="lesson.id">
                      {{ lesson.title }}
                    </option>
                  </select>
                  <div v-if="errors.lesson_id" class="mt-2 alert alert-danger">
                    {{ errors.lesson_id }}
                  </div>
                </div>

                <!-- Kelas -->
                <div class="mb-4">
                  <label>Kelas (Bisa memilih lebih dari satu)</label>
                  <div class="row">
                    <div
                      v-for="(classroom, index) in classrooms"
                      :key="index"
                      class="mb-2 col-md-6"
                    >
                      <div class="form-check">
                        <input
                          type="checkbox"
                          class="form-check-input"
                          :value="classroom.id"
                          v-model="form.classroom_ids"
                        />
                        <label class="form-check-label">{{ classroom.title }}</label>
                      </div>
                    </div>
                  </div>
                  <div v-if="errors.classroom_ids" class="mt-2 alert alert-danger">
                    {{ errors.classroom_ids }}
                  </div>
                </div>

                <!-- Deskripsi -->
                <div class="mb-4">
                  <label>Deskripsi</label>
                  <div class="quill-editor"></div>
                  <div v-if="errors.description" class="mt-2 alert alert-danger">
                    {{ errors.description }}
                  </div>
                </div>

                <!-- Pengaturan Ujian -->
                <div class="row">
                  <div class="col-md-4">
                    <div class="mb-4">
                      <label>Acak Soal</label>
                      <select class="form-select" v-model="form.random_question">
                        <option value="Y">Ya</option>
                        <option value="N">Tidak</option>
                      </select>
                      <div v-if="errors.random_question" class="mt-2 alert alert-danger">
                        {{ errors.random_question }}
                      </div>
                    </div>
                  </div>
                  <div class="col-md-4">
                    <div class="mb-4">
                      <label>Acak Jawaban</label>
                      <select class="form-select" v-model="form.random_answer">
                        <option value="Y">Ya</option>
                        <option value="N">Tidak</option>
                      </select>
                      <div v-if="errors.random_answer" class="mt-2 alert alert-danger">
                        {{ errors.random_answer }}
                      </div>
                    </div>
                  </div>
                  <div class="col-md-4">
                    <div class="mb-4">
                      <label>Tampilkan Hasil</label>
                      <select class="form-select" v-model="form.show_answer">
                        <option value="Y">Ya</option>
                        <option value="N">Tidak</option>
                      </select>
                      <div v-if="errors.show_answer" class="mt-2 alert alert-danger">
                        {{ errors.show_answer }}
                      </div>
                    </div>
                  </div>
                </div>

                <!-- Durasi -->
                <div class="mb-4">
                  <label>Durasi (Menit)</label>
                  <input
                    type="number"
                    min="1"
                    class="form-control"
                    placeholder="Masukkan Durasi Ujian (Menit)"
                    v-model="form.duration"
                  />
                  <div v-if="errors.duration" class="mt-2 alert alert-danger">
                    {{ errors.duration }}
                  </div>
                </div>
                <!-- Validasi Grading -->
                <div v-if="totalWeight !== 100" class="mt-3 alert alert-warning">
                    Total bobot harus 100%. Saat ini: {{ totalWeight }}%
                </div>
                <!-- Grading Components -->
                <div class="mb-4">
                <label>Komponen Nilai</label>
                <div v-for="(component, index) in form.grading_components" :key="index" class="mb-3">
                    <div class="row">
                    <div class="col-md-6">
                        <label>Tipe Soal</label>
                        <select class="form-select" v-model="component.type">
                        <option value="single">PG</option>
                        <option value="multiple">PG Kompleks</option>
                        <option value="essay">Essay</option>
                        </select>
                    </div>
                    <div class="col-md-4">
                        <label>Bobot (%)</label>
                        <input
                        type="number"
                        v-model="component.weight"
                        class="form-control"
                        placeholder="Bobot (%)"
                        min="0"
                        max="100"
                        />
                    </div>
                    <div class="col-md-2 d-flex align-items-end">
                        <button type="button" @click="removeComponent(index)" class="btn btn-danger">
                        Hapus
                        </button>
                    </div>
                    </div>
                </div>
                <button type="button" @click="addComponent" class="mt-2 btn btn-primary"><i class="fa fa-plus-circle"></i> Komponen Nilai</button>
                </div>
                <!-- Tombol Submit -->
                <button
                  type="submit"
                  class="border-0 shadow btn btn-md btn-primary me-2"
                >
                  Update
                </button>
                <button
                  type="reset"
                  @click="resetForm"
                  class="border-0 shadow btn btn-md btn-warning"
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
import { reactive, ref, onMounted, computed } from "vue";
import Swal from "sweetalert2";
import Quill from "quill";

export default {
  layout: LayoutAdmin,

  components: {
    Head,
    Link,
  },

  props: {
    errors: Object,
    exam: Object,
    lessons: Array,
    classrooms: Array,
  },

  setup(props) {
    const quillEditor = ref(null);

    const form = reactive({
      title: props.exam.title,
      lesson_id: props.exam.lesson_id,
      classroom_ids: props.exam.classrooms.map((classroom) => classroom.id),
      duration: props.exam.duration,
      description: props.exam.description,
      random_question: props.exam.random_question,
      random_answer: props.exam.random_answer,
      show_answer: props.exam.show_answer,
      grading_components: props.exam.grading_components
        ? JSON.parse(props.exam.grading_components)
        : [{ type: "single", weight: 100 }], // Default jika grading_components tidak ada
    });

    // Hitung total bobot grading_components
    const totalWeight = computed(() =>
      form.grading_components.reduce((sum, component) => sum + component.weight, 0)
    );

    const resetForm = () => {
      form.title = props.exam.title;
      form.lesson_id = props.exam.lesson_id;
      form.classroom_ids = props.exam.classrooms.map((classroom) => classroom.id);
      form.duration = props.exam.duration;
      form.description = props.exam.description;
      form.random_question = props.exam.random_question;
      form.random_answer = props.exam.random_answer;
      form.show_answer = props.exam.show_answer;
      form.grading_components = props.exam.grading_components
        ? JSON.parse(props.exam.grading_components)
        : [{ type: "single", weight: 100 }];

      if (quillEditor.value) {
        quillEditor.value.root.innerHTML = props.exam.description;
      }
    };

    const addComponent = () => {
      form.grading_components.push({ type: "single", weight: 0 });
    };

    const removeComponent = (index) => {
      form.grading_components.splice(index, 1);
    };

    const submit = () => {
      if (quillEditor.value) {
        form.description = quillEditor.value.root.innerHTML;
      }

      if (totalWeight.value !== 100) {
        Swal.fire({
          title: "Gagal!",
          text: "Total bobot nilai harus 100%.",
          icon: "error",
        });
        return;
      }

      router.put(
        `/admin/exams/${props.exam.id}`,
        { ...form },
        {
          onSuccess: () => {
            Swal.fire({
              title: "Success!",
              text: "Ujian Berhasil Diupdate.",
              icon: "success",
              timer: 2000,
              showConfirmButton: false,
            });
          },
        }
      );
    };

    onMounted(() => {
      quillEditor.value = new Quill(".quill-editor", {
        theme: "snow",
        placeholder: "Edit Deskripsi Ujian di sini...",
        modules: {
          toolbar: [
            [{ header: [1, 2, 3, false] }],
            ["bold", "italic", "underline"],
            [{ list: "ordered" }, { list: "bullet" }],
            ["link", "image"],
          ],
        },
      });

      quillEditor.value.root.innerHTML = form.description;
    });

    return {
      form,
      quillEditor,
      totalWeight,
      resetForm,
      addComponent,
      removeComponent,
      submit,
    };
  },
};
</script>


  <style scoped>
  .form-check-label {
    font-size: 0.9rem;
  }

  .quill-editor {
    height: 200px;
    overflow-y: auto;
    border: 1px solid #ddd;
    border-radius: 4px;
  }
  </style>
