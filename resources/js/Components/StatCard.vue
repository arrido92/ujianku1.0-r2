<template>
    <div class="mb-4 col-12 col-sm-6 col-xl-3">
      <div class="border-0 shadow card">
        <div class="card-body">
          <div class="row d-block d-xl-flex align-items-center">
            <!-- Icon Section -->
            <div
              class="mb-3 col-12 col-xl-5 text-xl-center mb-xl-0 d-flex align-items-center justify-content-xl-center"
            >
              <div :class="`icon-shape icon-shape-${color} rounded me-4 me-sm-0`">
                <i :class="icon" class="icon icon-md"></i>
              </div>
            </div>

            <!-- Text Section -->
            <div class="col-12 col-xl-7 px-xl-0">
              <h2 class="h5">{{ title }}</h2>
              <h3 class="mb-1 fw-extrabold">{{ count }}</h3>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>

  <script>
  export default {
    props: {
      title: {
        type: String,
        required: true,
      },
      count: {
        type: Number,
        required: true,
      },
      icon: {
        type: String,
        required: true,
      },
      color: {
        type: String,
        default: "info", // Options: info, success, danger, tertiary
      },
    },
  };
  </script>

  <style>
  .icon-shape {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 50px;
    height: 50px;
    color: #fff;
  }

  .icon-shape-info {
    background-color: #17a2b8;
  }

  .icon-shape-success {
    background-color: #28a745;
  }

  .icon-shape-danger {
    background-color: #dc3545;
  }

  .icon-shape-tertiary {
    background-color: #6c757d;
  }

  .icon {
    font-size: 1.5rem;
  }
  </style>
