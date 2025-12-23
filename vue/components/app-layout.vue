<template id="app-layout">
  <div class="app-layout">
    <div class="container">
        <!-- Navbar -->
        <nav class="navbar navbar-expand-lg navbar-light shadow-sm sticky-top">
            <div class="container-fluid">
                <a class="navbar-brand fw-semibold" href="/">
                    Health Tracker
                </a>

                <button
                        aria-controls="navbarSupportedContent"
                        aria-expanded="false"
                        aria-label="Toggle navigation"
                        class="navbar-toggler"
                        data-bs-target="#navbarSupportedContent"
                        data-bs-toggle="collapse"
                        type="button"
                >
                    <span class="navbar-toggler-icon"></span>
                </button>

                <div class="collapse navbar-collapse" id="navbarSupportedContent">
                    <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                        <li class="nav-item">
                            <a class="nav-link" href="/users">Users</a>
                        </li>

                        <li class="nav-item">
                            <a class="nav-link" href="/activities">Activities</a>
                        </li>

                        <li class="nav-item">
                            <a class="nav-link" href="/runs">Runs</a>
                        </li>

                        <li class="nav-item">
                            <a class="nav-link" href="/tracks">Tracks</a>
                        </li>

                        <li class="nav-item">
                            <a class="nav-link" href="/heart-rates">Heart Rates</a>
                        </li>
                    </ul>

                    <div class="d-flex gap-2"></div>
                </div>
        </div>
      </nav>

        <!-- Page content slot -->
        <slot></slot>
    </div>
  </div>
</template>

<script>
    app.component("app-layout", {
      template: "#app-layout"
    });
</script>

