<template id="track-profile">
    <app-layout>
        <div class="text-center my-5" v-if="noTrackFound">
            <h4>Track not found</h4>
            <p class="text-muted">
                We were not able to retrieve this track.
            </p>
            <a class="btn btn-outline-primary btn-sm" href="/tracks">
                Back to tracks
            </a>
        </div>

        <div class="ibm-card" v-else>
            <div class="ibm-card-header">
                <div class="row align-items-center">
                    <div class="col">
                        <h2 class="mb-0">Track Profile</h2>
                    </div>
                    <div class="col-auto d-flex gap-2">
                        <button @click="updateTrack" class="btn btn-sm btn-outline-primary" title="Save changes">
                            <i class="fa-regular fa-floppy-disk"></i>
                        </button>
                        <button
                                @click="deleteTrack" class="btn btn-sm btn-outline-danger" title="Delete track">
                            <i class="fa fa-trash"></i>
                        </button>
                    </div>
                </div>
            </div>

            <div class="ibm-card-body">
                <form @submit.prevent="updateTrack">
                    <div class="mb-3">
                        <label class="form-label">Track ID</label>
                        <input class="form-control" readonly type="number" v-model="track.id"/>
                    </div>

                    <div class="mb-3">
                        <label class="form-label">Title</label>
                        <input class="form-control" required type="text" v-model="track.title"/>
                    </div>

                    <div class="mb-3">
                        <label class="form-label">Distance</label>
                        <input class="form-control" required type="text" v-model="track.distance"/>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Difficulty</label>
                        <input class="form-control" required type="text" v-model="track.difficulty"/>
                    </div>
                </form>
            </div>

            <div class="row ibm-card-header">
                <h2 class="mb-2">Runs</h2>
                <div class="ibm-footer">
                    <p v-if="runs.length === 0" class="text-muted mb-0">
                        No runs yet.
                    </p>

                    <ul v-else class="mb-0 ps-3">
                        <li v-for="run in runs" :key="run.id">
                            <a :href="`/runs/${run.id}`" class="btn btn-sm btn-outline-primary">
                                {{ run.id }}.</a> {{ run.pace }} minutes/Km
                        </li>
                    </ul>
                </div>
            </div>
        </div>

    </app-layout>
</template>


<script>
    app.component("track-profile", {
      template: "#track-profile",
      data: () => ({
        track: null,
        noTrackFound: false,
        runs: [],
      }),
      created: function () {
        const trackId = this.$javalin.pathParams["track-id"];
        const url = `/api/tracks/${trackId}`
        axios.get(url)
            .then(res => this.track = res.data)
            .catch(error => {
              console.log("No track found for id passed in the path parameter: " + error)
              this.noTrackFound = true
            })
        axios.get(url + `/runs`)
            .then(res => this.runs = res.data)
            .catch(error => {
              console.log("No runs added yet (this is ok): " + error)
        })
      },
      methods: {
        updateTrack: function () {
          const trackId = this.$javalin.pathParams["track-id"];
          const url = `/api/tracks/${trackId}`
          axios.patch(url,
              {
                title: this.track.title,
                distance: this.track.distance,
                difficulty: this.track.difficulty
              })
              .then(response =>
                  this.track.push(response.data))
              .catch(error => {
                console.log(error)
              })
          alert("Track updated!")
        },
        deleteTrack: function () {
          if (confirm("Do you really want to delete?")) {
            const trackId = this.$javalin.pathParams["track-id"];
            const url = `/api/tracks/${trackId}`
            axios.delete(url)
                .then(response => {
                  alert("Track deleted")
                  //display the /tracks endpoint
                  window.location.href = '/tracks';
                })
                .catch(function (error) {
                  console.log(error)
                });
          }
        }
      }
    });
</script>