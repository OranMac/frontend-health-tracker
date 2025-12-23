<template id="user-overview">
  <app-layout>
    <div class="card shadow-sm border-0 mb-4">
      <div class="ibm-card-header">
        <div class="row align-items-center">
          <div class="col">
            <h2 class="mb-0">Users</h2>
          </div>

          <div class="col-auto">
            <button
              class="btn btn-lg btn-outline-primary" title="Add user" @click="hideForm = !hideForm">
              <i class="fa-solid fa-plus"></i>
            </button>
          </div>
        </div>
      </div>

      <transition name="fade">
      <div class="ibm-card-body" v-show="!hideForm">
        <form @submit.prevent="addUser">
          <div class="mb-3">
            <label class="form-label">Name</label>
            <input
              type="text"
              class="form-control"
              v-model="formData.name"
              placeholder="Enter name"
              required
            />
          </div>

          <div class="mb-3">
            <label class="form-label">Email</label>
            <input
              type="email"
              class="form-control"
              v-model="formData.email"
              placeholder="Enter email"
              required
            />
          </div>

          <div class="d-flex gap-2">
            <button type="submit" class="btn btn-primary btn-sm">
              Add User
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
        v-for="(user,index) in users" v-bind:key="index">
        <a :href="`/users/${user.id}`" class="text-decoration-none">
          <strong>{{ user.name }}</strong>
          <div>{{ user.email }}</div>
        </a>
        <div class="d-flex gap-2">
          <a :href="`/users/${user.id}`" class="btn btn-sm btn-outline-primary">
            <i class="fa fa-pencil"></i>
          </a>
          <button class="btn btn-sm btn-outline-danger" @click="deleteUser(user)">
            <i class="fa fa-trash"></i>
          </button>
        </div>
      </div>
      <div v-if="users.length === 0" class="text-center">
        No users found
      </div>
    </div>
  </app-layout>
</template>

<script>
app.component("user-overview", {
  template: "#user-overview",
  data: () => ({
    users: [],
    formData: [],
    hideForm :true,
  }),
  created() {
    this.fetchUsers();
  },
  methods: {
    fetchUsers: function () {
      axios.get("/api/users")
          .then(res => this.users = res.data)
          .catch(() => alert("Error while fetching users"));
    },
    deleteUser: function (user, index) {
      if (confirm('Are you sure you want to delete this user? This action cannot be undone.', 'Warning')) {
        //user confirmed delete
        const userId = user.id;
        const url = `/api/users/${userId}`;
        axios.delete(url)
            .then(response =>
                //delete from the local state so Vue will reload list automatically
                this.users.splice(index, 1).push(response.data))
            .catch(function (error) {
              console.log(error)
            });
      }
    },
    addUser: function (){
      const url = `/api/users`;
      axios.post(url,
          {
            name: this.formData.name,
            email: this.formData.email
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