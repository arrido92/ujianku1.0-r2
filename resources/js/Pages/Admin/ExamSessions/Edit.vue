<template>
    <Head>
        <title>Edit Waktu Sesi Ujian - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
        <div class="row">
            <div class="col-md-12">
                <Link href="/admin/exam_sessions" class="mb-3 border-0 shadow btn btn-md btn-primary" type="button">
                    <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
                </Link>
                <div class="border-0 shadow card">
                    <div class="card-body">
                        <h5><i class="fas fa-stopwatch"></i> Edit Waktu Sesi</h5>
                        <hr>
                        <form @submit.prevent="submit">
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
                                    Update
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
import { reactive, ref } from "vue";
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
        errors: Object,
        exam_session: Object, // Data sesi ujian dari backend
    },

    setup(props) {
        // Initialize form dengan data yang diterima dari backend
        const form = reactive({
            start_time: new Date(props.exam_session.start_time),
            end_time: new Date(props.exam_session.end_time),
        });

        const isSubmitting = ref(false);

        // Fungsi untuk submit data
        const submit = () => {
            isSubmitting.value = true;

            router.put(
                `/admin/exam_sessions/${props.exam_session.id}`,
                {
                    start_time: form.start_time,
                    end_time: form.end_time,
                },
                {
                    onSuccess: () => {
                        Swal.fire({
                            title: "Berhasil!",
                            text: "Waktu sesi ujian berhasil diperbarui!",
                            icon: "success",
                            timer: 2000,
                            showConfirmButton: false,
                        });
                    },
                    onFinish: () => {
                        isSubmitting.value = false;
                    },
                }
            );
        };

        // Fungsi untuk mereset form
        const resetForm = () => {
            form.start_time = new Date(props.exam_session.start_time);
            form.end_time = new Date(props.exam_session.end_time);
        };

        return {
            form,
            isSubmitting,
            submit,
            resetForm,
        };
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
