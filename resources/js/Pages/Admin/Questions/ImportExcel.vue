<template>
    <Head>
        <title>Import Soal Ujian - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
        <div class="row">
            <div class="col-md-12">
                <Link :href="`/admin/exams/${exam.id}`" class="mb-3 border-0 shadow btn btn-md btn-primary me-3">
                    <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
                </Link>
                <a href="/assets/excel/contoh.zip" target="_blank" class="mb-3 text-white border-0 shadow btn btn-md btn-success">
                    <i class="fa fa-file-archive me-2"></i> Contoh File ZIP
                </a>
                <div class="border-0 shadow card">
                    <div class="card-body">
                        <h5><i class="fa fa-question-circle"></i> Import Soal dalam File ZIP</h5>
                        <hr>
                        <form @submit.prevent="submit">

                            <div class="mb-4">
                                <label>File ZIP</label>
                                <input type="file" class="form-control" @change="handleFileUpload" accept=".xls,.xlsx,.csv,.zip">
                                <div v-if="errors.file" class="mt-2 alert alert-danger">
                                    {{ errors.file }}
                                </div>
                            </div>

                            <button type="submit" class="border-0 shadow btn btn-md btn-primary me-2">Upload</button>
                            <button type="reset" class="border-0 shadow btn btn-md btn-warning" @click="resetForm">Reset</button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import LayoutAdmin from '../../../Layouts/Admin.vue';
import { Head, Link, router } from '@inertiajs/vue3';
import Swal from 'sweetalert2';
import { reactive } from 'vue';

export default {
    layout: LayoutAdmin,
    components: {
        Head,
        Link,
    },
    props: {
        errors: Object,
        exam: Object,
    },
    setup(props) {
        const form = reactive({
            file: null,
        });

        const handleFileUpload = (event) => {
            form.file = event.target.files[0];
        };

        const resetForm = () => {
            form.file = null;
        };

        const submit = () => {
            if (!form.file) {
                Swal.fire({
                    title: 'Error',
                    text: 'Pilih file terlebih dahulu!',
                    icon: 'error',
                });
                return;
            }

            const formData = new FormData();
            formData.append('file', form.file);

            router.post(`/admin/exams/${props.exam.id}/questions/import-excel`, formData, {
                headers: {
                    'Content-Type': 'multipart/form-data',
                },
                onSuccess: () => {
                    Swal.fire({
                        title: 'Success!',
                        text: 'Import Soal Ujian Berhasil!',
                        icon: 'success',
                        showConfirmButton: false,
                        timer: 2000,
                    });
                },
                onError: () => {
                    Swal.fire({
                        title: 'Error',
                        text: 'Gagal mengimpor soal. Periksa kembali format file.',
                        icon: 'error',
                    });
                },
            });
        };

        return {
            form,
            handleFileUpload,
            resetForm,
            submit,
        };
    },
};
</script>

<style>
/* Tambahkan styling jika diperlukan */
</style>
