<template>
  <v-list>
    <v-list-item-group multiple>
      <template>
        <v-list-item :class="{'item-selected':isSelected}"
          @mouseleave="hovered = false"
          @mouseenter="hovered = true"
          @click="idToSelect"
          v-slot:default="{ active }"
          active-class="item-selected"
        >
          <v-list-item-action>
            <v-checkbox
              v-model="isSelected"
              :input-value="active"
              color="blue accent-4"
            ></v-checkbox>
          </v-list-item-action>

          <template>
            <v-row no-gutters>
              <v-col cols="6" sm="4" md="6">
                <v-row>
                  <v-list-item-avatar>
                    <v-img :src="user.avatar"></v-img>
                  </v-list-item-avatar>

                  <v-list-item-content class="mx-3">
                    <v-list-item-title v-text="user.name"></v-list-item-title>
                    <v-list-item-subtitle
                      v-text="user.email"
                    ></v-list-item-subtitle>
                  </v-list-item-content>
                </v-row>
              </v-col>

              <v-col class="py-3" cols="3" sm="2" md="2">
                <span :class="[defaultClass, colorClass, 'my-2']">{{
                  rolePrettier(user.role)
                }}</span>
              </v-col>

              <template v-if="hovered">
                <v-col class="py-3" cols="3" sm="2" md="2">
                  <v-btn class="ma-2" small outlined color="indigo">
                    <v-icon left> mdi-pencil</v-icon> Edit
                  </v-btn>
                </v-col>
                <v-col class="py-3" cols="2" sm="2" md="1">
                  <v-btn
                    width="2"
                    class="ma-2 btn"
                    small
                    outlined
                    color="indigo"
                  >
                    <v-icon @click.stop="idToDelete" class="mx-2" left>
                      mdi-delete-outline</v-icon
                    ></v-btn
                  >
                </v-col>
              </template>
            </v-row>
          </template>
        </v-list-item>
      </template>
    </v-list-item-group>
  </v-list>
</template>

<script>
export default {
  name: "ListItem",
  props: {
    user: Object,
  },
  data() {
    return {
      defaultClass: "role-badge",
      hovered: false,
      isSelected: false,
    };
  },

  methods: {
    rolePrettier: function(role) {
      switch (role) {
        case "ADMIN":
          return "Admin";

        case "AGENT":
          return "Agent";

        case "EXTERNAL_REVIEWER":
          return "External reviewer";

        case "ACCOUNT_MANAGER":
          return "Account manager";

        default:
          return "No role";
      }
    },
    itemSelected: function(user) {
      console.log(user.name);
    },

    idToDelete() {
      this.isSelected = !this.isSelected;
      this.$emit("idToDelete", this.user.id);
    },

    idToSelect() {
      this.isSelected = !this.isSelected;
      this.$emit("idToSelect", this.isSelected);
    },
  },

  computed: {
    colorClass: function() {
      return {
        isAdmin: this.user.role === "ADMIN",
        isAgent: this.user.role === "AGENT",
        isReviewer: this.user.role == "EXTERNAL_REVIEWER",
        isManager: this.user.role === "ACCOUNT_MANAGER",
      };
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.item-selected {
  border-left: 4px solid blue;
  background: rgba(247, 250, 252, 0.01);
  border-top-left-radius: 4px;
  border-bottom-left-radius: 4px;
}

.role-badge {
  font-size: 12px;
  display: inline-block;
  font-weight: 300;
  color: black;
  cursor: default;
  background-color: teal;
  padding: 3px 8px;
  border-radius: 4px;
}

.isAdmin {
  background-color: #efe2fe;
}
.isAgent {
  background-color: #c8e7f9;
}

.isReviewer {
  background-color: #feebc8;
}

.isManager {
  background-color: #fedde6;
}
</style>
