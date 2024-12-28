<template>
    <!-- Navbar for mobile -->
    <nav class="px-4 navbar navbar-dark navbar-theme-primary col-12 d-lg-none">
      <div class="d-flex align-items-center">
        <button
          class="navbar-toggler d-lg-none collapsed"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#sidebarMenu"
          aria-controls="sidebarMenu"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
      </div>
    </nav>

    <!-- Sidebar -->
    <Sidebar />

    <!-- Main Content -->
    <main class="content">
      <!-- Navbar -->
      <Navbar />

      <!-- Dynamic Content -->
      <div class="content-wrapper">
        <slot />
      </div>
      <!-- Footer -->
      <Footer :app_settings="$page.props.app_settings" />
    </main>
  </template>

  <script>
  import Navbar from "../Components/Navbar.vue";
  import Sidebar from "../Components/Sidebar-teacher.vue";
  import Footer from "../Components/Footer.vue";

  export default {
    components: {
      Navbar,
      Sidebar,
      Footer
    },
  };
  </script>

  <style>
  /* General layout styles */
  body {
    font-family: "Roboto", sans-serif;
    background-color: #f8f9fa;
  }

  /* Sidebar */
  .sidebar {
    height: 100vh;
    position: fixed;
    top: 0;
    left: 0;
    width: 250px;
    background-color: #343a40;
    color: #fff;
    z-index: 1000;
  }

  .sidebar .nav-link {
    color: #adb5bd;
  }

  .sidebar .nav-link.active,
  .sidebar .nav-link:hover {
    color: #fff;
    background-color: #495057;
    border-radius: 4px;
  }

  /* Content */
  .content {
    margin-left: 250px;
    padding: 20px;
    transition: all 0.3s;
  }

  /* Content for smaller screens */
  @media (max-width: 992px) {
    .content {
      margin-left: 0;
    }
    .sidebar {
      display: none;
    }
    .sidebar.collapse {
      display: block;
    }
  }

  .content-wrapper {
    margin-top: 20px;
    padding: 20px;
    background-color: #ffffff;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  </style>
