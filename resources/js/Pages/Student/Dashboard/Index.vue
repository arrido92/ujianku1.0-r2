<template>
    <Head>
        <title>Dashboard Siswa - Aplikasi Ujian Online</title>
    </Head>
    <div class="container">
        <!-- Selamat Datang -->
        <div class="row">
            <div class="col-md-12">
                <div class="border-0 shadow alert alert-success">
                    Selamat Datang <strong>{{ auth.student?.name || 'Siswa' }}</strong>
                </div>
            </div>
        </div>

        <!-- Daftar Ujian -->
        <div class="row" v-if="exam_groups.length > 0">
            <div class="mb-4 col-md-4" v-for="(data, index) in exam_groups" :key="index">
                <div class="border-0 shadow card">
                    <div class="card-body">
                        <h5 class="text-sm card-title text-truncate">{{ data.exam_group.exam.title }}</h5>
                        <hr>
                        <div class="table-responsive">
                            <table class="table text-sm table-sm table-bordered text-nowrap">
                                <tbody>
                                    <tr>
                                        <td class="fw-bold">Mapel</td>
                                        <td>{{ data.exam_group.exam.lesson?.title || '-' }}</td>
                                    </tr>
                                    <tr>
                                        <td class="fw-bold">Kelas</td>
                                        <td>{{ data.exam_group.student?.classroom?.title || '-' }}</td>
                                    </tr>
                                    <tr>
                                        <td class="fw-bold">Sesi</td>
                                        <td>{{ data.exam_session?.sesi?.title || '-' }}</td>
                                    </tr>
                                    <tr>
                                        <td class="fw-bold">Subsesi</td>
                                        <td>{{ data.exam_group.exam_session?.title || '-' }}</td>
                                    </tr>
                                    <tr>
                                        <td class="fw-bold">Mulai</td>
                                        <td>{{ formatDate(data.exam_group.exam_session?.start_time) }}</td>
                                    </tr>
                                    <tr>
                                        <td class="fw-bold">Selesai</td>
                                        <td>{{ formatDate(data.exam_group.exam_session?.end_time) }}</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>

                        <!-- Tombol Kerjakan, Terlewat, atau Selesai -->
                        <div v-if="data.grade.end_time == null">
                            <div v-if="examTimeRangeChecker(data.exam_group.exam_session.start_time, data.exam_group.exam_session.end_time)">
                                <div v-if="data.grade.start_time == null">
                                    <Link :href="`/student/exam-confirmation/${data.exam_group.id}`" class="mt-2 text-white border-0 shadow btn btn-md btn-success w-100">Kerjakan</Link>
                                </div>
                                <div v-else>
                                    <Link :href="`/student/exam/${data.exam_group.id}/1`" class="mt-2 border-0 shadow btn btn-md btn-info w-100">Lanjut Kerjakan</Link>
                                </div>
                            </div>
                            <div v-else-if="examTimeEndChecker(data.exam_group.exam_session.end_time)">
                                <button class="mt-2 btn btn-danger btn-sm w-100" disabled>
                                    Ujian Terlewat
                                </button>
                            </div>
                            <div v-else>
                                <button class="mt-2 btn btn-gray-700 btn-sm w-100" disabled>
                                    Belum Dimulai
                                </button>
                            </div>
                        </div>
                        <div v-else>
                            <button class="mt-2 btn btn-danger btn-sm w-100" disabled>
                                Sudah Dikerjakan
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Jika Tidak Ada Ujian -->
        <div class="row" v-else>
            <div class="col-md-12">
                <div class="border-0 shadow alert alert-danger">
                    <i class="fa fa-info-circle"></i> Tidak ada ujian yang tersedia
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import { Head, Link } from "@inertiajs/vue3";
import LayoutStudent from "../../../Layouts/Student.vue";

export default {
    layout: LayoutStudent,
    components: {
            Head,
            Link,

        },
    props: {
        exam_groups: Array,
        auth: Object,
    },
    methods: {
        examTimeRangeChecker(startTime, endTime) {
            const now = new Date();
            const start = new Date(startTime);
            const end = new Date(endTime);
            return now >= start && now <= end;
        },
        examTimeEndChecker(endTime) {
            const now = new Date();
            const end = new Date(endTime);
            return now > end;
        },
        formatDate(date) {
            if (!date) return "-";
            const options = {
                day: "2-digit",
                month: "long",
                year: "numeric",
                hour: "2-digit",
                minute: "2-digit",
            };
            return new Date(date).toLocaleDateString("id-ID", options);
        },
    },
};
</script>
