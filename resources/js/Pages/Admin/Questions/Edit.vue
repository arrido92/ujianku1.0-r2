<template>
    <Head>
      <title>Edit Soal Ujian - Aplikasi Ujian Online</title>
    </Head>
    <div class="container-fluid">
      <div class="row">
        <div class="col-md-12">
          <Link :href="`/admin/exams/${exam.id}`" class="mb-3 border-0 shadow btn btn-md btn-primary" @click.prevent="goBack">
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>

          <div class="card">
            <div class="card-body">
              <h5>Edit Soal Ujian</h5>
              <form @submit.prevent="submit">

                <!-- Jenis Soal -->
                <div class="mb-3">
                  <label>Jenis Soal</label>
                  <select v-model="form.answer_type" class="form-control" disabled>
                    <option value="single">PG</option>
                    <option value="multiple">PG Kompleks</option>
                    <option value="essay">Essay</option>
                  </select>
                </div>

                <!-- Soal -->
                <div class="mb-3">
                  <label>Soal</label>
                  <div ref="questionEditor" class="quill-editor"></div>
                </div>

                <!-- Pilihan Jawaban untuk PG -->
                <template v-if="form.answer_type !== 'essay'">
                  <div class="mb-3">
                    <label>Pilihan A</label>
                    <div ref="option1Editor" class="quill-editor"></div>
                  </div>

                  <div class="mb-3">
                    <label>Pilihan B</label>
                    <div ref="option2Editor" class="quill-editor"></div>
                  </div>

                  <div class="mb-3">
                    <label>Pilihan C</label>
                    <div ref="option3Editor" class="quill-editor"></div>
                  </div>

                  <div class="mb-3">
                    <label>Pilihan D</label>
                    <div ref="option4Editor" class="quill-editor"></div>
                  </div>
                </template>

                <!-- Jawaban Benar Tunggal -->
                <div v-if="form.answer_type === 'single'" class="mb-3">
                  <label>Jawaban Benar</label>
                  <select v-model="form.answer" class="form-control">
                    <option value="1">A</option>
                    <option value="2">B</option>
                    <option value="3">C</option>
                    <option value="4">D</option>
                  </select>
                </div>

                <!-- Jawaban Benar Ganda -->
                <!--<div v-if="form.answer_type === 'multiple'" class="mb-3">
                  <label>Jawaban Benar (Pilih lebih dari satu)</label>
                  <div>
                    <div v-for="(option, index) in options" :key="index">
                      <input
                        class="form-check-input"
                        type="checkbox"
                        :value="option.key"
                        v-model="form.complex_answers"
                      />
                      {{ option.label }}
                    </div>
                  </div>
                </div>-->
                <!-- Jawaban Benar Ganda -->
                <div v-if="form.answer_type === 'multiple'" class="mb-3">
                  <label>Jawaban Benar (Pilih lebih dari satu)</label>
                  <div>
                    <input class="form-check-input" type="checkbox" v-model="form.complex_answers" value="1"> A
                    <input class="form-check-input" type="checkbox" v-model="form.complex_answers" value="2"> B
                    <input class="form-check-input" type="checkbox" v-model="form.complex_answers" value="3"> C
                    <input class="form-check-input" type="checkbox" v-model="form.complex_answers" value="4"> D
                  </div>
                </div>

                <button type="submit" class="btn btn-primary">Simpan</button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
<script>
import LayoutAdmin from "../../../Layouts/Admin.vue";
import Quill from "quill";
import "quill/dist/quill.snow.css";
import { ref, reactive, onMounted } from "vue";
import { Head, Link, router } from "@inertiajs/vue3";
import Swal from "sweetalert2";


export default {
  layout: LayoutAdmin,
  components: {
    Head,
    Link,
  },
  props: {
    exam: Object,
    question: Object,
  },
  setup(props) {
    const form = reactive({
      question: props.question.question || "",
      option_1: props.question.option_1 || "",
      option_2: props.question.option_2 || "",
      option_3: props.question.option_3 || "",
      option_4: props.question.option_4 || "",
      answer_type: props.question.answer_type || "single", // Default ke pilihan tunggal
      answer: props.question.answer || "",
      complex_answers: Array.isArray(props.question.complex_answers)
        ? props.question.complex_answers.map(String) // Pastikan checkbox cocok dengan tipe string
        : [],
    });

    const options = [
      { label: "A", key: "1" },
      { label: "B", key: "2" },
      { label: "C", key: "3" },
      { label: "D", key: "4" },
    ];

    const questionEditor = ref(null);
    const option1Editor = ref(null);
    const option2Editor = ref(null);
    const option3Editor = ref(null);
    const option4Editor = ref(null);

    const uploadImageToServer = async (file) => {
            const formData = new FormData();
            formData.append("file", file);

            try {
                const response = await fetch("/admin/upload-image", {
                    method: "POST",
                    body: formData,
                    headers: {
                        "X-CSRF-TOKEN": document.querySelector('meta[name="csrf-token"]').getAttribute("content"),
                    },
                });

                const result = await response.json();
                if (result.url) {
                    return result.url;

                } else {
                    Swal.fire("Error", result.error || "Failed to upload image", "error");
                    return null;
                }
            } catch (error) {
                console.error("Image upload failed", error);
                Swal.fire("Error", "Failed to upload image", "error");
                return null;
            }
        };

        const handleImageUpload = (editor) => {
            const toolbar = editor.getModule("toolbar");
            toolbar.addHandler("image", async () => {
                const input = document.createElement("input");
                input.setAttribute("type", "file");
                input.setAttribute("accept", "image/*");
                input.click();

                input.onchange = async () => {
                    const file = input.files[0];
                    if (file) {
                        const imageUrl = await uploadImageToServer(file);
                        if (imageUrl) {
                            const range = editor.getSelection();
                            editor.clipboard.dangerouslyPasteHTML(
                        range.index,
                        `<img src="${imageUrl}">`
                    );
}

                    }
                };
            });
        };

        const initQuillEditor = (ref, key, content) => {
            const editor = new Quill(ref.value, {
                theme: "snow",
                modules: {
                    toolbar: [
                        ["bold", "italic", "underline"],
                        [{ list: "ordered" }, { list: "bullet" }],
                        ["link", "image", "video"],
                    ],

                },
            });

            handleImageUpload(editor);

            editor.root.innerHTML = content;

            editor.on("text-change", () => {
                form[key] = editor.root.innerHTML;
            });

            return editor;
        };
    onMounted(() => {
      initQuillEditor(questionEditor, "question", form.question);
      initQuillEditor(option1Editor, "option_1", form.option_1);
      initQuillEditor(option2Editor, "option_2", form.option_2);
      initQuillEditor(option3Editor, "option_3", form.option_3);
      initQuillEditor(option4Editor, "option_4", form.option_4);
    });

    const goBack = () => {
      router.visit(`/admin/exams/${props.exam.id}`, { preserveState: false });
    };

    const submit = () => {
    if (form.answer_type === "single" && !form.answer) {
        Swal.fire("Error", "Pilih jawaban yang benar!", "error");
        return;
    }

    if (form.answer_type === "multiple" && form.complex_answers.length === 0) {
        Swal.fire("Error", "Pilih setidaknya satu jawaban yang benar!", "error");
        return;
    }

    router.put(`/admin/exams/${props.exam.id}/questions/${props.question.id}/update`, form, {
        preserveState: false, // Pastikan state halaman tidak dipertahankan
        onSuccess: () => {
            Swal.fire("Success!", "Soal berhasil diperbarui!", "success");
            location.reload(); // Paksa reload halaman
        },
    });
};

    return {
      form,
      questionEditor,
      option1Editor,
      option2Editor,
      option3Editor,
      option4Editor,
      options,
      goBack,
      submit,
    };
  },
};
</script>

<style>
.quill-editor {
  height: 300px;
  margin-bottom: 15px;
}
.quill-editor img {
    max-width: 200px;
    max-height: 200px;
    width: auto; /* Opsional untuk gambar berukuran besar */
    height: auto; /* Opsional untuk gambar berukuran besar */
}
.question-checkbox {
    display: flex;
    align-items: center;
  }
</style>
