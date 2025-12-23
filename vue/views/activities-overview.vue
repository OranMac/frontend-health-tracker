<template id="activities-overview">
    <app-layout>
        <div>
            <h2 style="text-align: center">Activities list </h2>
            <h3>There are {{activities.length}} activities to show</h3>
            <ul>
                <li v-for="activity in activities">
                    {{activity.id}}: {{activity.description}}
                </li>
            </ul>
        </div>
    </app-layout>
</template>

<script>
    app.component("activities-overview",{
      template: "#activities-overview",
      data: () => ({
        activities: [],
        users: [],
      }),
      created() {
        axios.get(`/api/activities`)
            .then(res => this.activities = res.data)
            .catch(() => alert("Error while fetching activities"));
        axios.get(`/api/users`)
            .then(res => this.users = res.data)
            .catch(() => alert("Error while fetching activities"));
      }
    });
</script>
<style scoped>
    header span{
      font-size:3rem;
    }
</style>
