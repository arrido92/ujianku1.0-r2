<template>
    <Head>
        <title>Daftarkan Siswa - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
        <div class="row">
            <div class="col-md-12">
                <Link :href="`/admin/exam_sessions/${exam_session.id}`" class="mb-3 border-0 shadow btn btn-md btn-primary" type="button">
                    <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
                </Link>
                <div class="border-0 shadow card">
                    <div class="card-body">
                        <h5><i class="fa fa-user-plus"></i> Pilih Siswa Untuk di Daftarkan</h5>
                        <hr>
                        <form @submit.prevent="submit">
                            <div class="mb-4 table-responsive">
                                <table class="table mb-0 rounded table-bordered table-centered table-nowrap">
                                    <thead class="thead-dark">
                                        <tr class="border-0">
                                            <th class="border-0 rounded-start" style="width:5%">
                                                <input type="checkbox" v-model="form.allSelected" @change="selectAll" />
                                            </th>
                                            <th class="border-0">Nama Siswa</th>
                                            <th class="border-0">Kelas</th>
                                            <th class="border-0">Jenis Kelamin</th>
                                        </tr>
                                    </thead>
                                    <div class="mt-3"></div>
                                    <tbody>
                                        <tr v-for="student of students" :key="student.id">
                                            <td>
                                                <input type="checkbox" v-model="form.student_id" :id="student.id" :value="student.id" number :checked="form.allSelected" />
                                            </td>
                                            <td>{{ student.name }}</td>
                                            <td class="text-center">{{ student.classroom.title }}</td>
                                            <td class="text-center">{{ student.gender }}</td>
                                        </tr>
                                    </tbody>
                                </table>
                                <div v-if="errors.student_id" class="mt-2 alert alert-danger">
                                    {{ errors.student_id }}
                                </div>
                            </div>
                            <button type="submit" class="border-0 shadow btn btn-md btn-primary me-2">Simpan</button>
                            <button type="reset" class="border-0 shadow btn btn-md btn-warning">Reset</button>
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
import { reactive } from 'vue';
import Swal from 'sweetalert2';

export default {
    layout: LayoutAdmin,

    components: {
        Head,
        Link,
    },

    props: {
        errors: Object,
        exam: Object,
        exam_session: Object,
        students: Array,
    },

    setup(props) {
        // Define form with reactive
        const form = reactive({
            exam_id: props.exam.id,
            student_id: [],
            allSelected: false,
        });

        // Define method to select all students
        const selectAll = () => {
            if (form.allSelected) {
                form.student_id = props.students.map((student) => student.id);
            } else {
                form.student_id = [];
            }
        };

        // Define method to submit form
        const submit = () => {
            if (form.student_id.length === 0) {
                Swal.fire({
                    title: 'Error!',
                    text: 'Silakan pilih minimal satu siswa.',
                    icon: 'error',
                    showConfirmButton: true,
                });
                return;
            }

            // Send data to server
            router.post(`/admin/exam_sessions/${props.exam_session.id}/enrolle/store`, {
                exam_id: form.exam_id,
                student_id: form.student_id,
            }, {
                onSuccess: () => {
                    Swal.fire({
                        title: 'Success!',
                        text: 'Enrolle Siswa Berhasil Disimpan!.',
                        icon: 'success',
                        showConfirmButton: false,
                        timer: 2000,
                    });
                },
            });
        };

        return {
            form,
            selectAll,
            submit,
        };
    },
};
</script>

<style>
.table-centered {
    text-align: center;
    vertical-align: middle;
}
</style>
