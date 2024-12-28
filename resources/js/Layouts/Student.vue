<template>
    <nav class="shadow-sm navbar navbar-expand-lg navbar-dark bg-primary">
        <div class="container">
            <!-- Logo -->
            <Link class="navbar-brand d-flex align-items-center" href="/student/dashboard">
                <img :src="`/storage/${appSettings.logo}`" alt="Logo" class="logo me-2">
                <div class="d-flex flex-column">
                    <span class="text-white fs-5 fw-bold">{{ appSettings.madrasah }}</span>
                    <small class="text-light">{{ appSettings.instansi }}</small>
                </div>
            </Link>

            <!-- Logout and Lihat Nilai Buttons -->
            <div class="gap-2 d-flex align-items-center" v-if="$page.props.auth.student && !isExamOngoing">
                <Link href="/student/grades" class="shadow btn btn-outline-light btn-sm">
                    <i class="fa fa-graduation-cap me-1"></i> NILAI
                </Link>
                <Link href="/logout" method="POST" class="shadow btn btn-outline-light btn-sm">
                    <i class="fa fa-sign-out-alt me-1"></i> LOGOUT
                </Link>
            </div>
        </div>
    </nav>

    <!-- Content -->
    <div class="container mt-4">
        <slot />
    </div>
</template>

<script>
import { Link, router } from "@inertiajs/vue3";

export default {
    components: {
        Link,
    },
    computed: {
        appSettings() {
            return this.$page.props.app_settings || {};
        },
        isExamOngoing() {
            // Periksa apakah ujian sedang berlangsung
            return this.$page.props.is_exam_ongoing || false;
        },
    },
    data() {
        return {
            inactivityTimer: null,
            isNavigating: false,
            isReloading: false,
        };
    },
    methods: {
        handleVisibilityChange() {
            if (document.hidden) {
                // Tab tidak aktif atau browser diminimalkan
                this.inactivityTimer = setTimeout(() => {
                    this.logoutTidakNormal();
                }, 30000); // 30 detik
            } else {
                // Tab aktif kembali
                clearTimeout(this.inactivityTimer);
            }
        },
        handleBeforeUnload(event) {
            // Deteksi apakah reload atau navigasi sedang berlangsung
            if (this.isReloading || this.isNavigating) {
                sessionStorage.setItem("isReloading", "true");
                return;
            }

            // Proses logout tidak normal hanya jika browser/tab ditutup
            navigator.sendBeacon("/student/logout-abnormal");
        },
        logoutTidakNormal() {
            // Panggil endpoint logout tidak normal menggunakan POST
            router.post("/student/logout-abnormal");
        },
        startNavigation() {
            this.isNavigating = true;
        },
        endNavigation() {
            this.isNavigating = false;
        },
    },
    mounted() {
        // Periksa apakah reload terjadi
        if (sessionStorage.getItem("isReloading") === "true") {
            this.isReloading = true;
            sessionStorage.removeItem("isReloading");
        }

        // Event listener untuk visibilitas tab
        document.addEventListener("visibilitychange", this.handleVisibilityChange);

        // Event listener untuk sebelum tab ditutup
        window.addEventListener("beforeunload", this.handleBeforeUnload);

        // Event untuk navigasi menggunakan Inertia
        window.addEventListener("inertia:start", this.startNavigation);
        window.addEventListener("inertia:finish", this.endNavigation);
    },
    beforeUnmount() {
        // Hapus event listener
        document.removeEventListener("visibilitychange", this.handleVisibilityChange);
        window.removeEventListener("beforeunload", this.handleBeforeUnload);
        window.removeEventListener("inertia:start", this.startNavigation);
        window.removeEventListener("inertia:finish", this.endNavigation);
    },
};
</script>

<style scoped>
/* Navbar Styles */
.bg-primary {
    background: linear-gradient(90deg, #0062cc, #004a99);
}

.navbar-brand .logo {
    height: 50px;
    max-width: 50px;
}

.navbar-brand span {
    font-size: 1.25rem;
}

.navbar-brand small {
    font-size: 0.875rem;
    opacity: 0.8;
}

/* Button Styles */
.btn-outline-light {
    color: #f8f9fa;
    border-color: rgba(255, 255, 255, 0.5);
    transition: all 0.3s ease;
}

.btn-outline-light:hover {
    background: rgba(255, 255, 255, 0.2);
    color: #ffffff;
    border-color: rgba(255, 255, 255, 0.7);
}
</style>
