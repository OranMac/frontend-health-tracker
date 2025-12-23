<template id="user-profile">
    <app-layout>
        <div class="text-center my-5" v-if="noUserFound">
            <h4>User not found</h4>
            <p class="text-muted">
                We were not able to retrieve this user.
            </p>
            <a class="btn btn-outline-primary btn-sm" href="/users">
                Back to users
            </a>
        </div>

        <div class="ibm-card" v-else>
            <div class="ibm-card-header">
                <div class="row align-items-center">
                    <div class="col">
                        <h2 class="mb-0">User Profile</h2>
                    </div>
                    <div class="col-auto d-flex gap-2">
                        <button @click="updateUser" class="btn btn-sm btn-outline-primary" title="Save changes">
                            <i class="fa-regular fa-floppy-disk"></i>
                        </button>
                        <button
                                @click="deleteUser" class="btn btn-sm btn-outline-danger" title="Delete user">
                            <i class="fa fa-trash"></i>
                        </button>
                    </div>
                </div>
            </div>

            <div class="ibm-card-body">
                <form @submit.prevent="updateUser">
                    <div class="mb-3">
                        <label class="form-label">User ID</label>
                        <input class="form-control" readonly type="number" v-model="user.id"/>
                    </div>

                    <div class="mb-3">
                        <label class="form-label">Name</label>
                        <input class="form-control" required type="text" v-model="user.name"/>
                    </div>

                    <div class="mb-3">
                        <label class="form-label">Email</label>
                        <input class="form-control" required type="email" v-model="user.email"/>
                    </div>
                </form>
            </div>

            <div class="row ibm-card-header">
                <h2 class="mb-2">Runs</h2>
                <div class="ibm-footer">
                    <p class="text-muted mb-0" v-if="runs.length === 0">
                        No runs yet.
                    </p>

                    <ul class="mb-0 ps-3" v-else>
                        <li :key="run.id" v-for="run in runs">
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
    app.component("user-profile", {
      template: "#user-profile",
      data: () => ({
        user: null,
        noUserFound: false,
        runs: [],
      }),
      created: function () {
        const userId = this.$javalin.pathParams["user-id"];
        const url = `/api/users/${userId}`
        axios.get(url)
            .then(res => this.user = res.data)
            .catch(error => {
              console.log("No user found for id passed in the path parameter: " + error)
              this.noUserFound = true
            })
        axios.get(url + `/runs`)
            .then(res => this.runs = res.data)
            .catch(error => {
              console.log("No runs added yet (this is ok): " + error)
            })
      },
      methods: {
        updateUser: function () {
          const userId = this.$javalin.pathParams["user-id"];
          const url = `/api/users/${userId}`
          axios.patch(url,
              {
                name: this.user.name,
                email: this.user.email
              })
              .then(response =>
                  this.user.push(response.data))
              .catch(error => {
                console.log(error)
              })
          alert("User updated!")
        },
        deleteUser: function () {
          if (confirm("Do you really want to delete?")) {
            const userId = this.$javalin.pathParams["user-id"];
            const url = `/api/users/${userId}`
            axios.delete(url)
                .then(response => {
                  alert("User deleted")
                  //display the /users endpoint
                  window.location.href = '/users';
                })
                .catch(function (error) {
                  console.log(error)
                });
          }
        }
      }
    });
</script>