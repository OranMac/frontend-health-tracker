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
              class="btn btn-lg btn-outline-primary" title="Add Run" @click="hideForm = !hideForm">
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
              type="number"
              class="form-control"
              v-model="formData.id"
              placeholder="Enter Track id"
              required
            />
          </div>

          <div class="mb-3">
              <label class="form-label">UserId</label>
              <input
                type="number"
                class="form-control"
                v-model="formData.userId"
                placeholder="Enter User id"
                required
              />
            </div>
          <div class="mb-3">
            <label class="form-label">Pace</label>
            <input
              type="text"
              class="form-control"
              v-model="formData.number"
              placeholder="Enter Pace"
              required
            />
          </div>
          <div class="mb-3">
              <label class="form-label">Cadence</label>
              <input
                type="text"
                class="form-control"
                v-model="formData.cadence"
                placeholder="Enter Cadence"
                required
              />
          </div>
          <div class="d-flex gap-2">
            <button type="submit" class="btn btn-primary btn-sm">
              Add Run
            </button>
            <button type="button" class="btn btn-outline-secondary btn-sm"@click="hideForm = true">
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
        v-for="(run,index) in runs" v-bind:key="index">
        <a :href="`/runs/${run.id}`" class="text-decoration-none">
          <strong>{{ run.id }}</strong>
        </a>
        <div class="d-flex gap-2">
          <a :href="`/runs/${run.id}`" class="btn btn-sm btn-outline-primary">
            <i class="fa fa-pencil"></i>
          </a>
          <button class="btn btn-sm btn-outline-danger" @click="deleteRun(run)">
            <i class="fa fa-trash"></i>
          </button>
        </div>
      </div>
      <div v-if="runs.length === 0" class="text-center">
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
