<template>
    <section class="vh-100 d-flex justify-content-center align-items-center">
        <div class="container-fluid">
            <div class="row h-100 w-100">
                <!-- Sidebar for illustration -->
                <div class="text-white col-md-6 d-none d-md-flex align-items-center justify-content-center bg-primary">
                    <div class="text-center">
                        <img
                            :src="`/storage/${appSettings.logo}`"
                            alt="Logo"
                            class="mb-3 img-fluid"
                            style="max-width: 100px;"
                        />
                        <h2>Selamat Datang di Admin Ujian </h2>
                        <h1>{{ appSettings.madrasah }}</h1>
                        <p>Silakan masuk untuk mengelola aplikasi dengan mudah dan cepat.</p>
                    </div>
                </div>
                <!-- Slot for dynamic content -->
                <div class="col-md-6 bg-light">
                    <slot />
                </div>
            </div>
        </div>
    </section>
</template>

<script>
export default {
    computed: {
    appSettings() {
      // Ensure appSettings is available and valid
      return this.$page.props.app_settings || {};
    },
  },
    props: {
        logo: {
            type: String,
            default: "/assets/images/logo.png", // Path default untuk logo
        },
    },
};
</script>

<style scoped>
.vh-100 {
    background: linear-gradient(to right, #2b8655, #2575fc);
}

.bg-primary {
    background-color: #acaeae !important;
}

.bg-light {
    background-color: #f8f9fa !important;
}

img {
    max-width: 100%;
    height: auto;
}
</style>
