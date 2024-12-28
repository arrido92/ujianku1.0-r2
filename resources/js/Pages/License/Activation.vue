<template>
    <Head>
      <title>Aktivasi Lisensi - Aplikasi Ujian Online</title>
    </Head>
    <div class="d-flex justify-content-center align-items-center vh-100">
      <div class="border-0 shadow card" style="width: 700px;">
        <div class="card-body">
          <h5 class="text-center"><i class="fa fa-key"></i> Aktivasi Lisensi</h5>
          <hr />
          <div class="text-center">
            <p>
                Untuk mendapatkan <strong>Kunci Lisensi</strong>, silakan kunjungi website kami:
                <a href="https://nifatek.biz.id" target="_blank" class="text-decoration-none fw-bold">
                Lisensi Ujianku
                <i class="fa fa-external-link-alt"></i>
                </a>
            </p>
            <p class="text-danger fw-bold">
                <i class="fa fa-info-circle"></i> Note: Untuk pengguna baru, setelah mendapatkan kunci lisensi, pastikan untuk klik tombol
                <strong>Generate Storage Link</strong> terlebih dahulu sebelum melakukan aktivasi.
            </p>
            </div>
          <hr />
          <p><strong>UUID:</strong> {{ uuid }}</p>
          <p><strong>Domain:</strong> {{ domain }}</p>
          <form @submit.prevent="submit">
            <!-- Input Lisensi -->
            <div class="mb-4">
                <label for="license_key" class="form-label">Kunci Lisensi</label>
                <textarea
                    id="license_key"
                    class="form-control"
                    placeholder="Masukkan Kunci Lisensi"
                    v-model="form.license_key"
                    :disabled="loading"
                    rows="5"
                    style="resize: none;"
                ></textarea>
                <!-- Error Message -->
                <div v-if="errors.license_key" class="mt-2 alert alert-danger">
                    {{ errors.license_key }}
                </div>
                </div>

            <!-- Buttons -->
            <div class="d-flex justify-content-between">
              <button
                type="submit"
                class="border-0 shadow btn btn-md btn-primary"
                :disabled="loading"
              >
                <span v-if="loading">
                  <i class="fa fa-spinner fa-spin"></i> Memproses...
                </span>
                <span v-else>Aktivasi</span>
              </button>
              <button
                type="button"
                class="border-0 shadow btn btn-md btn-warning"
                @click="resetForm"
                :disabled="loading"
              >
                Reset
              </button>
            </div>
          </form>
          <hr />
          <!-- Generate Storage Link -->
          <button
            type="button"
            class="border-0 shadow btn btn-md btn-success w-100"
            @click="generateStorageLink"
            :disabled="loading"
          >
            Generate Storage Link
          </button>
          <!-- Error Message -->
          <div v-if="errorMessage" class="mt-3 alert alert-danger">
            {{ errorMessage }}
          </div>
          <div v-if="successMessage" class="mt-3 alert alert-success">
            {{ successMessage }}
          </div>
        </div>
      </div>
    </div>
  </template>

  <script>
  import { Head, router } from "@inertiajs/vue3";
  import { reactive, ref } from "vue";
  import Swal from "sweetalert2";

  export default {
    components: {
      Head,
    },

    props: {
      uuid: String,
      domain: String,
      errors: Object,
    },

    setup(props) {
      const form = reactive({
        license_key: "",
      });
      const loading = ref(false);
      const errorMessage = ref("");
      const successMessage = ref("");

      const submit = () => {
        if (loading.value) return;

        loading.value = true;
        router.post(
          "/license/activate",
          {
            license_key: form.license_key,
            uuid: props.uuid,
            domain: props.domain,
          },
          {
            onSuccess: () => {
              Swal.fire({
                icon: "success",
                title: "Aktivasi Berhasil!",
                text: "Lisensi berhasil diaktifkan.",
                timer: 3000,
                showConfirmButton: false,
              }).then(() => {
                router.get("/");
              });
            },
            onError: (err) => {
              errorMessage.value = err.message || "Terjadi kesalahan.";
            },
            onFinish: () => {
              loading.value = false;
            },
          }
        );
      };

      const resetForm = () => {
        form.license_key = "";
        errorMessage.value = "";
      };

      const generateStorageLink = async () => {
        if (loading.value) return;

        loading.value = true;
        errorMessage.value = "";
        successMessage.value = "";

        try {
          const response = await fetch("/generate");
          const result = await response.text();

          if (response.ok) {
            successMessage.value = result;
          } else {
            errorMessage.value = "Gagal membuat storage link.";
          }
        } catch (error) {
          errorMessage.value = "Terjadi kesalahan saat membuat storage link.";
        } finally {
          loading.value = false;
        }
      };

      return {
        form,
        loading,
        errorMessage,
        successMessage,
        submit,
        resetForm,
        generateStorageLink,
      };
    },
  };
  </script>

  <style>
  .d-flex {
    display: flex;
  }

  .vh-100 {
    min-height: 100vh;
  }

  button[disabled] {
    opacity: 0.6;
    cursor: not-allowed;
  }
  </style>
