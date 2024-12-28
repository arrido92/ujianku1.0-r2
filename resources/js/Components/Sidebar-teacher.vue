<template>
    <nav
      id="sidebarMenu"
      class="text-white sidebar bg-primary"
      :class="{ collapsed: isCollapsed }"
      v-if="menuItems && appSettings"
    >
      <div class="px-4 pt-3 sidebar-inner">
        <!-- Sidebar Toggle (Mobile Only) -->
        <div class="d-flex justify-content-between align-items-center d-lg-none">
          <h5 class="mb-0 text-white">Menu</h5>
          <button class="btn btn-light btn-sm" @click="toggleSidebar">
            <i :class="isCollapsed ? 'bi bi-list' : 'bi bi-x-lg'"></i>
          </button>
        </div>

        <!-- Brand Section -->
        <div class="my-4 text-center">
          <img
            v-if="appSettings.logo"
            :src="`/storage/${appSettings.logo}`"
            alt="Logo"
            class="img-fluid img-thumbnail rounded-circle"
            style="max-height: 50px; width: 50px;"
          />
          <h6 class="mt-2 fw-bold">{{ appSettings.madrasah }}</h6>
          <span class="text-light">CBT</span>
        </div>

        <!-- Navigation Links -->
        <ul class="nav flex-column">
          <li
            v-for="(item, index) in menuItems"
            :key="index"
            class="nav-item"
            :class="{ active: $page.url.startsWith(item.route) }"
          >
            <Link :href="item.route" class="nav-link d-flex align-items-center">
              <span class="sidebar-icon"><i :class="item.icon"></i></span>
              <span class="sidebar-text" v-if="!isCollapsed">{{ item.label }}</span>
            </Link>
          </li>
        </ul>
      </div>
    </nav>
  </template>
<script>
import { Link } from '@inertiajs/vue3';

export default {
  components: { Link },
  data() {
    return {
      isCollapsed: false, // State for toggling sidebar
      menuItems: [], // Initialize empty
    };
  },
  computed: {
    appSettings() {
      // Ensure appSettings is available and valid
      return this.$page.props.app_settings || {};
    },
  },
  methods: {
    toggleSidebar() {
      this.isCollapsed = !this.isCollapsed;
    },
  },
  mounted() {
    // Safeguard to load menuItems properly
    this.menuItems = [
      { label: 'Dashboard', route: '/teacher/dashboard', icon: 'bi bi-house-fill' },
      { label: 'Profile', route: '/teacher/profile', icon: 'bi bi-person-square' },
      { label: 'Daftar Siswa', route: '/teacher/students', icon: 'bi bi-people-fill' },
      { label: 'Soal Ujian', route: '/teacher/exams', icon: 'bi bi-pencil-square' },
      { label: 'Monitoring Ujian', route: '/teacher/monitoring', icon: 'bi bi-display' },
      { label: 'Login Peserta', route: '/teacher/login-peserta', icon: 'bi bi-box-arrow-in-right' },
      { label: 'Laporan Nilai', route: '/teacher/reports', icon: 'bi bi-graph-up-arrow' },

    ];
  },
};
</script>
<style scoped>
.sidebar {
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
  width: 250px;
  background-color: #007bff;
  color: #fff;
  transition: width 0.3s ease, padding 0.3s ease;
  z-index: 1050;
}

.sidebar.collapsed {
  width: 80px;
}

.sidebar .sidebar-inner {
  overflow-y: auto;
  height: 100%;
}

.sidebar .nav-link {
  color: #ffffffcc;
  padding: 10px 15px;
  font-size: 15px;
  border-radius: 4px;
}

.sidebar .nav-link:hover,
.sidebar .nav-link.active {
  background-color: #0056b3;
  color: white;
}

.sidebar .sidebar-icon {
  font-size: 20px;
  margin-right: 10px;
}

.sidebar .sidebar-text {
  font-size: 14px;
  transition: opacity 0.3s ease;
}

.sidebar.collapsed .sidebar-text {
  opacity: 0;
}

.sidebar.collapsed .sidebar-icon {
  margin-right: 0;
}

@media (max-width: 992px) {
  .sidebar {
    width: 100%;
    height: auto;
    position: relative;
  }

  .sidebar.collapsed {
    width: 100%;
  }
}
</style>
