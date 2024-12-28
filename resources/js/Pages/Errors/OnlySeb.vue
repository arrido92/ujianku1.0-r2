<template>
    <head>
      <title>Harus Menggunakan Safe Exam Browser</title>
      </head>
      <div class="vh-100 d-flex flex-column justify-content-center align-items-center bg-light">
          <h1 class="text-danger">Akses Ditolak</h1>
          <p class="text-muted">
              Halaman ini hanya dapat diakses melalui <strong>Safe Exam Browser</strong>.
          </p>
          <a href="https://safeexambrowser.org" target="_blank" class="btn btn-primary">
              Unduh Safe Exam Browser
          </a>
      </div>
  </template>

  <script>
  export default {
      name: "OnlySeb",
  };
  </script>

  <style scoped>
  .vh-100 {
      height: 100vh;
  }
  </style>
