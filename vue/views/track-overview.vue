<template id="track-overview">
    <app-layout>
        <div class="card shadow-sm border-0 mb-4">
            <div class="ibm-card-header">
                <div class="row align-items-center">
                    <div class="col">
                        <h2 class="mb-0">Tracks</h2>
                    </div>

                    <div class="col-auto">
                        <button
                                @click="hideForm = !hideForm" class="btn btn-lg btn-outline-primary" title="Add Track">
                            <i class="fa-solid fa-plus"></i>
                        </button>
                    </div>
                </div>
            </div>

            <transition name="fade">
                <div class="ibm-card-body" v-show="!hideForm">
                    <form @submit.prevent="addTrack">
                        <div class="mb-3">
                            <label class="form-label">TrackId</label>
                            <input
                                    class="form-control"
                                    placeholder="Enter Title"
                                    required
                                    type="text"
                                    v-model="formData.title"
                            />
                        </div>

                        <div class="mb-3">
                            <label class="form-label">UserId</label>
                            <input
                                    class="form-control"
                                    placeholder="Enter Distance"
                                    required
                                    type="text"
                                    v-model="formData.distance"
                            />
                        </div>
                        <div class="mb-3">
                            <label class="form-label">Pace</label>
                            <input
                                    class="form-control"
                                    placeholder="Enter Difficulty"
                                    required
                                    type="number"
                                    v-model="formData.number"
                            />
                        </div>
                        <div class="d-flex gap-2">
                            <button class="btn btn-primary btn-sm" type="submit">
                                Add Track
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
                    v-bind:key="index" v-for="(track,index) in tracks">
                <a :href="`/tracks/${track.id}`" class="text-decoration-none">
                    <strong>{{ track.id }}</strong>
                </a>
                <div class="d-flex gap-2">
                    <a :href="`/tracks/${track.id}`" class="btn btn-sm btn-outline-primary">
                        <i class="fa fa-pencil"></i>
                    </a>
                    <button @click="deleteTrack(track)" class="btn btn-sm btn-outline-danger">
                        <i class="fa fa-trash"></i>
                    </button>
                </div>
            </div>
            <div class="text-center" v-if="tracks.length === 0">
                No tracks found
            </div>
        </div>
    </app-layout>
</template>

<script>
    app.component("track-overview", {
      template: "#track-overview",
      data: () => ({
        tracks: [],
        formData: [],
        hideForm :true,
      }),
      created() {
        this.fetchTracks();
      },
      methods: {
        fetchTracks: function () {
          axios.get("/api/tracks")
              .then(res => this.tracks = res.data)
              .catch(() => alert("Error while fetching tracks"));
        },
        deleteTrack: function (track, index) {
          if (confirm('Are you sure you want to delete this track? This action cannot be undone.', 'Warning')) {
            //track confirmed delete
            const TrackId = track.id;
            const url = `/api/tracks/${trackId}`;
            axios.delete(url)
                .then(response =>
                    //delete from the local state so Vue will reload list automatically
                    this.tracks.splice(index, 1).push(response.data))
                .catch(function (error) {
                  console.log(error)
                });
          }
        },
        addTrack: function (){
          const url = `/api/tracks`;
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
