<template>
    <footer class="py-3 mt-auto footer bg-light">
      <div class="text-center container-fluid">
        <span class="text-muted">
          © {{ currentYear }} <strong></strong> {{app_settings?.madrasah}} - Developed With ❤️ by <a href="#" class="text-decoration-none">Nizam Studio</a>
        </span>
        <!--<span class="mt-1 d-block">
          <a href="#" class="mx-2 text-decoration-none">Kebijakan Privasi</a> |
          <a href="#" class="mx-2 text-decoration-none">Syarat & Ketentuan</a>
        </span>-->
      </div>
    </footer>
  </template>

  <script>
  export default {
    props: {
    app_settings: {
      type: Object,
      default: () => ({}), // Default kosong jika data tidak tersedia
    },
  },
    data() {
      return {
        currentYear: new Date().getFullYear(),
      };
    },
  };
  </script>

  <style>
  .footer {
    background-color: #f8f9fa;
    color: #6c757d;
    position: relative;
    bottom: 0;
    width: 100%;
    border-top: 1px solid #e9ecef;
  }
  .footer a {
    color: #007bff;
  }
  .footer a:hover {
    text-decoration: underline;
  }
  </style>
