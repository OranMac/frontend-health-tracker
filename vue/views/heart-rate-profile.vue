<template id="heart-rate-profile">
    <app-layout>
        <div class="text-center my-5" v-if="noHeartRateFound">
            <h4>HeartRate not found</h4>
            <p class="text-muted">
                We were not able to retrieve this heart-rate.
            </p>
            <a class="btn btn-outline-primary btn-sm" href="/heart-rates">
                Back to heart-rates
            </a>
        </div>

        <div class="ibm-card" v-else>
            <div class="ibm-card-header">
                <div class="row align-items-center">
                    <div class="col">
                        <h2 class="mb-0">HeartRate Profile</h2>
                    </div>
                    <div class="col-auto d-flex gap-2">
                        <button @click="updateHeartRate" class="btn btn-sm btn-outline-primary" title="Save changes">
                            <i class="fa-regular fa-floppy-disk"></i>
                        </button>
                        <button
                                @click="deleteHeartRate" class="btn btn-sm btn-outline-danger"
                                title="Delete heart-rate">
                            <i class="fa fa-trash"></i>
                        </button>
                    </div>
                </div>
            </div>

            <div class="ibm-card-body">
                <form @submit.prevent="updateHeartRate">
                    <div class="mb-3">
                        <label class="form-label">HeartRate ID</label>
                        <input class="form-control" readonly type="number" v-model="heartRate.id"/>
                    </div>

                    <div class="mb-3">
                        <label class="form-label">Max</label>
                        <input class="form-control" required type="text" v-model="heartRate.maximum"/>
                    </div>

                    <div class="mb-3">
                        <label class="form-label">Base</label>
                        <input class="form-control" required type="text" v-model="heartRate.base"/>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Intensity</label>
                        <input class="form-control" required type="text" v-model="heartRate.intensity"/>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Run ID</label>
                        <input class="form-control" required type="text" v-model="heartRate.runId"/>
                    </div>
                </form>
            </div>

            <div class="row ibm-card-header">
                <h2 class="mb-2">Runs</h2>
            </div>

            <div class="ibm-card-body">
                <a :href="`/runs/${heartRate.runId}`" class="text-decoration-none">
                    Run for this Heartrate</a>
            </div>
        </div>

    </app-layout>
</template>


<script>
    app.component("heart-rate-profile", {
      template: "#heart-rate-profile",
      data: () => ({
        heartRate: null,
        noHeartRateFound: false,
        runs: [],
      }),
      created: function () {
        const heartRateId = this.$javalin.pathParams["heart-rate-id"];
        const url = `/api/heart-rates/${heartRateId}`
        axios.get(url)
            .then(res => this.heartRate = res.data)
            .catch(error => {
              console.log("No heart-rate found for id passed in the path parameter: " + error)
              this.noHeartRateFound = true
            })
        axios.get(url + `/runs`)
            .then(res => this.runs = res.data)
            .catch(error => {
              console.log("No runs added yet (this is ok): " + error)
        })
      },
      methods: {
        updateHeartRate: function () {
          const heartRateId = this.$javalin.pathParams["heart-rate-id"];
          const url = `/api/heart-rates/${heartRateId}`
          axios.patch(url,
              {
                maximum: this.heartRate.maximum,
                base: this.heartRate.base,
                intensity: this.heartRate.intensity,
                runId: this.heartRate.runId
              })
              .then(response =>
                  this.heartRate.push(response.data))
              .catch(error => {
                console.log(error)
              })
          alert("HeartRate updated!")
        },
        deleteHeartRate: function () {
          if (confirm("Do you really want to delete?")) {
            const heartRateId = this.$javalin.pathParams["heart-rate-id"];
            const url = `/api/heart-rates/${heartRateId}`
            axios.delete(url)
                .then(response => {
                  alert("HeartRate deleted")
                  //display the /heart-rates endpoint
                  window.location.href = '/heart-rates';
                })
                .catch(function (error) {
                  console.log(error)
                });
          }
        }
      }
    });
</script>