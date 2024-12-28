<template>
    <Head>
        <title>Login Siswa - Aplikasi Ujian Online</title>
    </Head>
    <div class="mt-5 row justify-content-center">
        <div class="col-md-4 col-sm-6">
            <div class="p-4 bg-white border-0 rounded shadow-lg">
                <!-- Logo dan Judul -->
                <div class="mb-4 text-center">
                    <img :src="`/storage/${appSettings.logo}`" alt="Logo" style="height: 65px;" class="mb-3">
                    <h4 class="text-primary fw-bold">Login Siswa</h4>
                    <p class="text-muted small">Akses portal ujian Anda dengan NISN dan password</p>
                </div>

                <!-- Error Alerts -->
                <div v-if="errors.message" class="alert alert-danger small">
                    {{ errors.message }}
                </div>
                <div v-if="$page.props.session.error" class="alert alert-danger small">
                    {{ $page.props.session.error }}
                </div>

                <!-- Form -->
                <form @submit.prevent="submit" class="mt-3">
                    <!-- NISN -->
                    <div class="mb-3">
                        <label for="nisn" class="form-label small">NISN</label>
                        <div class="input-group">
                            <span class="text-white input-group-text bg-primary">
                                <i class="fa fa-user"></i>
                            </span>
                            <input
                                type="number"
                                class="form-control form-control-sm"
                                v-model="form.nisn"
                                placeholder="Masukkan NISN"
                            />
                        </div>
                        <div v-if="errors.nisn" class="mt-1 text-danger small">
                            {{ errors.nisn }}
                        </div>
                    </div>

                    <!-- Password -->
                    <div class="mb-3">
                        <label for="password" class="form-label small">Password</label>
                        <div class="input-group">
                            <span class="text-white input-group-text bg-primary">
                                <i class="fa fa-lock"></i>
                            </span>
                            <input
                                :type="showPassword ? 'text' : 'password'"
                                class="form-control form-control-sm"
                                v-model="form.password"
                                placeholder="Masukkan Password"
                            />
                            <span
                                class="text-white input-group-text bg-primary"
                                style="cursor: pointer;"
                                @click="togglePasswordVisibility"
                            >
                                <i :class="showPassword ? 'fa fa-eye-slash' : 'fa fa-eye'"></i>
                            </span>
                        </div>
                        <div v-if="errors.password" class="mt-1 text-danger small">
                            {{ errors.password }}
                        </div>
                    </div>

                    <!-- Submit Button -->
                    <div class="d-grid">
                        <button type="submit" class="shadow-sm btn btn-primary btn-sm">Login</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import { Head, router } from "@inertiajs/vue3";
import { reactive, ref } from "vue";

export default {
    components: {
        Head,
    },
    computed: {
        appSettings() {
            return this.$page.props.app_settings || {};
        },
    },
    props: {
        errors: Object,
    },
    setup() {
        const form = reactive({
            nisn: "",
            password: "",
        });

        // Password visibility toggle
        const showPassword = ref(false);

        const togglePasswordVisibility = () => {
            showPassword.value = !showPassword.value;
        };

        const submit = () => {
            router.post("/students/login", {
                nisn: form.nisn,
                password: form.password,
            });
        };

        return {
            form,
            submit,
            showPassword,
            togglePasswordVisibility,
        };
    },
};
</script>

<style scoped>
/* Gaya Form */
.form-label {
    font-size: 0.85rem;
    color: #495057;
}

.input-group-text {
    font-size: 0.85rem;
    border-radius: 0.25rem;
}

.input-group .form-control {
    border-left: none;
    border-radius: 0.25rem;
}

.form-control:focus {
    border-color: #007bff;
    box-shadow: 0 0 0 0.1rem rgba(0, 123, 255, 0.25);
}

/* Tombol */
.btn-primary {
    background-color: #007bff;
    border: none;
    font-size: 0.85rem;
}

.btn-primary:hover {
    background-color: #0056b3;
}

/* Teks dan Margin */
.text-muted {
    font-size: 0.75rem;
}

/* Logo */
img {
    max-width: 100%;
    height: auto;
}
</style>
