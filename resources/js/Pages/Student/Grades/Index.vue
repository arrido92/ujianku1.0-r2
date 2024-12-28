<template>
    <Head>
        <title>Nilai Saya - Portal Siswa</title>
    </Head>
    <div class="container mt-5">
        <h3 class="mb-4 text-center text-primary">Daftar Nilai Saya</h3>

        <!-- Tombol Kembali -->
        <Link
            href="/student/dashboard"
            class="mb-3 shadow-sm btn btn-primary btn-md"
            type="button"
        >
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
        </Link>

        <!-- Daftar Nilai -->
        <div v-if="grades.length > 0" class="custom-table-container">
            <table class="custom-table">
                <thead>
                    <tr>
                        <th>#</th>
                        <th>Mata Pelajaran</th>
                        <th>Ujian</th>
                        <th>Nilai</th>
                        <th>Waktu Sesi</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(grade, index) in grades" :key="index">
                        <td>{{ index + 1 }}</td>
                        <td>
                            <div class="d-flex align-items-center">
                                <i class="bi bi-journal-text me-2 text-info"></i>
                                <span>{{ grade.exam.lesson.title }}</span>
                            </div>
                        </td>
                        <td>
                            <div class="d-flex align-items-center">
                                <i class="bi bi-pencil-square me-2 text-warning"></i>
                                <span>{{ grade.exam.title }}</span>
                            </div>
                        </td>
                        <td>
                            <span v-if="grade.exam.show_answer === 'Y'" :class="['badge', grade.grade >= 75 ? 'bg-success' : 'bg-danger']">
                                {{ grade.grade }}
                            </span>
                            <span v-else class="badge bg-secondary">
                                Sedang Dikoreksi
                            </span>
                        </td>
                        <td class="text-start">
                            <span class="text-muted small">
                                {{ formatDate(grade.exam_session.start_time) }}
                                <br />
                                hingga
                                <br />
                                {{ formatDate(grade.exam_session.end_time) }}
                            </span>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>

        <!-- Jika Tidak Ada Nilai -->
        <div v-else>
            <div class="text-center alert alert-warning">
                <i class="bi bi-exclamation-triangle-fill me-2"></i>
                Belum ada nilai tersedia.
            </div>
        </div>
    </div>
</template>

<script>
import { Head, Link } from "@inertiajs/vue3";
import LayoutStudent from "../../../Layouts/Student.vue";

export default {
    layout: LayoutStudent,
    props: {
        grades: Array, // Nilai yang diterima dari backend
    },
    components: {
        Head,
        Link,
    },
    methods: {
        formatDate(date) {
            const options = { day: "2-digit", month: "long", year: "numeric", hour: "2-digit", minute: "2-digit" };
            return new Date(date).toLocaleDateString("id-ID", options);
        },
    },
};
</script>

<style scoped>
/* Container Tabel Khusus */
.custom-table-container {
    background-color: #f9f9f9; /* Latar belakang terang */
    border-radius: 8px;
    padding: 20px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); /* Bayangan halus */
    overflow-x: auto; /* Scroll horizontal untuk perangkat kecil */
}

/* Tabel */
.custom-table {
    width: 100%;
    border-collapse: separate;
    border-spacing: 0;
    text-align: center;
    background-color: white; /* Warna tabel */
    border-radius: 8px;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2); /* Bayangan tabel */
    font-size: 0.85rem;
}

.custom-table th {
    background-color: #007bff; /* Warna header tabel */
    color: white;
    font-weight: bold;
    text-transform: uppercase;
    padding: 10px;
    font-size: 0.85rem;
}

.custom-table td {
    padding: 12px 10px;
    font-size: 0.85rem;
    color: #333;
    word-wrap: break-word;
}

.custom-table tbody tr:nth-child(odd) {
    background-color: #f2f2f2; /* Warna baris ganjil */
}

.custom-table tbody tr:hover {
    background-color: #e6f7ff; /* Warna hover */
}

/* Badge Nilai */
.badge {
    font-size: 0.75rem;
    padding: 0.3em 0.5em;
}

.text-muted {
    font-size: 0.8rem;
}

/* Icon */
.bi {
    font-size: 1.2rem;
    vertical-align: middle;
}

/* Responsiveness */
@media (max-width: 768px) {
    .custom-table th,
    .custom-table td {
        padding: 8px 5px;
        font-size: 0.75rem;
    }

    .custom-table-container {
        padding: 15px;
    }

    .btn {
        font-size: 0.9rem;
    }
}
</style>
