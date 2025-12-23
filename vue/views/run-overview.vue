<template id="run-overview">
    <app-layout>
        <div class="card shadow-sm border-0 mb-4">
            <div class="ibm-card-header">
                <div class="row align-items-center">
                    <div class="col">
                        <h2 class="mb-0">Runs</h2>
                    </div>

                    <div class="col-auto">
                        <button
                                @click="hideForm = !hideForm" class="btn btn-lg btn-outline-primary" title="Add Run">
                            <i class="fa-solid fa-plus"></i>
                        </button>
                    </div>
                </div>
            </div>

            <transition name="fade">
                <div class="ibm-card-body" v-show="!hideForm">
                    <form @submit.prevent="addRun">
                        <div class="mb-3">
                            <label class="form-label">TrackId</label>
                            <input
                                    class="form-control"
                                    placeholder="Enter Track id"
                                    required
                                    type="number"
                                    v-model="formData.id"
                            />
                        </div>

                        <div class="mb-3">
                            <label class="form-label">UserId</label>
                            <input
                                    class="form-control"
                                    placeholder="Enter User id"
                                    required
                                    type="number"
                                    v-model="formData.userId"
                            />
                        </div>
                        <div class="mb-3">
                            <label class="form-label">Pace</label>
                            <input
                                    class="form-control"
                                    placeholder="Enter Pace"
                                    required
                                    type="text"
                                    v-model="formData.number"
                            />
                        </div>
                        <div class="mb-3">
                            <label class="form-label">Cadence</label>
                            <input
                                    class="form-control"
                                    placeholder="Enter Cadence"
                                    required
                                    type="text"
                                    v-model="formData.cadence"
                            />
                        </div>
                        <div class="d-flex gap-2">
                            <button class="btn btn-primary btn-sm" type="submit">
                                Add Run
                            </button>
                            <button @click="hideForm = true" class="btn btn-outline-secondary btn-sm" type="button">
                                Cancel
                            </button>
                        </div>
                    </form>
                </div>
            </transition>
        </div>
        <div class="list-group list-group-flush">
            <div
                    class="list-group-item d-flex align-items-start justify-content-between"
                    v-bind:key="index" v-for="(run,index) in runs">
                <a :href="`/runs/${run.id}`" class="text-decoration-none">
                    <strong>{{ run.id }}</strong>
                    <div>Track: {{ run.trackId }}</div>
                </a>
                <div class="d-flex gap-2">
                    <a :href="`/runs/${run.id}`" class="btn btn-sm btn-outline-primary">
                        <i class="fa fa-pencil"></i>
                    </a>
                    <button @click="deleteRun(run)" class="btn btn-sm btn-outline-danger">
                        <i class="fa fa-trash"></i>
                    </button>
                </div>
            </div>
            <div class="text-center" v-if="runs.length === 0">
                No runs found
            </div>
        </div>
    </app-layout>
</template>

<script>
    app.component("run-overview", {
      template: "#run-overview",
      data: () => ({
        runs: [],
        formData: [],
        hideForm :true,
      }),
      created() {
        this.fetchRuns();
      },
      methods: {
        fetchRuns: function () {
          axios.get("/api/runs")
              .then(res => this.runs = res.data)
              .catch(() => alert("Error while fetching runs"));
        },
        deleteRun: function (run, index) {
          if (confirm('Are you sure you want to delete this run? This action cannot be undone.', 'Warning')) {
            //run confirmed delete
            const RunId = run.id;
            const url = `/api/runs/${runId}`;
            axios.delete(url)
                .then(response =>
                    //delete from the local state so Vue will reload list automatically
                    this.runs.splice(index, 1).push(response.data))
                .catch(function (error) {
                  console.log(error)
                });
          }
        },
        addRun: function (){
          const url = `/api/runs`;
          axios.post(url,
              {
                trackId: this.formData.id,
                userId: this.formData.userId,
                pace: this.formData.number,
                cadence: this.formData.cadence
              })
              .then(response => {
                this.users.push(response.data)
                this.hideForm= true;
              })
              .catch(error => {
                console.log(error)
              })
        }
      }
    });
</script>
