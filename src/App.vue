<template>
  <v-app id="main">
    <v-container>
      <v-row class="py-4" align="center" justify="space-around">
        <h2>Account users</h2>
        <v-spacer />
        <v-spacer />
        <v-text-field
          id="search"
          solo
          class="mx-5"
          v-model="searchWord"
          hide-details
          single-line
          placeholder="Search"
          prepend-inner-icon="mdi-magnify"
        >
        </v-text-field>

        <v-btn large class="py-5" depressed color="primary">
          Connect users
        </v-btn>
      </v-row>
      <v-row>
        <v-container id="list-container">
          <template>
            <v-container>
              <v-row>
                <v-col class="py-6" cols="2" sm="2" md="2"
                  >{{ selectedUsers }} users selected
                </v-col>
                <v-col cols="3" sm="2" md="2">
                  <v-btn class="ma-2" outlined color="indigo">
                    <v-icon left> mdi-pencil</v-icon> Edit
                  </v-btn>
                </v-col>
                <v-col cols="3" sm="2" md="2"
                  ><v-btn class="ma-2" outlined color="indigo">
                    <v-icon left> mdi-delete-outline</v-icon> Delete
                  </v-btn></v-col
                >
              </v-row>
              <v-row>
                <v-col cols="6" sm="4" md="6">Users</v-col>
                <v-col class="mx-4" cols="4" sm="2" md="4"
                  >Permissions<v-icon right>mdi-arrow-down</v-icon></v-col
                >
              </v-row>
            </v-container>
          </template>
          <ListItem
            :user="item"
            :key="item.id"
            v-for="item in paginatedUsers"
            @idToDelete="onClickListItem"
            @idToSelect="onClickSelectId"
          />
        </v-container>
        <v-row align="center" justify="space-around"
          ><div class="text-center">
            <v-btn
              x-small
              class="ma-2 mx-4 py-3"
              outlined
              color="blue"
              @click="prev"
              ><v-icon center>mdi-arrow-left</v-icon></v-btn
            >
            {{ currentPage }} of {{ totalPages }}
            <v-btn
              x-small
              class="ma-2 mx-4 py-3"
              outlined
              color="blue"
              @click="next"
              ><v-icon center>mdi-arrow-right</v-icon></v-btn
            >
          </div></v-row
        >
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import ListItem from "./components/ListItem";
import axios from "axios";

export default {
  name: "App",

  components: {
    ListItem,
  },
  data() {
    return {
      selectedUsers: 0,
      searchWord: "",
      items: [],
      // basic pagination
      currentPage: 1,
      usersPerPage: 10,
    };
  },

  mounted() {
    this.getAllUsers();
  },

  computed: {
    filteredUsers: function() {
      return this.items.filter((item) => {
        return item.name.toLowerCase().match(this.searchWord.toLowerCase());
      });
    },
    paginatedUsers: function() {
      return this.filteredUsers.slice(
        (this.currentPage - 1) * this.usersPerPage,
        this.currentPage * this.usersPerPage
      );
    },

    totalPages: function() {
      return Math.ceil(this.filteredUsers.length / this.usersPerPage);
    },
  },
  methods: {
    getAllUsers() {
      axios
        .get("https://users-test-api.herokuapp.com/api/users")
        .then((response) => (this.items = response.data.users));
    },
    next() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    prev() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },

    onClickListItem(value) {
      this.items.splice(this.items.findIndex(item => item.id === value), 1) // a small hack: as heroku responds slowly to delete request, 
                                                                            //first deleting the object from array updates the dom earlier and provides better UX
      axios
        .delete(`https://users-test-api.herokuapp.com/api/users/${value}`)
        .then(() => this.getAllUsers());
      if (this.selectedUsers > 0) {
        this.selectedUsers--;
      } else {
        this.selectedUsers = 0;
      }
    },

    onClickSelectId(value) {
      if (value) {
        this.selectedUsers++;
      } else {
        if (this.selectedUsers > 0) {
          this.selectedUsers--;
        } else {
          this.selectedUsers = 0;
        }
      }
    },
  },
};
</script>
<style>
#main {
  background-color: #edf2f7;
  padding: 0 10px;
}

#list-container {
  background-color: white;
  border-radius: 15px;
}
</style>
