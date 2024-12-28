<template>
    <nav class="pb-0 navbar navbar-top navbar-expand navbar-dashboard navbar-dark ps-0 pe-2">
        <div class="px-0 container-fluid">
            <div class="d-flex justify-content-between w-100" id="navbarSupportedContent">
                <div class="d-flex align-items-center">
                </div>
                <!-- Navbar links -->
                <ul class="navbar-nav align-items-center">
                    <li class="nav-item dropdown ms-lg-3">
                        <!-- User Avatar and Dropdown -->
                        <a
                            class="px-0 pt-1 nav-link dropdown-toggle"
                            href="#"
                            id="userDropdown"
                            role="button"
                            data-bs-toggle="dropdown"
                            aria-expanded="false"
                        >
                            <div class="media d-flex align-items-center">
                                <img
                                    class="avatar rounded-circle"
                                    alt="User Avatar"
                                    :src="`https://ui-avatars.com/api/?name=${$page.props.auth.user.name}&background=4e73df&color=ffffff&size=100`"
                                />
                                <div
                                    class="media-body ms-2 text-dark align-items-center d-none d-lg-block"
                                >
                                    <span class="mb-0 text-gray-900 font-small fw-bold">
                                        {{ $page.props.auth.user.name }}
                                    </span>
                                </div>
                            </div>
                        </a>
                        <!-- Dropdown Menu -->
                        <ul
                            class="py-1 mt-2 border-0 shadow dropdown-menu dashboard-dropdown dropdown-menu-end"
                            aria-labelledby="userDropdown"
                        >
                            <li>
                                <Link
                                    class="dropdown-item d-flex align-items-center"
                                    href="/logout"
                                    method="POST"
                                    as="button"
                                >
                                    <svg
                                        class="dropdown-icon text-danger me-2"
                                        fill="none"
                                        stroke="currentColor"
                                        viewBox="0 0 24 24"
                                        xmlns="http://www.w3.org/2000/svg"
                                    >
                                        <path
                                            stroke-linecap="round"
                                            stroke-linejoin="round"
                                            stroke-width="2"
                                            d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"
                                        ></path>
                                    </svg>
                                    Logout
                                </Link>
                            </li>
                        </ul>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
</template>

<script>
import { Link } from "@inertiajs/vue3";

export default {
    components: {
        Link,
    },
};
</script>

<style scoped>
.avatar {
    width: 40px;
    height: 40px;
    object-fit: cover;
}
</style>
