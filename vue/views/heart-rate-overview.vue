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
                                @click="hideForm = !hideForm" class="btn btn-lg btn-outline-primary"
                                title="Add HeartRate">
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
                                    class="form-control"
                                    placeholder="Enter maximum"
                                    required
                                    type="text"
                                    v-model="formData.maximum"
                            />
                        </div>

                        <div class="mb-3">
                            <label class="form-label">Bse Heart Rate</label>
                            <input
                                    class="form-control"
                                    placeholder="Enter Base"
                                    required
                                    type="text"
                                    v-model="formData.base"
                            />
                        </div>
                        <div class="mb-3">
                            <label class="form-label">Intensity</label>
                            <input
                                    class="form-control"
                                    placeholder="Enter intensity"
                                    required
                                    type="number"
                                    v-model="formData.intensity"
                            />
                        </div>
                        <div class="mb-3">
                            <label class="form-label">RunId</label>
                            <input
                                    class="form-control"
                                    placeholder="Enter RunId"
                                    required
                                    type="number"
                                    v-model="formData.runId"
                            />
                        </div>
                        <div class="d-flex gap-2">
                            <button class="btn btn-primary btn-sm" type="submit">
                                Add HeartRate
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
                    v-bind:key="index" v-for="(heartRate,index) in heartRates">
                <a :href="`/heart-rates/${heartRate.id}`" class="text-decoration-none">
                    <strong>{{ heartRate.id }}</strong>
                </a>
                <div class="d-flex gap-2">
                    <a :href="`/heart-rates/${heartRate.id}`" class="btn btn-sm btn-outline-primary">
                        <i class="fa fa-pencil"></i>
                    </a>
                    <button @click="deleteHeartRate(heartRate)" class="btn btn-sm btn-outline-danger">
                        <i class="fa fa-trash"></i>
                    </button>
                </div>
            </div>
            <div class="text-center" v-if="heartRates.length === 0">
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
