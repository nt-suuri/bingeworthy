<template>
  <v-dialog v-model="dialog" max-width="400" overlay-color="black" overlay-opacity=".97">
    <template v-slot:activator="{ on }">
      <v-btn
        fab
        small
        icon
        class="hidden-sm-and-up pa-0"
        v-on="on"
        active-class="no-active"
        style="margin-right: -4px; margin-left: -4px;"
      >
        <v-icon>mdi-account-outline</v-icon>
      </v-btn>
      <v-btn
        large
        class="hidden-md-and-down primary text-capitalize ml-3 scale-btn"
        v-on="on"
        style="margin-top: 1.5px;"
      >
        <v-icon left size="20">mdi-account-outline</v-icon>Account
      </v-btn>
    </template>

    <v-card class="px-6 py-2" elevation="15">
      <v-row class="align-center justify-center">
        <v-col cols="12" class="pb-0">
          <h2
            class="display-1 secondary--text text-center font-weight-regular font-italic mt-0 pb-0"
          >Account Options</h2>
        </v-col>
        <v-card-actions style="width: 100%;">
          <v-row class="justify-center">
            <!-- DISPLAY IF USER IS NOT AUTHENTICATED -->
            <v-col cols="12" class="text-center" v-if="!userAuth">
              <v-btn
                large
                to="/signup"
                @click="dialog=false"
                active-class="no-active"
                class="primary text-capitalize mx-2 my-3 px-6"
              >
                <v-icon size="25" left>mdi-account-plus-outline</v-icon>&nbsp;Create an Account
              </v-btn>
              <v-btn
                large
                to="/signin"
                @click="dialog=false"
                active-class="no-active"
                class="primary text-capitalize mx-2 my-3 px-6"
              >
                <v-icon size="25" left>mdi-account-check-outline</v-icon>&nbsp;Sign In to Account
              </v-btn>
            </v-col>
            <!-- DISPLAY IF USER IS AUTHENTICATED -->
            <v-col cols="12" class="text-center" v-else>
              <v-btn
                large
                @click="onLogout"
                active-class="no-active"
                class="primary text-capitalize mx-2 my-3 px-5"
              >
                <v-icon size="25" left>mdi-account-remove-outline</v-icon>&nbsp;Sign Out of Account
              </v-btn>
              <v-btn
                large
                to="/updatename"
                @click="dialog=false"
                active-class="no-active"
                class="primary text-capitalize mx-2 my-3 px-7"
              >
                <v-icon size="25" left>mdi-account-cog-outline</v-icon>&nbsp;Update User Name
              </v-btn>
            </v-col>
          </v-row>
        </v-card-actions>
      </v-row>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  data() {
    return {
      dialog: false
    };
  },
  computed: {
    userAuth() {
      return (
        this.$store.getters.user !== null &&
        this.$store.getters.user !== undefined
      );
    }
  },
  methods: {
    onLogout() {
      if (confirm("Sign Out?")) {
        this.$store.dispatch("logout");
        this.$router.push("/");
        this.dialog = false;
      }
    }
  }
};
</script>
