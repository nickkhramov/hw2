<template>
  <div>
    <div class="title">
      <h1 class="title__name">Users</h1>
      <p class="title__count">{{ users.length }} users</p>
    </div>

    <div class="search">
      <button class="search__button" type="submit">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
        <path
          d="M15.5 14h-.79l-.28-.27C15.41 12.59 16 11.11 16 9.5 16 5.91 13.09 3 9.5 3S3 5.91 3 9.5 5.91 16 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19l-4.99-5zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14z">
        </path>
      </svg>
      </button>
      <input class="search__input" type="search" id="search" placeholder="Who's That Pokémon?" v-model="query">
    </div>

    <div class="list">
      <table class="list__table">
        <tr v-if="filteredUsers.length === 0">
          <td>Pokémon not found</td>
        </tr>
        <tr v-for="(user, index) in filteredUsers" :key="index" class="list__row">
          <td class="list__cell list__cell-name">
            <span @click="filterControl(user.name)">{{ user.name }}</span>
          </td>
          <td class="list__cell">
            <span>{{ user.url }}</span>
          </td>
          <td>
            <button class="list__button" @click="deleteUser(index)">Delete</button>
          </td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      API: "https://pokeapi.co/api/v2/pokemon",
      users: [],
      query: "",
    };
  },
  computed: {
    filteredUsers() {
      return this.users.filter(user =>
        user.name.toLowerCase().includes(this.query.toLowerCase())
      );
    }
  },
  methods: {
    listBuilder(list) {
      this.$refs.table.innerHTML = "";
      let template = "";
      if (!list.length) {
        template = "<tr><td>Pokémon not found</td></tr>";
      } else {
        list.forEach((element, i) => {
          template +=
            "<tr class='list__row'><td class='list__cell list__cell-name'><span>" +
            element.name +
            "</span></td>" +
            "<td class='list__cell'><span>" +
            element.url +
            "</span></td>" +
            "<td><button class='list__button' @click='deleteUser(" +
            i +
            ")'>Delete</button></td></tr>";
        });
      }
      this.$refs.table.innerHTML = template;
    },

    setLocalStorage(key, value) {
      localStorage.setItem(key, JSON.stringify(value));
    },

    getLocalStorage(key) {
      return JSON.parse(localStorage.getItem(key));
    },

    fetchUsers() {
      if (!this.getLocalStorage("data") || this.getLocalStorage("data").length === 0) {
        fetch(this.API)
          .then(response => response.json())
          .then(data => {
            this.users = data.results;
            this.setLocalStorage("data", this.users);
            this.listBuilder(this.users);
          });
      } else {
        this.users = this.getLocalStorage("data");
        this.listBuilder(this.users);
      }
    },

    filterControl(q) {
      this.query = q;
    },

    deleteUser(i) {
      this.users.splice(i, 1);
      this.setLocalStorage("data", this.users);
      this.listBuilder(this.users);
    }
  },
  mounted() {
    this.fetchUsers();
  }
};
</script>