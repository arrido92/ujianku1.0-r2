<template>
    <div>
        <Head>
            <title>Monitoring Ujian - Aplikasi Ujian Online</title>
        </Head>

        <!-- Pencarian Nama -->
        <div class="mb-4">
            <label for="searchInput" class="form-label">Cari Siswa</label>
            <form @submit.prevent="handleSearch" class="gap-2 d-flex">
                <input
                    type="text"
                    id="searchInput"
                    class="form-control"
                    v-model="search"
                    placeholder="Cari nama atau NISN..."
                />
                <button class="btn btn-outline-primary" type="submit">
                    <i class="fa fa-search"></i> Cari
                </button>
            </form>
        </div>

        <!-- Tabel Monitoring -->
        <div class="table-responsive">
            <table class="table table-bordered table-striped">
                <thead class="table-dark">
                    <tr>
                        <th>No</th>
                        <th>NISN</th>
                        <th>Nama</th>
                        <th>Detail Ujian</th>
                        <th>Status</th>
                        <th>Jawaban</th>
                        <th>Lama Ujian</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-if="students.data.length === 0">
                        <td colspan="7" class="text-center fw-bold">
                            Tidak ada data siswa sedang ujian tersedia.
                        </td>
                    </tr>
                    <tr v-for="(student, index) in students.data" :key="student.id">
                        <td>{{ index + 1 + (students.current_page - 1) * students.per_page }}</td>
                        <td>{{ student.nisn }}</td>
                        <td>{{ student.name }}</td>
                        <td>
                            <dl>
                                <dt>Ujian: {{ student.exam }}</dt>
                                <dt>Mapel: {{ student.lesson }}</dt>
                                <dt>Kelas: {{ student.classroom }}</dt>
                            </dl>
                        </td>
                        <td>
                            <span
                                :class="{
                                    'badge bg-success': student.status === 'Selesai',
                                    'badge bg-warning': student.status === 'Sedang Dikerjakan',
                                    'badge bg-gray-500': student.status === 'Belum Mulai',
                                }"
                            >
                                {{ student.status }}
                            </span>
                        </td>
                        <td>
                            {{ student.answers_count }} Soal
                        </td>
                        <td>
                            {{ formatDuration(student.exam_duration) }}
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
import LayoutAdmin from "../../../Layouts/Admin.vue";
import Pagination from "../../../Components/Pagination.vue";
import { ref } from "vue";
import { router } from "@inertiajs/vue3";

export default {
    layout: LayoutAdmin,
    components: {
        Pagination,
    },
    props: {
        students: Object, // Data siswa
        filters: Object, // Filter pencarian
    },
    setup(props) {
        const search = ref(props.filters.q || ""); // Ambil query pencarian dari props

        // Fungsi pencarian
        const handleSearch = () => {
            router.get("/admin/monitoring", {
                q: search.value,
            });
        };

        // Fungsi paginasi
        const handlePagination = (url) => {
            router.get(url, {
                q: search.value,
            });
        };

        // Format durasi menjadi jam dan menit
        const formatDuration = (duration) => {
            const hours = Math.floor(duration / 60);
            const minutes = duration % 60;
            return `${hours > 0 ? hours + " jam " : ""}${minutes} menit`;
        };

        return {
            search,
            handleSearch,
            handlePagination,
            formatDuration,
        };
    },
};
</script>

<style scoped>
.table-responsive {
    margin-top: 20px;
}
.form-control {
    max-width: 400px;
}
</style>
