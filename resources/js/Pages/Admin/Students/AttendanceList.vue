<template>
    <Head>
      <title>Daftar Hadir Siswa - Aplikasi Ujian Online</title>
    </Head>
          <!-- Button Kembali -->
          <Link href="/admin/students" class="mb-3 border-0 shadow btn btn-md btn-primary">
            <i class="fa fa-long-arrow-alt-left me-2"></i> Kembali
          </Link>
          <div class="mt-5 mb-5 container-fluid">
    <div class="container mt-5">
        <h4 class="mb-4 text-center">Daftar Hadir Siswa</h4>
        <!-- Tombol Download PDF -->
        <div class="mt-3 d-flex justify-content-end">
            <button
        :disabled="!selectedSession || !selectedClassroom || !selectedExam"
        class="btn btn-primary"
        @click="downloadPdf"
    >
        Download PDF
    </button>
        </div>
        <!-- Filter Section -->
        <div class="mb-4 row">
            <div class="col-md-4">
                <!-- Filter Sesi -->
                <label for="sessionDropdown" class="form-label">Pilih Sesi</label>
                <select
                    id="sessionDropdown"
                    :value="selectedSession"
                    @change="onSessionChange($event.target.value)"
                    class="form-select"
                >
                    <option value="" disabled>Pilih Sesi</option>
                    <option v-for="session in examSessions" :key="session.title" :value="session.ids[0]">
                        {{ session.title }}
                    </option>
                </select>
            </div>
            <div class="col-md-4">
                <!-- Filter Kelas -->
                <label for="classroomDropdown" class="form-label">Pilih Kelas</label>
                <select
                    id="classroomDropdown"
                    :value="selectedClassroom"
                    @change="onClassroomChange($event.target.value)"
                    class="form-select"
                    :disabled="!selectedSession"
                >
                    <option value="" disabled>Pilih Kelas</option>
                    <option v-for="classroom in classrooms" :key="classroom.id" :value="classroom.id">
                        {{ classroom.title }}
                    </option>
                </select>
            </div>
            <div class="col-md-4">
                <!-- Filter Ujian -->
                <label for="examDropdown" class="form-label">Pilih Ujian</label>
                <select
                    id="examDropdown"
                    :value="selectedExam"
                    @change="onExamChange($event.target.value)"
                    class="form-select"
                    :disabled="!selectedClassroom"
                >
                    <option value="" disabled>Pilih Ujian</option>
                    <option v-for="exam in exams" :key="exam.id" :value="exam.id">
                        {{ exam.title }}
                    </option>
                </select>
            </div>
        </div>

        <!-- Tabel Data Siswa -->
        <div class="table-responsive">
            <table class="table table-bordered table-striped">
                <thead class="table-dark">
                    <tr>
                        <th>No</th>
                        <th>NIS</th>
                        <th>NISN</th>
                        <th>Nama</th>
                        <th>Kelas</th>
                        <th>Ujian</th>
                        <th>Kehadiran</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(group, index) in examGroups" :key="group.id">
                        <td>{{ index + 1 }}</td>
                        <td>{{ group.nis }}</td>
                        <td>{{ group.nisn }}</td>
                        <td>{{ group.name }}</td>
                        <td>{{ group.classroom }}</td>
                        <td>{{ group.exam }}</td>
                        <td>
                            <input type="checkbox" v-model="group.attendance" />
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
        <div v-if="examGroups.length === 0" class="mt-3 text-center alert alert-warning">
            Data siswa tidak ditemukan.
        </div>
    </div>
    </div>


</template>

<script>
import LayoutAdmin from "../../../Layouts/Admin.vue";
import { Head, Link } from "@inertiajs/vue3";
export default {
    layout: LayoutAdmin,
    components: {
      Head,
      Link,
    },
    props: {
        examGroups: Array,
        classrooms: Array,
        exams: Array,
        examSessions: Array,
        selectedSession: [String, Number],
        selectedClassroom: [String, Number],
        selectedExam: [String, Number],
    },
    emits: ['update:selectedSession', 'update:selectedClassroom', 'update:selectedExam'],
    methods: {
        onSessionChange(value) {
            this.$emit('update:selectedSession', value);
            this.$inertia.get('/admin/students/attendance-list', {
                exam_session_id: value,
            });
        },
        onClassroomChange(value) {
            this.$emit('update:selectedClassroom', value);
            this.$inertia.get('/admin/students/attendance-list', {
                exam_session_id: this.selectedSession,
                classroom_id: value,
            });
        },
        onExamChange(value) {
            this.$emit('update:selectedExam', value);
            this.$inertia.get('/admin/students/attendance-list', {
                exam_session_id: this.selectedSession,
                classroom_id: this.selectedClassroom,
                exam_id: value,
            });
        },
        downloadPdf() {
            if (this.selectedSession && this.selectedClassroom && this.selectedExam) {
                const params = new URLSearchParams({
                    exam_session_id: this.selectedSession,
                    classroom_id: this.selectedClassroom,
                    exam_id: this.selectedExam,
                });

                // Buka URL di tab baru untuk unduhan
                window.open(`/admin/students/attendance-pdf?${params.toString()}`, '_blank');
            } else {
                alert('Pastikan semua filter telah dipilih sebelum mendownload PDF.');
            }
        },
    },
};
</script>

<style scoped>
.container {
    padding: 20px;
}

.table-responsive {
    margin-top: 20px;
}

.form-select {
    max-width: 300px;
    margin: 0 auto;
}
</style>
