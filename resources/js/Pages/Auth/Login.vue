<template>
    <Head>
        <title>Login - CBT</title>
    </Head>
    <div class="p-5">
        <h2 class="mb-4 text-center">Login</h2>
        <form @submit.prevent="login">
            <!-- Email Input -->
            <div class="mb-3">
                <label for="email" class="form-label">Email</label>
                <input
                    type="email"
                    id="email"
                    v-model="form.email"
                    class="form-control"
                    :class="{ 'is-invalid': errors.email }"
                    placeholder="Masukkan email Anda"
                    required
                />
                <div v-if="errors.email" class="invalid-feedback">
                    {{ errors.email }}
                </div>
            </div>

            <!-- Password Input -->
            <div class="mb-3">
                <label for="password" class="form-label">Password</label>
                <div class="input-group">
                    <input
                        :type="showPassword ? 'text' : 'password'"
                        id="password"
                        v-model="form.password"
                        class="form-control"
                        :class="{ 'is-invalid': errors.password }"
                        placeholder="Masukkan password Anda"
                        required
                    />
                    <span
                        class="input-group-text"
                        @click="togglePasswordVisibility"
                        style="cursor: pointer;"
                    >
                        <i :class="showPassword ? 'bi bi-eye-slash' : 'bi bi-eye'"></i>
                    </span>
                </div>
                <div v-if="errors.password" class="invalid-feedback">
                    {{ errors.password }}
                </div>
            </div>

            <!-- Submit Button -->
            <div class="mb-3 d-grid">
                <button type="submit" class="btn btn-primary">
                    <i class="bi bi-box-arrow-in-right"></i> Masuk
                </button>
            </div>
        </form>
        <div class="text-center">
            <small class="text-muted">UJIANKU V 1.0 by Nizam Studio &copy; 2024</small>
        </div>
    </div>
</template>
<script>
import { reactive, ref } from "vue";
import { useForm } from "@inertiajs/vue3";
import LayoutAuth from "../../Layouts/Auth.vue";

export default {
    layout: LayoutAuth,
    props: {
        errors: Object,
    },
    setup() {
        const form = reactive({
            email: "",
            password: "",
        });

        const showPassword = ref(false);

        const togglePasswordVisibility = () => {
            showPassword.value = !showPassword.value;
        };

        const login = () => {
            useForm(form).post("/login");
        };

        return {
            form,
            showPassword,
            togglePasswordVisibility,
            login,
        };
    },
};
</script>
<style scoped>
.input-group-text {
    background-color: transparent;
    border: none;
    cursor: pointer;
}

.input-group-text:hover {
    color: #007bff;
}
</style>
