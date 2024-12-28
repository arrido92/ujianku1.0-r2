<template>
    <nav aria-label="Pagination">
      <ul :class="`pagination pagination-sm justify-content-${align} mb-0 mt-4`">
        <!-- Looping Pagination Links -->
        <li
          v-for="(link, index) in links"
          :key="index"
          :class="[
            'page-item',
            link.url == null ? 'disabled' : '',
            link.active ? 'active' : ''
          ]"
        >
          <Link
            class="page-link"
            :href="link.url === null ? '#' : link.url"
            :aria-disabled="link.url === null"
          >
            <!-- Safely Render HTML Entities -->
            <span v-html="decodeHtml(link.label)"></span>
          </Link>
        </li>
      </ul>
    </nav>
  </template>

  <script>
  import { Link } from "@inertiajs/vue3";

  export default {
    props: {
      links: {
        type: Array,
        required: true,
      },
      align: {
        type: String,
        default: "center", // Default alignment is "center"
        validator: (value) => ["start", "center", "end"].includes(value),
      },
    },
    components: {
      Link,
    },
    methods: {
      /**
       * Decode HTML entities into readable text
       * @param {string} html
       * @returns {string}
       */
      decodeHtml(html) {
        const txt = document.createElement("textarea");
        txt.innerHTML = html;
        return txt.value;
      },
    },
  };
  </script>

  <style>
  /* Style untuk elemen yang disabled */
  .page-item.disabled .page-link {
    cursor: not-allowed;
    opacity: 0.6;
  }

  /* Style untuk elemen yang aktif */
  .page-item.active .page-link {
    background-color: #007bff;
    color: #fff;
    border-color: #007bff;
  }

  /* Hover effect */
  .page-link:hover {
    color: #007bff;
    text-decoration: none;
  }
  </style>
