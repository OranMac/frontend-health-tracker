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
              class="btn btn-lg btn-outline-primary" title="Add Track" @click="hideForm = !hideForm">
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
              type="text"
              class="form-control"
              v-model="formData.title"
              placeholder="Enter Title"
              required
            />
          </div>

          <div class="mb-3">
              <label class="form-label">UserId</label>
              <input
                type="text"
                class="form-control"
                v-model="formData.distance"
                placeholder="Enter Distance"
                required
              />
            </div>
          <div class="mb-3">
            <label class="form-label">Pace</label>
            <input
              type="number"
              class="form-control"
              v-model="formData.number"
              placeholder="Enter Difficulty"
              required
            />
          </div>
          <div class="d-flex gap-2">
            <button type="submit" class="btn btn-primary btn-sm">
              Add Track
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
        v-for="(track,index) in tracks" v-bind:key="index">
        <a :href="`/tracks/${track.id}`" class="text-decoration-none">
          <strong>{{ track.id }}</strong>
        </a>
        <div class="d-flex gap-2">
          <a :href="`/tracks/${track.id}`" class="btn btn-sm btn-outline-primary">
            <i class="fa fa-pencil"></i>
          </a>
          <button class="btn btn-sm btn-outline-danger" @click="deleteTrack(track)">
            <i class="fa fa-trash"></i>
          </button>
        </div>
      </div>
      <div v-if="tracks.length === 0" class="text-center">
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
