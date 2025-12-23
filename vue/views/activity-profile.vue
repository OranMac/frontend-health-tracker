<template id="activity-profile">
  <app-layout>
  <div>
    <form v-if="activity">
      <label class="col-form-label">Activity ID: </label>
      <input class="form-control" v-model="activity.id" name="id" type="number" readonly/><br>
    </form>
  </div>
  </app-layout>
</template>
<script>
app.component("activity-profile", {
  template: "#activity-profile",
  data: () => ({
    activity: null
  }),
  created: function () {
    const activityId = this.$javalin.pathParams["activity-id"];
    const url = `/api/activities/${activityId}`
    axios.get(url)
        .then(res => this.activity = res.data)
        .catch(() => alert("Error while fetching activity" + activityId));
  }
});
</script>