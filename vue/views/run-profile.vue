<template id="run-profile">
    <app-layout>
        <div class="text-center my-5" v-if="noRunFound">
            <h4>Run not found</h4>
            <p class="text-muted">
                We were not able to retrieve this run.
            </p>
            <a class="btn btn-outline-primary btn-sm" href="/runs">
                Back to runs
            </a>
        </div>

        <div class="ibm-card" v-else>
            <div class="ibm-card-header">
                <div class="row align-items-center">
                    <div class="col">
                        <h2 class="mb-0">Run Profile</h2>
                    </div>
                    <div class="col-auto d-flex gap-2">
                        <button @click="updateRun" class="btn btn-sm btn-outline-primary" title="Save changes">
                            <i class="fa-regular fa-floppy-disk"></i>
                        </button>
                        <button
                                @click="deleteRun" class="btn btn-sm btn-outline-danger" title="Delete run">
                            <i class="fa fa-trash"></i>
                        </button>
                    </div>
                </div>
            </div>

            <div class="ibm-card-body">
                <form @submit.prevent="updateRun">
                    <div class="mb-3">
                        <label class="form-label">Run ID</label>
                        <input class="form-control" readonly type="number" v-model="run.id"/>
                    </div>

                    <div class="mb-3">
                        <label class="form-label">TrackId</label>
                        <input class="form-control" required type="text" v-model="run.trackId"/>
                    </div>

                    <div class="mb-3">
                        <label class="form-label">Pace</label>
                        <input class="form-control" required type="email" v-model="run.pace"/>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Cadence</label>
                        <input class="form-control" required type="email" v-model="run.cadence"/>
                    </div>
                </form>
            </div>

            <div class="row ibm-card-footer">
                <h2 class="mb-2">
                    <a :href="`/tracks/${run.trackId}`" class="text-decoration-none">
                        Track</a>
                </h2>
            </div>
        </div>

    </app-layout>
</template>


<script>
    app.component("run-profile", {
      template: "#run-profile",
      data: () => ({
        run: null,
        noRunFound: false,
        tracks: [],
      }),
      created: function () {
        const runId = this.$javalin.pathParams["run-id"];
        const url = `/api/runs/${runId}`
        axios.get(url)
            .then(res => this.run = res.data)
            .catch(error => {
              console.log("No run found for id passed in the path parameter: " + error)
              this.noRunFound = true
            })
        axios.get(url + `/track`)
            .then(res => this.tracks = res.data)
            .catch(error => {
              console.log("No runs added yet (this is ok): " + error)
            })
      },
      methods: {
        updateRun: function () {
          const runId = this.$javalin.pathParams["run-id"];
          const url = `/api/runs/${runId}`
          axios.patch(url,
              {
                trackId: this.run.trackId,
                userId: this.run.userId,
                pace: this.run.pace,
                cadence: this.run.cadence
              })
              .then(response =>
                  this.run.push(response.data))
              .catch(error => {
                console.log(error)
              })
          alert("Run updated!")
        },
        deleteRun: function () {
          if (confirm("Do you really want to delete?")) {
            const runId = this.$javalin.pathParams["run-id"];
            const url = `/api/runs/${runId}`
            axios.delete(url)
                .then(response => {
                  alert("Run deleted")
                  //display the /runs endpoint
                  window.location.href = '/runs';
                })
                .catch(function (error) {
                  console.log(error)
                });
          }
        }
      }
    });
</script>