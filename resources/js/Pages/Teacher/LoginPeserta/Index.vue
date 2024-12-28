<template>
    <div>
        <Head>
            <title>Login Peserta - Aplikasi Ujian Online</title>
        </Head>
        <!-- Header -->
        <div class="mb-4">
            <h4>Daftar Login Siswa Ujian</h4>
        </div>

        <!-- Tabel Monitoring -->
        <div class="table-responsive">
            <table class="table table-bordered table-striped">
                <thead class="table-dark">
                    <tr>
                        <th style="width:5%">No</th>
                        <th style="width:10%">NISN</th>
                        <th>Nama Siswa</th>
                        <th style="width:5%">Kelas</th>
                        <th style="width:20%">Waktu Login</th>
                        <th style="width:10%">Aksi</th>
                    </tr>
                </thead>
                <tbody>
                    <!-- Pesan jika tidak ada data -->
                    <tr v-if="students.data.length === 0">
                        <td colspan="6" class="text-center">Tidak ada data siswa.</td>
                    </tr>
                    <!-- Daftar siswa -->
                    <tr v-for="(student, index) in students.data" :key="student.id">
                        <td>{{ index + 1 + (students.current_page - 1) * students.per_page }}</td>
                        <td>{{ student.nisn }}</td>
                        <td>{{ student.name }}</td>
                        <td>{{ student.classroom.title }}</td>
                        <td>{{ formatDate(student.last_login) }} Pukul {{ formatTime(student.last_login) }} WIB</td>
                        <td>
                            <button
                                class="btn btn-danger btn-sm"
                                @click="resetLogin(student.id)"
                            >
                                Reset Login
                            </button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>

        <!-- Pagination -->
        <div class="mt-3">
            <Pagination :links="students.links" align="end" @navigate="handlePagination" />
        </div>
    </div>
</template>

<script>
import LayoutTeacher from "../../../Layouts/Teacher.vue";
import { ref } from "vue";
import { Head, router } from "@inertiajs/vue3";
import Pagination from "../../../Components/Pagination.vue";
import Swal from "sweetalert2";

export default {
    layout: LayoutTeacher,
    components: {
        Pagination,
        Head,
    },
    props: {
        students: Object, // Data siswa dari backend
    },
    setup(props) {
        const resetLogin = (studentId) => {
    Swal.fire({
        title: "Tunggu sebentar...",
        text: "Sedang mereset login siswa...",
        allowOutsideClick: false,
        didOpen: () => {
            Swal.showLoading();
        },
    });

    router.post(`/teacher/login-peserta/reset/${studentId}`, {}, {
        preserveScroll: true,
        onSuccess: () => {
            Swal.fire("Berhasil", "Login siswa berhasil di-reset.", "success");
        },
        onError: () => {
            Swal.fire("Gagal", "Terjadi kesalahan saat mereset login.", "error");
        },
    });
};

        // Fungsi untuk navigasi halaman
        const handlePagination = (url) => {
            router.get(url);
        };
        const formatDate = (date) => {
            return new Date(date).toLocaleString("id-ID", {
                year: "numeric",
                month: "short",
                day: "numeric",
            });
        };
        const formatTime = (date) => {
            return new Date(date).toLocaleString("id-ID", {
                hour: "2-digit",
                minute: "2-digit",
            });
        };

        return {
            resetLogin,
            handlePagination,
            formatDate,
            formatTime,
        };
    },
};
</script>

<style scoped>
.table-responsive {
    margin-top: 20px;
}
</style>
