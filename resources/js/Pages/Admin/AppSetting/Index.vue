<template>
    <Head>
        <title>Pengaturan Aplikasi</title>
    </Head>
    <div class="mt-5 mb-5 container-fluid">
        <div class="row">
            <div class="col-md-12">
                <div class="border-0 shadow card">
                    <div class="card-body">
                        <!-- Tab Navigation -->
                        <ul class="nav nav-tabs" id="appSettingTab" role="tablist">
                            <li class="nav-item">
                                <a
                                    class="nav-link active"
                                    id="app-settings-tab"
                                    data-bs-toggle="tab"
                                    href="#app-settings"
                                    role="tab"
                                    aria-controls="app-settings"
                                    aria-selected="true"
                                >
                                    Pengaturan Identitas
                                </a>
                            </li>
                            <li class="nav-item">
                                <a
                                    class="nav-link"
                                    id="exam-settings-tab"
                                    data-bs-toggle="tab"
                                    href="#exam-settings"
                                    role="tab"
                                    aria-controls="exam-settings"
                                    aria-selected="false"
                                >
                                    Pengaturan Ujian
                                </a>
                            </li>
                            <li class="nav-item">
                                <a
                                    class="nav-link"
                                    id="license-info-tab"
                                    data-bs-toggle="tab"
                                    href="#license-info"
                                    role="tab"
                                    aria-controls="license-info"
                                    aria-selected="false"
                                >
                                    Informasi Lisensi
                                </a>
                            </li>
                            <li class="nav-item">
                                <a
                                    class="nav-link"
                                    id="backup-tab"
                                    data-bs-toggle="tab"
                                    href="#backup"
                                    role="tab"
                                    aria-controls="backup"
                                    aria-selected="false"
                                >
                                    Backup & Restore
                                </a>
                            </li>
                        </ul>

                        <div class="tab-content" id="appSettingTabContent">
                            <!-- Tab: Pengaturan Identitas -->
                            <div
                                class="tab-pane fade show active"
                                id="app-settings"
                                role="tabpanel"
                                aria-labelledby="app-settings-tab"
                            >
                                <h5 class="mt-4"><i class="fa fa-cogs"></i> Identitas Lembaga</h5>
                                <hr />
                                <form @submit.prevent="update" enctype="multipart/form-data">
                                    <div class="row">
                                        <div class="mb-3 col-md-6">
                                            <label for="instansi" class="form-label">Instansi</label>
                                            <input
                                                type="text"
                                                id="instansi"
                                                class="form-control"
                                                v-model="form.instansi"
                                            />
                                        </div>
                                        <div
                                            class="mb-3 col-md-6"
                                            v-for="(field, index) in fields"
                                            :key="index"
                                        >
                                            <label :for="field.id" class="form-label">
                                                {{ field.label }}
                                            </label>
                                            <input
                                                v-if="!field.isFile"
                                                type="text"
                                                :id="field.id"
                                                class="form-control"
                                                v-model="form[field.name]"
                                            />
                                            <input
                                                v-else
                                                type="file"
                                                :id="field.id"
                                                class="form-control"
                                                @change="handleFileUpload(field.name, $event)"
                                            />
                                            <img
                                                v-if="field.isFile && appSetting[field.name]"
                                                :src="`/storage/${appSetting[field.name]}`"
                                                alt="Preview"
                                                class="mt-2 img-fluid img-thumbnail"
                                                style="max-height: 100px;"
                                            />
                                        </div>
                                    </div>
                                    <div class="d-flex justify-content-end">
                                        <button type="submit" class="btn btn-primary">
                                            <i class="fa fa-save"></i> Update
                                        </button>
                                    </div>
                                </form>
                            </div>

                            <!-- Tab: Pengaturan Ujian -->
                            <div
                                class="tab-pane fade"
                                id="exam-settings"
                                role="tabpanel"
                                aria-labelledby="exam-settings-tab"
                            >
                                <h5 class="mt-4"><i class="fa fa-laptop"></i> Pengaturan Ujian</h5>
                                <hr />
                                <form @submit.prevent="updateExamSettings" enctype="multipart/form-data">
                                    <div class="row">
                                        <div class="mb-3 col-md-4">
                                            <label for="nama_ujian" class="form-label">Nama Ujian</label>
                                            <select
                                                id="nama_ujian"
                                                class="form-control"
                                                v-model="examSettings.nama_ujian"
                                            >
                                                <option disabled value="">Pilih Nama Ujian</option>
                                                <option value="Asesmen Sumatif Akhir Semester">Asesmen Sumatif Akhir Semester</option>
                                                <option value="Asesmen Sumatif Akhir Tahun">Asesmen Sumatif Akhir Tahun</option>
                                                <option value="Ujian Madrasah">Ujian Madrasah</option>
                                                <option value="Ujian Sekolah">Ujian Sekolah</option>
                                            </select>
                                        </div>
                                        <div class="mb-3 col-md-4">
                                            <label for="tp_ujian" class="form-label">Tahun Pelajaran Ujian</label>
                                            <select
                                                id="tp_ujian"
                                                class="form-control"
                                                v-model="examSettings.tp_ujian"
                                            >
                                                <option disabled value="">Pilih Tahun Pelajaran</option>
                                                <option value="2027/2028">2027/2028</option>
                                                <option value="2026/2027">2026/2027</option>
                                                <option value="2025/2026">2025/2026</option>
                                                <option value="2024/2025">2024/2025</option>
                                            </select>
                                        </div>
                                        <div class="mb-3 col-md-4">
                                            <label for="web_ujian" class="form-label">Alamat Server</label>
                                            <input
                                                type="text"
                                                id="web_ujian"
                                                class="form-control"
                                                v-model="examSettings.web_ujian"
                                            />
                                        </div>
                                    </div>

                                    <div class="form-check">
                                        <input
                                            class="form-check-input"
                                            type="checkbox"
                                            id="safeExamBrowser"
                                            v-model="examSettings.safeExamBrowser"
                                        />
                                        <label class="form-check-label fw-bold" for="safeExamBrowser">
                                            Ujian Mode Aman
                                        </label>
                                    </div>
                                    <div v-if="examSettings.safeExamBrowser" class="mt-3 alert alert-warning">
                                        <strong>Catatan:</strong>
                                        <ul class="mt-2">
                                            <li>
                                                <i class="fa fa-laptop"></i>
                                                Menggunakan <strong>Safe Exam Browser</strong> jika menggunakan Laptop/PC.
                                            </li>
                                            <li>
                                                <i class="fa fa-mobile-alt"></i>
                                                Menggunakan <strong>Aplikasi Exam Khusus</strong> untuk HP Android.
                                            </li>
                                        </ul>
                                    </div>

                                    <div class="d-flex justify-content-end">
                                        <button type="submit" class="btn btn-primary">
                                            <i class="fa fa-save"></i> Simpan
                                        </button>
                                    </div>
                                </form>
                            </div>

                            <!-- Tab: Informasi Lisensi -->
                            <div
                                class="tab-pane fade"
                                id="license-info"
                                role="tabpanel"
                                aria-labelledby="license-info-tab"
                            >
                                <h5 class="mt-4"><i class="fa fa-key"></i> Informasi Lisensi</h5>
                                <hr />
                                <table class="table table-responsive">
                                    <tr>
                                        <td colspan="3" class="text-center">
                                            <strong>Ujianku V1.0</strong>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>License Key</td>
                                        <td>:</td>
                                        <th>{{ license.license_key }}</th>
                                    </tr>
                                    <tr>
                                        <td>Status</td>
                                        <td>:</td>
                                        <td>
                                            <span
                                                :class="{
                                                    'badge bg-success': license.status === 'active',
                                                    'badge bg-danger': license.status !== 'active',
                                                }"
                                            >
                                                {{ license.status }}
                                            </span>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>Berlaku s/d</td>
                                        <td>:</td>
                                        <th>{{ license.expires_at }}</th>
                                    </tr>
                                </table>
                                <p v-if="!license" class="text-danger">
                                    Informasi lisensi tidak tersedia.
                                </p>
                            </div>
                            <!-- Tab: Backup & Restore -->
                            <div
                                class="tab-pane fade"
                                id="backup"
                                role="tabpanel"
                                aria-labelledby="backup-tab"
                            >
                                <h5 class="mt-4"><i class="fa fa-database"></i> Backup & Restore</h5>
                                <hr />
                                <button @click="backupDatabase" class="btn btn-success">
                                    <i class="fa fa-download"></i> Backup Database
                                </button>
                                <input
                                    type="file"
                                    class="mt-3 form-control"
                                    @change="restoreDatabase"
                                    accept=".nizam"
                                />
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import Swal from "sweetalert2";
import { reactive } from "vue";
import { Head, router } from "@inertiajs/vue3";
import LayoutAdmin from "../../../Layouts/Admin.vue";

export default {
    layout: LayoutAdmin,
    props: {
        appSetting: Object,
        license: Object, // Tambahkan data lisensi
    },
    setup(props) {
        const form = reactive({ ...props.appSetting });
        const examSettings = reactive({
            nama_ujian: props.appSetting.nama_ujian || props.appSetting.nama_ujian,
            tp_ujian: props.appSetting.tp_ujian || props.appSetting.tp_ujian,
            web_ujian: props.appSetting.web_ujian || props.appSetting.web_ujian,
           safeExamBrowser: props.appSetting.enable_safe_exam_browser === 1 || props.appSetting.enable_safe_exam_browser === true,
    });

        const fields = [
            { id: "nsm", name: "nsm", label: "NSM", isFile: false },
            { id: "npsn", name: "npsn", label: "NPSN", isFile: false },
            { id: "madrasah", name: "madrasah", label: "Madrasah", isFile: false },
            { id: "kamad", name: "kamad", label: "Kepala Madrasah", isFile: false },
            { id: "nip", name: "nip", label: "NIP", isFile: false },
            { id: "alamat", name: "alamat", label: "Alamat", isFile: false },
            { id: "desa", name: "desa", label: "Desa", isFile: false },
            { id: "kec", name: "kec", label: "Kecamatan", isFile: false },
            { id: "kota", name: "kota", label: "Kota", isFile: false },
            { id: "telp", name: "telp", label: "Telepon", isFile: false },
            { id: "logo", name: "logo", label: "Logo", isFile: true },
            { id: "logo_instansi", name: "logo_instansi", label: "Logo Instansi", isFile: true },
            { id: "ttd", name: "ttd", label: "Tanda Tangan", isFile: true },
        ];

        const handleFileUpload = (field, event) => {
            const file = event.target.files[0];
            if (file) {
                form[field] = file;
            }
        };

        const update = () => {
            const data = new FormData();
            Object.keys(form).forEach((key) => {
                if (key === "logo" || key === "logo_instansi" || key === "ttd") {
                    if (form[key] instanceof File) {
                        data.append(key, form[key]);
                    }
                } else {
                    data.append(key, form[key]);
                }
            });

            router.post("/admin/app-settings/update", data, {
                onSuccess: () => {
                    Swal.fire({
                        title: "Berhasil!",
                        text: "Pengaturan berhasil diperbarui!",
                        icon: "success",
                        timer: 2000,
                        showConfirmButton: false,
                    });
                },
                onError: () => {
                    Swal.fire({
                        title: "Gagal!",
                        text: "Terjadi kesalahan saat memperbarui pengaturan.",
                        icon: "error",
                    });
                },
            });
        };
        const updateExamSettings = () => {
                const data = {
                    nama_ujian: examSettings.nama_ujian,
                    tp_ujian: examSettings.tp_ujian,
                    web_ujian: examSettings.web_ujian,
                    enable_safe_exam_browser: examSettings.safeExamBrowser, };
                router.post('/admin/app-settings/update', data, {
                    onSuccess: () => {
                        Swal.fire({
                            title: "Berhasil!",
                            text: "Pengaturan ujian berhasil diperbarui!",
                            icon: "success",
                            timer: 2000,
                            showConfirmButton: false,
                        });
                    },
                    onError: () => {
                        Swal.fire({
                            title: "Gagal!",
                            text: "Terjadi kesalahan saat memperbarui pengaturan ujian.",
                            icon: "error",
                        });
                    },
                });
            };

        return {
            form,
            fields,
            handleFileUpload,
            update,
            license: props.license,
            currentYear: new Date().getFullYear(),
            examSettings,
            updateExamSettings,
        };
    },
};
</script>
