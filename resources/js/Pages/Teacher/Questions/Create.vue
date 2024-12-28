<template>
    <Head>
      <title>Tambah Soal Ujian - Aplikasi Ujian Online</title>
    </Head>
    <div class="container-fluid">
      <div class="row">
        <div class="col-md-12">
          <Link :href="`/teacher/exams/${exam.id}`" class="mb-3 border-0 shadow btn btn-md btn-primary">
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>

          <div class="card">
            <div class="card-body">
              <h5>Tambah Soal Ujian</h5>
              <form @submit.prevent="submit">

                <!-- Jenis Soal -->
                <div class="mb-3">
                  <label>Jenis Soal</label>
                  <select v-model="form.answer_type" class="form-control">
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
import LayoutTeacher from "../../../Layouts/Teacher.vue";
import Quill from "quill";
import "quill/dist/quill.snow.css";
import { ref, reactive, onMounted } from "vue";
import { Head, Link, router } from "@inertiajs/vue3";
import Swal from "sweetalert2";


export default {
  layout: LayoutTeacher,
  components: {
    Head,
    Link,
  },
  props: {
    exam: Object,
  },
  setup(props) {
    const form = reactive({
      question: "",
      option_1: "",
      option_2: "",
      option_3: "",
      option_4: "",
      answer_type: "single", // Default ke pilihan tunggal
      answer: "",
      complex_answers: [], // Untuk pilihan ganda
    });

    const questionEditor = ref(null);
    const option1Editor = ref(null);
    const option2Editor = ref(null);
    const option3Editor = ref(null);
    const option4Editor = ref(null);

    const uploadImageToServer = async (file) => {
            const formData = new FormData();
            formData.append("file", file);

            try {
                const response = await fetch("/teacher/upload-image", {
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

        const handleDragAndDrop = (editor) => {
            editor.root.addEventListener("drop", async (event) => {
                event.preventDefault();

                const file = event.dataTransfer.files[0];
                if (file && file.type.startsWith("image/")) {
                    const imageUrl = await uploadImageToServer(file);
                    if (imageUrl) {
                        const range = editor.getSelection() || { index: editor.getLength() };
                        editor.clipboard.dangerouslyPasteHTML(
                        range.index,
                        `<br /><img src="${imageUrl}">`
                    );
                    }
                }
            });
        };

        const handlePaste = (editor) => {
            editor.root.addEventListener("paste", async (event) => {
                const clipboardData = event.clipboardData || window.clipboardData;
                const items = clipboardData.items;

                for (const item of items) {
                    if (item.type.startsWith("image/")) {
                        event.preventDefault();

                        const file = item.getAsFile();
                        const imageUrl = await uploadImageToServer(file);
                        if (imageUrl) {
                            const range = editor.getSelection() || { index: editor.getLength() };
                            editor.clipboard.dangerouslyPasteHTML(
                        range.index,
                        `<br /><img src="${imageUrl}">`
                    );
                        }
                    }
                }
            });
        };
        const ImageFormat = Quill.import('formats/image');
ImageFormat.whitelist = ['style']; // Mengizinkan atribut 'style'
Quill.register(ImageFormat, true);


        const initQuillEditor = (ref, key) => {
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
            handleDragAndDrop(editor);
            handlePaste(editor);

            editor.on("text-change", () => {
                form[key] = editor.root.innerHTML;
            });

            return editor;
        };
    onMounted(() => {
      initQuillEditor(questionEditor, "question");
      initQuillEditor(option1Editor, "option_1");
      initQuillEditor(option2Editor, "option_2");
      initQuillEditor(option3Editor, "option_3");
      initQuillEditor(option4Editor, "option_4");
    });

    const submit = () => {
    if (form.answer_type === "single" && !form.answer) {
        Swal.fire("Error", "Pilih jawaban yang benar!", "error");
        return;
    }

    if (form.answer_type === "multiple" && form.complex_answers.length === 0) {
        Swal.fire("Error", "Pilih setidaknya satu jawaban yang benar!", "error");
        return;
    }

    router.post(`/teacher/exams/${props.exam.id}/questions/store`, form, {
        onSuccess: () => {
            Swal.fire("Success!", "Soal berhasil disimpan!", "success");
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
