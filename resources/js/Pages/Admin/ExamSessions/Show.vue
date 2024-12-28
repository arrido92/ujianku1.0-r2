<template>
    <Head>
        <title>Detail Sesi Ujian - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
        <div class="row">
            <div class="col-md-12">
                <Link href="/admin/exam_sessions" class="mb-3 border-0 shadow btn btn-md btn-primary" type="button">
                    <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
                </Link>

                <!-- Detail Sesi -->
                <div class="mb-4 border-0 shadow card">
                    <div class="card-body">
                        <h5><i class="fa fa-stopwatch"></i> Detail Sesi Ujian</h5>
                        <hr>
                        <div class="table-responsive">
                            <table class="table mb-0 rounded table-bordered table-nowrap">
                                <tbody>
                                    <tr>
                                        <td style="width:30%" class="fw-bold">Nama Ujian</td>
                                        <td>{{ exam_session.exam.title }}</td>
                                    </tr>
                                    <tr>
                                        <td class="fw-bold">Mata Pelajaran</td>
                                        <td>{{ exam_session.exam.lesson.title }}</td>
                                    </tr>
                                    <tr>
                                        <td class="fw-bold">Kelas</td>
                                        <td v-for="classroom in exam_session.classrooms" :key="classroom.id">
                                                    {{ classroom.title }}
                                        </td>
                                    </tr>
                                    <tr>
                                        <td class="fw-bold">Sesi</td>
                                        <td>{{ exam_session.sesi.title }}</td>
                                    </tr>
                                    <tr>
                                        <td class="fw-bold">Subsesi</td>
                                        <td>{{ exam_session.title }}</td>
                                    </tr>
                                    <tr>
                                        <td class="fw-bold">Mulai</td>
                                        <td>{{ formatDate(exam_session.start_time) }}</td>
                                    </tr>
                                    <tr>
                                        <td class="fw-bold">Selesai</td>
                                        <td>{{ formatDate(exam_session.end_time) }}</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>

                <!-- Enrolled Siswa -->
                <div class="border-0 shadow card">
                    <div class="card-body">
                        <h5><i class="fa fa-user-plus"></i> Daftar Siswa Ujian</h5>
                        <hr>

                        <div class="mb-3 d-flex align-items-center">
                            <Link :href="`/admin/exam_sessions/${exam_session.id}/enrolle/create`"
                                  class="border-0 shadow btn btn-md btn-primary me-3"
                                  type="button">
                                <i class="fa fa-user-plus"></i> Daftarkan Siswa
                            </Link>
                            <button
                                @click="deleteSelected"
                                class="border-0 shadow btn btn-md btn-danger"
                                :disabled="selectedStudents.length === 0"
                            >
                                <i class="fa fa-trash"></i> Hapus Terpilih
                            </button>
                        </div>

                        <div class="mt-3 table-responsive">
                            <table class="table mb-0 rounded table-bordered table-centered table-nowrap">
                                <thead class="thead-dark">
                                    <tr class="border-0">
                                        <th class="border-0 rounded-start" style="width: 5%;">No.</th>
                                        <th class="border-0">Nama Siswa</th>
                                        <th class="border-0">Kelas</th>
                                        <th class="border-0">Jenis Kelamin</th>
                                        <th class="border-0 rounded-end" style="width:5%">
                                            <input type="checkbox" v-model="selectAll" @change="toggleSelectAll" />
                                        </th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr v-for="(group, index) in exam_groups.data" :key="group.id">
                                        <td class="text-center fw-bold">
                                            {{ index + 1 + (exam_groups.current_page - 1) * exam_groups.per_page }}
                                        </td>
                                        <td>{{ group.student.name }}</td>
                                        <td>{{ group.student.classroom.title }}</td>
                                        <td>{{ group.student.gender }}</td>
                                        <td class="text-center">
                                            <input type="checkbox" v-model="selectedStudents" :value="group.id" />
                                        </td>
                                    </tr>
                                    <tr v-if="exam_groups.data.length === 0">
                                        <td colspan="5" class="text-center fw-bold">Tidak ada siswa yang di-enroll.</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                        <p>Jumlah Siswa: {{ exam_session.exam_groups.length }} Orang</p>
                        <Pagination :links="exam_groups.links" align="end" />
                    </div>
                </div>

            </div>
        </div>
    </div>
</template>

<script>
import LayoutAdmin from "../../../Layouts/Admin.vue";
import { Head, Link, router } from "@inertiajs/vue3";
import Pagination from "../../../Components/Pagination.vue";
import Swal from "sweetalert2";
import { ref } from "vue";

export default {
    layout: LayoutAdmin,
    components: {
        Head,
        Link,
        Pagination,
    },
    props: {
        error: Object,
        exam_session: Object,
        exam_groups: Object,
    },
    setup(props) {
        const selectedStudents = ref([]);
        const selectAll = ref(false);

        const toggleSelectAll = () => {
            if (selectAll.value) {
                selectedStudents.value = props.exam_groups.data.map((group) => group.id);
            } else {
                selectedStudents.value = [];
            }
        };

        const deleteSelected = () => {
            Swal.fire({
                title: "Apakah Anda yakin?",
                text: "Data siswa yang dipilih akan dihapus dari sesi ujian!",
                icon: "warning",
                showCancelButton: true,
                confirmButtonColor: "#d33",
                cancelButtonColor: "#3085d6",
                confirmButtonText: "Ya, hapus!",
            }).then((result) => {
                if (result.isConfirmed) {
                    router.delete(`/admin/exam_sessions/${props.exam_session.id}/enrolle/bulk-destroy`, {
                        data: { ids: selectedStudents.value },
                        onSuccess: () => {
                            Swal.fire("Deleted!", "Siswa berhasil dihapus dari sesi ujian.", "success");
                            selectedStudents.value = [];
                            selectAll.value = false;
                        },
                        onError: () => {
                            Swal.fire("Error!", "Gagal menghapus siswa dari sesi ujian.", "error");
                        },
                    });
                }
            });
        };

        const formatDate = (date) => {
            return new Date(date).toLocaleString("id-ID", {
                year: "numeric",
                month: "long",
                day: "numeric",
                hour: "2-digit",
                minute: "2-digit",
            });
        };

        return {
            selectedStudents,
            selectAll,
            toggleSelectAll,
            deleteSelected,
            formatDate,
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
