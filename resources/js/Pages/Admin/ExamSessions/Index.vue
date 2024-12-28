<template>
    <Head>
        <title>Sesi Ujian - Aplikasi Ujian Online</title>
    </Head>
    <div class="container mt-4 mb-5">
        <!-- Header dengan Tombol Tambah dan Pencarian -->
        <div class="mb-2 row">
            <div class="col-md-3">
                <Link href="/admin/exam_sessions/create" class="btn btn-sm btn-primary w-100">
                    <i class="fa fa-plus-circle"></i> Tambah
                </Link>
            </div>
            <div class="col-md-9">
                <form @submit.prevent="handleSearch" class="d-flex">
                    <input
                        type="text"
                        class="form-control form-control-sm me-2"
                        v-model="search"
                        placeholder="Cari..."
                    />
                    <button type="submit" class="btn btn-sm btn-outline-secondary">
                        <i class="fa fa-search"></i>
                    </button>
                </form>
            </div>
        </div>

        <!-- Tabel Data -->
        <div class="shadow-sm card">
            <div class="p-2 card-body">
                <div class="table-responsive">
                    <table class="table align-middle table-sm table-bordered table-hover">
                        <thead class="text-center table-dark">
                            <tr>
                                <th>No</th>
                                <th>Ujian</th>
                                <th>Sesi</th>
                                <th>Kelas</th>
                                <th>Siswa</th>
                                <th>Mulai</th>
                                <th>Selesai</th>
                                <th>Status</th>
                                <th>Aksi</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(exam_session, index) in exam_sessions.data" :key="exam_session.id">
                                <!-- No -->
                                <td class="text-center">
                                    {{ index + 1 + (exam_sessions.current_page - 1) * exam_sessions.per_page }}
                                </td>

                                <!-- Informasi Ujian -->
                                <td>
                                    <strong>{{ exam_session.exam.title }}</strong><br />
                                    <small>Mapel: {{ exam_session.exam.lesson.title }}</small>
                                </td>

                                <!-- Sesi Ujian -->
                                <td class="text-center">
                                    <strong>{{ exam_session.sesi.title }}</strong><br />
                                    <small>{{ exam_session.title }}</small>
                                </td>

                                <!-- Kelas -->
                                <td>
                                    <ul class="mb-0 list-unstyled">
                                        <li v-for="(classroom, idx) in filteredClassrooms(exam_session)" :key="idx">
                                            <span class="badge bg-info">{{ classroom.title }}</span>
                                        </li>
                                    </ul>
                                </td>

                                <!-- Jumlah Siswa -->
                                <td class="text-center">
                                    {{ exam_session.exam_groups.length }}
                                </td>

                                <!-- Waktu Mulai -->
                                <td class="text-center">
                                    <small>{{ formatHari(exam_session.start_time) }}</small><br />
                                    {{ formatTime(exam_session.start_time) }}
                                </td>

                                <!-- Waktu Selesai -->
                                <td class="text-center">
                                    <small>{{ formatHari(exam_session.end_time) }}</small><br />
                                    {{ formatTime(exam_session.end_time) }}
                                </td>

                                <!-- Status -->
                                <td class="text-center">
                                    <input
                                        class="form-check-input"
                                        type="checkbox"
                                        :checked="exam_session.is_active"
                                        @change="toggleStatus(exam_session.id)"
                                    />
                                </td>

                                <!-- Aksi -->
                                <td class="text-center">
                                    <div class="gap-1 d-flex justify-content-center">
                                        <Link :href="`/admin/exam_sessions/${exam_session.id}`" class="btn btn-sm btn-primary" title="Detail">
                                            <i class="fa fa-eye"></i>
                                        </Link>
                                        <Link :href="`/admin/exam_sessions/${exam_session.id}/edit`" class="btn btn-sm btn-info" title="Edit">
                                            <i class="fa fa-pencil-alt"></i>
                                        </Link>
                                        <button
                                            @click.prevent="destroy(exam_session.id)"
                                            class="btn btn-sm btn-danger"
                                            title="Hapus"
                                        >
                                            <i class="fa fa-trash"></i>
                                        </button>
                                        <button
                                            @click="generatePDF(exam_session.id)"
                                            class="text-white btn btn-sm btn-success"
                                            title="Berita Acara"
                                        >
                                            <i class="fa fa-file-pdf"></i> BA
                                        </button>
                                    </div>
                                </td>
                            </tr>

                            <!-- Jika Tidak Ada Data -->
                            <tr v-if="exam_sessions.data.length === 0">
                                <td colspan="9" class="text-center">Tidak ada data ditemukan.</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
                <!-- Pagination -->
                <Pagination :links="exam_sessions.links" align="end" />
            </div>
        </div>
    </div>
</template>

<script>
import LayoutAdmin from "../../../Layouts/Admin.vue";
import Pagination from "../../../Components/Pagination.vue";
import { Head, Link, router } from "@inertiajs/vue3";
import { ref } from "vue";
import Swal from "sweetalert2";

export default {
    layout: LayoutAdmin,
    components: {
        Head,
        Link,
        Pagination,
    },
    props: {
        exam_sessions: Object,
    },
    setup() {
        const search = ref("");

        const handleSearch = () => {
            router.get("/admin/exam_sessions", { q: search.value }, { preserveState: true });
        };

        const toggleStatus = (id) => {
            router.patch(`/admin/exam_sessions/${id}/toggle-status`, {}, {
                onSuccess: () => {
                    Swal.fire("Berhasil!", "Status berhasil diperbarui.", "success");
                },
                onError: () => {
                    Swal.fire("Gagal!", "Gagal memperbarui status ujian.", "error");
                },
            });
        };

        const destroy = (id) => {
            Swal.fire({
                title: "Apakah Anda yakin?",
                text: "Data ini tidak dapat dikembalikan!",
                icon: "warning",
                showCancelButton: true,
                confirmButtonText: "Ya, Hapus!",
                cancelButtonText: "Batal",
            }).then((result) => {
                if (result.isConfirmed) {
                    router.delete(`/admin/exam_sessions/${id}`, {
                        onSuccess: () => {
                            Swal.fire("Deleted!", "Sesi ujian berhasil dihapus.", "success");
                        },
                        onError: () => {
                            Swal.fire("Error!", "Gagal menghapus sesi ujian.", "error");
                        },
                    });
                }
            });
        };

        const filteredClassrooms = (exam_session) => {
            return exam_session.exam.classrooms.filter(
                (classroom) => classroom.pivot.exam_session_id === exam_session.id
            );
        };

        const formatHari = (date) => new Date(date).toLocaleDateString("id-ID", { weekday: "long" });
        const formatTime = (date) => new Date(date).toLocaleTimeString("id-ID", { hour: "2-digit", minute: "2-digit" });

        const generatePDF = (id) => {
            window.open(`/admin/exam_sessions/${id}/generate-berita-acara`, "_blank");
        };

        return {
            search,
            handleSearch,
            destroy,
            toggleStatus,
            filteredClassrooms,
            formatHari,
            formatTime,
            generatePDF,
        };
    },
};
</script>

<style scoped>
.table th,
.table td {
    padding: 6px; /* Lebih kecil */
    font-size: 0.85rem;
    white-space: nowrap; /* Kolom tetap ringkas */
}

.badge {
    font-size: 0.8rem; /* Ukuran badge lebih kecil */
}

.form-check-input {
    transform: scale(0.8);
}

.table-hover tbody tr:hover {
    background-color: #f8f9fa;
}

.text-center {
    text-align: center;
}
</style>
