<!-- the "home-page" element is passed as a parameter to VueComponent in the JavalinConfig file -->
<template id="home-page">
    <app-layout>
        <div class="row my-5">
            <!-- Users Card -->
            <div class="ibm-card">
                <div class="ibm-card-header ibm-user-color">
                    <i class="fas fa-users me-2"></i> Users
                </div>
                <div class="ibm-card-body">
                    <h2 class="card-title ibm-user-text fw-bold">{{users.length}}</h2>
                    <p class="text-muted mb-3">Total users</p>
                    <a class="ibm-card-action" href="/users">
                        <i class="fas fa-arrow-right me-1"></i>View Details
                    </a>
                </div>
            </div>

            <!-- Activities Card -->
            <div class="ibm-card">
                <div class="ibm-card-header ibm-activity-color">
                    <i class="fa-solid fa-person-walking me-2"></i>Total Activities
                </div>
                <div class="ibm-card-body">
                    <h2 class="card-title ibm-activity-text fw-bold">{{activities.length}}</h2>
                    <p class="text-muted mb-3">Recorded activities</p>
                    <a class="ibm-card-action" href="/activities">
                        <i class="fas fa-arrow-right me-1"></i>View Details
                    </a>
                </div>
            </div>

            <!-- Heart Rates Card -->
            <div class="ibm-card">
                <div class="ibm-card-header ibm-heart-rate-color">
                    <i class="fa-solid fa-heart me-2"></i>Heart Rate Records
                </div>
                <div class="ibm-card-body">
                    <h2 class="card-title ibm-heart-rate-text fw-bold">{{heartRates.length}}</h2>
                    <p class="text-muted mb-3">Total measurements</p>
                    <a class="ibm-card-action" href="/heart-rates">
                        <i class="fas fa-arrow-right me-1"></i>View Details
                    </a>
                </div>
            </div>

            <!-- Runs Card -->
            <div class="ibm-card">
                <div class="ibm-card-header ibm-run-color">
                    <i class="fa-solid fa-person-running me-2"></i>Running Sessions
                </div>
                <div class="ibm-card-body">
                    <h2 class="card-title ibm-run-text fw-bold">{{runs.length}}</h2>
                    <p class="text-muted mb-3">Completed runs</p>
                    <a class="ibm-card-action" href="/runs">
                        <i class="fas fa-arrow-right me-1"></i>View Details
                    </a>
                </div>
            </div>
        </div>

        <!-- Tracks Card -->
        <div class="row g-4">
            <div class="col-12">
                <div class="ibm-card">
                    <div class="ibm-card-header ibm-track-color">
                        <i class="fas fas-geo-alt-fill me-2"></i>Running Tracks
                    </div>
                    <div class="ibm-card-body">
                        <div class="row align-items-center">
                            <div class="col-md-8">
                                <h2 class="card-title ibm-track-text fw-bold mb-2">{{tracks.length}}</h2>
                                <p class="text-muted mb-3">Total tracks recorded</p>
                            </div>
                            <div class="col-md-4 text-md-end mt-3 mt-md-0">
                                <a class="ibm-card-action" href="/tracks">
                                    <i class="fa-solid fa-earth-europe me-1"></i>Explore Tracks
                                </a>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </app-layout>
</template>

<script>
    app.component('home-page', {
        template: "#home-page",
        data: () => ({
        users: [],
        activities: [],
        heartRates: [],
        runs: [],
        tracks: []
        }),
    created() {
        // Fetch all data when component is created
        this.fetchAllData();
        },
        methods: {
        fetchAllData() {
        axios.get("/api/users")
        .then(res => this.users = res.data)
        .catch(error => {
        console.error("Error fetching users:", error);
        alert("Error while fetching users");
        });

    axios.get("/api/activities")
        .then(res => this.activities = res.data)
        .catch(error => {
        console.error("Error fetching activities:", error);
        alert("Error while fetching activities");
        });
        axios.get("/api/heart-rates")
        .then(res => this.heartRates = res.data)
        .catch(error => {
        console.error("Error fetching heart rates:", error);
        alert("Error while fetching heart rates");
        });
    axios.get("/api/runs")
    .then(res => this.runs = res.data)
    .catch(error => {
    console.error("Error fetching runs:", error);
    alert("Error while fetching runs");
    });
    axios.get("/api/tracks")
    .then(res => this.tracks = res.data)
    .catch(error => {
    console.error("Error fetching tracks:", error);
    alert("Error while fetching tracks");
    });
    }
    }
    });
</script>