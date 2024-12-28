<template>
    <Head>
        <title>Ujian Dengan Nomor Soal : {{ page }} - Aplikasi Ujian Online</title>
    </Head>
    <div class="mb-5 row">
        <div class="col-md-7">
            <div class="border-0 shadow card">
                <div class="card-header">
                    <div class="d-flex justify-content-between">
                        <div>
                            <h5 class="mb-0">Soal No. <strong class="fw-bold">{{ page }}</strong></h5>
                        </div>
                        <div>
                            <VueCountdown :time="duration" @progress="handleCountdown" @end="endExamAuto" v-slot="{ hours, minutes, seconds }">
                                <span class="p-2 badge bg-info">
                                    <i class="fa fa-clock"></i> {{ hours }} jam, {{ minutes }} menit, {{ seconds }} detik.
                                </span>
                            </VueCountdown>
                        </div>
                    </div>
                </div>
                <div class="card-body">
                    <div v-if="question_active !== null">
                        <div>
                            <p v-html="question_active.question.question"></p>
                        </div>
                        <!-- Handling PG (Single Answer) -->
                        <div v-if="question_active.question.answer_type === 'single'">
                            <table>
                                <tbody>
                                    <tr v-for="(answer, index) in answer_order" :key="index">
                                        <td width="50" style="padding: 10px;">
                                            <button
                                                v-if="answer == question_active.answer"
                                                class="shadow btn btn-info btn-sm w-100">
                                                {{ options[index] }}
                                            </button>
                                            <button
                                                v-else
                                                @click.prevent="submitSingleAnswer(question_active.question.exam.id, question_active.question.id, answer)"
                                                class="shadow btn btn-outline-info btn-sm w-100">
                                                {{ options[index] }}
                                            </button>
                                        </td>
                                        <td style="padding: 10px;">
                                            <p v-html="question_active.question['option_'+answer]"></p>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                        <!-- Handling PG Kompleks (Multiple Answers) -->
                        <div v-else-if="question_active.question.answer_type === 'multiple'">
                            <div>
                                <p>Pilih jawaban yang benar:</p>
                                <div v-for="(answer, index) in answer_order" :key="index" style="margin-bottom: 10px; display: flex; align-items: center;">
                                    <label class="d-flex align-items-center">
                                        <input
                                            class="form-check-input me-2"
                                            type="checkbox"
                                            :value="answer"
                                            v-model="selectedAnswers"
                                            @change="submitComplexAnswer"
                                            style="width: 30px; height: 30px; margin-right: 10px;"
                                        />
                                        <p v-html="question_active.question['option_'+answer]" style="margin: 0;"></p>
                                    </label>
                                </div>
                            </div>
                        </div>
                        <!-- Handling Essay -->
                        <div v-else-if="question_active.question.answer_type === 'essay'">
                            <div>
                                <p>Jawaban:</p>
                                <textarea
                                    v-model="essayAnswer"
                                    @input="submitEssayAnswer"
                                    class="form-control"
                                    rows="5"
                                ></textarea>
                            </div>
                        </div>
                    </div>
                    <div v-else>
                        <div class="border-0 shadow alert alert-danger">
                            <i class="fa fa-exclamation-triangle"></i> Soal Tidak Ditemukan!.
                        </div>
                    </div>
                </div>
                <div class="card-footer">
                    <div class="d-flex justify-content-between">
                        <div class="text-start">
                            <button v-if="page > 1" @click.prevent="navigateTo(page - 1)" type="button" class="mb-2 btn btn-gray-400 btn-sm btn-block">Sebelumnya</button>
                        </div>
                        <div class="text-end">
                            <button v-if="page < all_questions.length" @click.prevent="navigateTo(page + 1)" type="button" class="btn btn-gray-400 btn-sm">Selanjutnya</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="col-md-5">
            <div class="border-0 shadow card">
                <div class="text-center card-header">
                    <div class="p-2 badge bg-success"> {{ questionAnsweredCount }} dikerjakan</div>
                </div>
                <div class="card-body" style="height: 330px; overflow-y: auto;">
                    <div v-for="(question, index) in all_questions" :key="index">
                        <div style="width: 20%; float: left;">
                            <div style="padding: 5px;">
                                <button @click.prevent="navigateTo(index + 1)" :class="[
                                    'btn btn-sm w-100',
                                    index + 1 === page ? 'btn-gray-400' : question.answer === 0 ? 'btn-outline-info' : 'btn-info'
                                ]">{{ index + 1 }}</button>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="card-footer">
                    <button @click="showModalEndExam = true" class="border-0 shadow btn btn-danger btn-md w-100">Akhiri Ujian</button>
                </div>
            </div>
        </div>
    </div>
    <!-- modal akhiri ujian -->
    <div v-if="showModalEndExam" class="modal fade" :class="{ 'show': showModalEndExam }" tabindex="-1" aria-hidden="true" style="display:block;" role="dialog">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Akhiri Ujian ?</h5>
                </div>
                <div class="modal-body">
                    Setelah mengakhiri ujian, Anda tidak dapat kembali ke ujian ini lagi. Yakin akan mengakhiri ujian?
                </div>
                <div class="modal-footer">
                    <button @click.prevent="endExam" type="button" class="btn btn-primary">Ya, Akhiri</button>
                    <button @click.prevent="showModalEndExam = false" type="button" class="btn btn-secondary">Tutup</button>
                </div>
            </div>
        </div>
    </div>

    <!-- modal waktu ujian berakhir -->
    <div v-if="showModalEndTimeExam" class="modal fade" :class="{ 'show': showModalEndTimeExam }" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-hidden="true" style="display:block;" role="dialog">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Waktu Habis !</h5>
                </div>
                <div class="modal-body">
                    Waktu ujian sudah berakhir!. Klik <strong class="fw-bold">Ya</strong> untuk mengakhiri ujian.
                </div>
                <div class="modal-footer">
                    <button @click.prevent="endExam" type="button" class="btn btn-primary">Ya</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import LayoutStudent from "../../../Layouts/Student.vue";
import { Head, Link, router } from "@inertiajs/vue3";
import { ref, computed, onMounted } from "vue";
import axios from "axios";
import Swal from "sweetalert2";
import VueCountdown from "@chenfengyuan/vue-countdown";

export default {
    layout: LayoutStudent,
    components: {
        Head,
        Link,
        VueCountdown,
    },
    props: {
        id: Number,
        page: Number,
        exam_group: Object,
        all_questions: Array,
        question_answered: Number,
        question_active: Object,
        answer_order: Array,
        duration: Object,
    },
    setup(props) {
        const options = ["A", "B", "C", "D"];
        const duration = ref(props.duration.duration); // Waktu ujian dalam milidetik
        const counter = ref(0); // Counter untuk mengatur sinkronisasi ke server
        const interval = ref(null); // Interval countdown

        // State untuk jawaban PG Kompleks dan Essay
        const selectedAnswers = ref(props.question_active.answer || []); // Jawaban untuk PG Kompleks
        const essayAnswer = ref(props.question_active.answer || ""); // Jawaban untuk Essay

        // Menghitung soal yang sudah dijawab
        const questionAnsweredCount = computed(() => {
            return props.all_questions.filter((q) => q.answer && q.answer.length > 0).length;
        });

        // Fungsi untuk countdown manual
        const handleCountdown = () => {
            if (duration.value > 0) {
                duration.value -= 1000;
                counter.value++;
                if (counter.value % 5 === 0) {
                    axios.put(`/student/exam-duration/update/${props.duration.id}`, {
                        duration: duration.value,
                    });
                }
            } else {
                clearInterval(interval.value);
                endExamAuto();
            }
        };

        // Mulai countdown
        const startCountdown = () => {
            if (!interval.value) {
                interval.value = setInterval(handleCountdown, 1000);
            }
        };

        // Navigasi antar halaman soal
        const navigateTo = (pageNumber) => {
            axios.put(`/student/exam-duration/update/${props.duration.id}`, {
                duration: duration.value,
            }).then(() => {
                router.get(`/student/exam/${props.id}/${pageNumber}`);
            });
        };
        //define state modal
            const showModalEndExam      = ref(false);
            const showModalEndTimeExam  = ref(false);
        // Akhiri ujian otomatis saat waktu habis
        const endExamAuto = () => {
            router.post("/student/exam-end", {
                exam_group_id: props.exam_group.id,
                exam_id: props.exam_group.exam.id,
                exam_session_id: props.exam_group.exam_session.id,
            }).then(() => {
                Swal.fire({
                    title: "Waktu Habis!",
                    text: "Ujian telah berakhir secara otomatis.",
                    icon: "info",
                    showConfirmButton: true,
                });
            });
        };

        // Akhiri ujian manual
        const endExam = () => {
            router.post("/student/exam-end", {
                exam_group_id: props.exam_group.id,
                exam_id: props.exam_group.exam.id,
                exam_session_id: props.exam_group.exam_session.id,
            }).then(() => {
                Swal.fire({
                    title: "Selesai!",
                    text: "Ujian berhasil diakhiri.",
                    icon: "success",
                    showConfirmButton: false,
                    timer: 4000,
                });
            });
        };

        // Kirim jawaban untuk PG Single
        const submitSingleAnswer = (exam_id, question_id, answer) => {
            router.post("/student/exam-answer", {
                exam_id,
                exam_session_id: props.exam_group.exam_session.id,
                question_id,
                answer,
                duration: duration.value,
            }).then(() => {
                props.all_questions[props.page - 1].answer = answer; // Update langsung di halaman soal aktif
            });
        };

        // Kirim jawaban untuk PG Kompleks
        const submitComplexAnswer = () => {
            router.post("/student/exam-answer", {
                exam_id: props.exam_group.exam.id,
                exam_session_id: props.exam_group.exam_session.id,
                question_id: props.question_active.question.id,
                answer: selectedAnswers.value,
                duration: duration.value,
            });
        };

        // Kirim jawaban untuk Essay
        const submitEssayAnswer = () => {
            router.post("/student/exam-answer", {
                exam_id: props.exam_group.exam.id,
                exam_session_id: props.exam_group.exam_session.id,
                question_id: props.question_active.question.id,
                answer: essayAnswer.value,
                duration: duration.value,
            });
        };

        // Periksa apakah ujian sudah diakhiri
        const checkExamStatus = () => {
            axios.get(`/student/exam-status/${props.id}`).then((response) => {
                if (response.data.status === "ended") {
                    Swal.fire({
                        title: "Ujian Selesai",
                        text: "Ujian ini sudah diakhiri. Anda tidak dapat melihat soal lagi.",
                        icon: "info",
                        confirmButtonText: "Lihat Hasil",
                    }).then(() => {
                        router.get(`/student/exams/result/${props.id}`);
                    });
                }
            });
        };

        // Mulai countdown saat komponen dimuat
        onMounted(() => {
            startCountdown();
            checkExamStatus();
        });

        return {
            options,
            duration,
            selectedAnswers,
            essayAnswer,
            questionAnsweredCount,
            navigateTo,
            endExam,
            submitSingleAnswer,
            submitComplexAnswer,
            submitEssayAnswer,
            showModalEndExam,
            showModalEndTimeExam,
        };
    },
};
</script>



<style>
.card {
  border-radius: 10px;
  overflow: hidden;
}
.badge {
  font-size: 1rem;
}
</style>
