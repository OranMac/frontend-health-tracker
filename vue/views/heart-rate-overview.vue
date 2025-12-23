<template id="heart-rate-overview">
    <app-layout>
        <div class="card shadow-sm border-0 mb-4">
            <div class="ibm-card-header">
                <div class="row align-items-center">
                    <div class="col">
                        <h2 class="mb-0">HeartRates</h2>
                    </div>

                    <div class="col-auto">
                        <button
                                class="btn btn-lg btn-outline-primary" title="Add HeartRate" @click="hideForm = !hideForm">
                            <i class="fa-solid fa-plus"></i>
                        </button>
                    </div>
                </div>
            </div>

            <transition name="fade">
                <div class="ibm-card-body" v-show="!hideForm">
                    <form @submit.prevent="addHeartRate">
                        <div class="mb-3">
                            <label class="form-label">Max Heart Rate</label>
                            <input
                                    type="text"
                                    class="form-control"
                                    v-model="formData.maximum"
                                    placeholder="Enter maximum"
                                    required
                            />
                        </div>

                        <div class="mb-3">
                            <label class="form-label">Bse Heart Rate</label>
                            <input
                                    type="text"
                                    class="form-control"
                                    v-model="formData.base"
                                    placeholder="Enter Base"
                                    required
                            />
                        </div>
                        <div class="mb-3">
                            <label class="form-label">Intensity</label>
                            <input
                                    type="number"
                                    class="form-control"
                                    v-model="formData.intensity"
                                    placeholder="Enter intensity"
                                    required
                            />
                        </div>
                        <div class="mb-3">
                            <label class="form-label">RunId</label>
                            <input
                                    type="number"
                                    class="form-control"
                                    v-model="formData.runId"
                                    placeholder="Enter RunId"
                                    required
                            />
                        </div>
                        <div class="d-flex gap-2">
                            <button type="submit" class="btn btn-primary btn-sm">
                                Add HeartRate
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
                    v-for="(heartRate,index) in heartRates" v-bind:key="index">
                <a :href="`/heart-rates/${heartRate.id}`" class="text-decoration-none">
                    <strong>{{ heartRate.id }}</strong>
                </a>
                <div class="d-flex gap-2">
                    <a :href="`/heart-rates/${heartRate.id}`" class="btn btn-sm btn-outline-primary">
                        <i class="fa fa-pencil"></i>
                    </a>
                    <button class="btn btn-sm btn-outline-danger" @click="deleteHeartRate(heartRate)">
                        <i class="fa fa-trash"></i>
                    </button>
                </div>
            </div>
            <div v-if="heartRates.length === 0" class="text-center">
                No heart-rates found
            </div>
        </div>
    </app-layout>
</template>

<script>
    app.component("heart-rate-overview", {
      template: "#heart-rate-overview",
      data: () => ({
        heartRates: [],
        formData: [],
        hideForm :true,
      }),
      created() {
        this.fetchHeartRates();
      },
      methods: {
        fetchHeartRates: function () {
          axios.get("/api/heart-rates")
              .then(res => this.heartRates = res.data)
              .catch(() => alert("Error while fetching heartRates"));
        },
        deleteHeartRate: function (heartRate, index) {
          if (confirm('Are you sure you want to delete this heart-rate? This action cannot be undone.', 'Warning')) {
            //heart-rate confirmed delete
            const heartRateId = heartRate.id;
            const url = `/api/heart-rates/${heartRateId}`;
            axios.delete(url)
                .then(response =>
                    //delete from the local state so Vue will reload list automatically
                    this.heartRate.splice(index, 1).push(response.data))
                .catch(function (error) {
                  console.log(error)
                });
          }
        },
        addHeartRate: function (){
          const url = `/api/heart-rates`;
          axios.post(url,
              {
                maximum: this.formData.maximum,
                base: this.formData.base,
                intensity: this.formData.intensity,
                runId: this.formData.runId
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
