<template>
  <v-container fluid class="pt-0 pb-12">
    <v-row class="justify-space-between ma-0 pa-0" no-gutters>
      <v-col cols="12" class="desktop">
        <!-- DROP DOWN SEARCH BAR FOR XS-MD BREAKPOINTS; LEAVE ON PAGES -->
        <v-row
          class="align-center justify-center"
          v-show="
            this.$store.state.searchBar && this.$vuetify.breakpoint.xsOnly
          "
        >
          <v-col cols="12" sm="10" md="6" class="mt-1 mx-0 mb-2 px-2 py-0">
            <v-text-field solo placeholder="Search" v-model="search" hide-details></v-text-field>
          </v-col>
        </v-row>
      </v-col>
    </v-row>

    <v-row justify="center">
      <v-col cols="12" xl="11" class="mt-0 pt-0 px-5">
        <h1
          class="hidden-xs-only secondary--text text-center font-weight-bold font-italic my-2"
          style="letter-spacing: -2px; font-size: 6vmax;"
        >BingeWorthy</h1>
        <v-row
          class="align-center justify-center"
          v-show="
            this.$store.state.searchBar && this.$vuetify.breakpoint.smAndUp
          "
        >
          <v-col cols="12" md="6" lg="4" class="mt-1 mx-0 mb-2 px-2 py-0">
            <v-text-field solo placeholder="Search" v-model="search" hide-details></v-text-field>
          </v-col>
        </v-row>
        <v-row class="justify-center mt-0 mb-0 pb-0">
          <v-btn
            text
            class="hidden-md-and-up text-capitalize mx-1"
            v-show="this.search"
            @click="clearSearch"
          >Clear Search</v-btn>
          <v-btn
            large
            class="hidden-sm-and-down primary text-capitalize mb-lg-3 mx-2 scale-btn"
            v-show="this.search"
            @click="clearSearch"
          >
            <v-icon left>mdi-arrow-left</v-icon>Back / Clear
          </v-btn>
        </v-row>

        <!-- START - MOBILE - RATINGS CARDS -->
        <v-row v-if="this.$vuetify.breakpoint.smAndDown" class="justify-center my-0">
          <v-col
            cols="12"
            v-for="rating in filteredRatings"
            :key="rating.id"
            class="pa-0"
            style="position: relative;"
          >
            <v-row class="text-center justify-center align-center pt-3 pb-4 px-4 px-sm-8">
              <v-col cols="12" class="text-left d-inline-flex py-0 px-0">
                <span
                  class="title"
                  style="line-height: 1em; margin-top: 5px;"
                  @click="setSearch(rating.name)"
                >{{ rating.name }}</span>

                <v-spacer />
                <v-rating
                  :value="parseFloat(rating.rating)"
                  color="gold"
                  size="25"
                  half-icon="mdi-star-half-full"
                  half-increments
                  dense
                  readonly
                ></v-rating>
              </v-col>
              <v-col
                cols="12"
                class="accent--text d-inline-flex py-0 pl-0"
                style="font-size: 1rem; padding-right: 5px; margin-bottom: -2px;"
              >
                <EditRating :rating="rating" v-if="userId === rating.userId || userIsAdmin" />
                <span
                  @click="setSearch(rating.platform)"
                  style="padding-left: 2px;"
                >{{ rating.platform }}</span>
                <span class="secondary--text" style="margin-left: 10px;">{{ rating.date }}</span>
                <v-spacer />
                <span @click="setSearch(rating.user)">{{ rating.user }}</span>
              </v-col>
            </v-row>
            <v-divider class="primary px-0" />
          </v-col>
        </v-row>

        <!-- START RATINGS CARDS -->
        <v-row v-if="this.$vuetify.breakpoint.mdAndUp" class="justify-center mt-2 mb-6">
          <v-col cols="12" md="6" lg="4" xl="3" v-for="rating in filteredRatings" :key="rating.id">
            <v-card
              class="px-4 pt-1 ma-0 align-center d-flex"
              elevation="15"
              height="100%"
              style="box-shadow: 0 0 5px 1px #782f40 !important;"
            >
              <v-row class="text-center justify-center align-center">
                <v-row class="justify-center align-center" style="height: 80px;">
                  <v-col
                    cols="12"
                    class="display-1 py-0 mt-0"
                    style="cursor: pointer; line-height: 1em;"
                    @click="setSearch(rating.name)"
                  >{{ rating.name }}</v-col>
                </v-row>
                <v-col cols="12" class="py-0" style="margin-top: -10px;">
                  <v-rating
                    :value="parseFloat(rating.rating)"
                    half-increments
                    half-icon="mdi-star-half-full"
                    size="40"
                    readonly
                    color="gold"
                  ></v-rating>
                </v-col>
                <v-col
                  cols="12"
                  class="py-0 font-weight-medium"
                  style="cursor: pointer; font-size: 1.9em; color: #782F40;"
                  @click="setSearch(rating.platform)"
                >{{ rating.platform }}</v-col>
                <v-col
                  cols="3"
                  class="headline secondary--text font-weight-bold font-italic text-center pa-0"
                  style="position: absolute; bottom: 0; right: 0;"
                >{{ rating.date }}</v-col>
                <v-col
                  cols="12"
                  class="headline font-weight-light font-italic pt-1"
                  style="cursor: pointer;"
                  @click="setSearch(rating.user)"
                >{{ rating.user }}</v-col>
                <EditRating :rating="rating" v-if="userId === rating.userId" />
              </v-row>
            </v-card>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { db } from "@/plugins/firebase.js";
import AddRating from "@/components/AddRating";
import EditRating from "@/components/EditRating";
import AccountOptions from "@/components/AccountOptions";

export default {
  components: {
    AddRating,
    EditRating,
    AccountOptions
  },
  data() {
    return {
      search: ""
    };
  },
  methods: {
    setSearch(prop) {
      this.search = prop;
      setTimeout(() => {
        this.$vuetify.goTo(0);
      }, 50);
    },
    clearSearch() {
      this.search = "";
    },
    onLogout() {
      if (confirm("Sign Out?")) {
        this.$store.dispatch("logout");
        this.$router.push("/");
      }
    }
  },
  computed: {
    filteredRatings() {
      return this.$store.getters.recentRatings.filter(rating => {
        return (
          rating.name
            .toLowerCase()
            .replace(/[^a-zA-Z ]/g, "")
            .match(this.search.toLowerCase().replace(/[^a-zA-Z ]/g, "")) ||
          rating.platform
            .toLowerCase()
            .replace(/[^a-zA-Z ]/g, "")
            .match(this.search.toLowerCase().replace(/[^a-zA-Z ]/g, "")) ||
          rating.user
            .toLowerCase()
            .replace(/[^a-zA-Z ]/g, "")
            .match(this.search.toLowerCase().replace(/[^a-zA-Z ]/g, ""))
        );
      });
    },
    userAuth() {
      return (
        this.$store.getters.user !== null &&
        this.$store.getters.user !== undefined
      );
    },
    userId() {
      if (
        this.$store.getters.user !== null &&
        this.$store.getters.user !== undefined
      ) {
        return this.$store.getters.user.id;
      }
    },
    userIsAdmin() {
      if (
        this.$store.getters.user !== null &&
        this.$store.getters.user !== undefined
      ) {
        if (this.$store.getters.user.id === this.$store.state.admin) {
          return true;
        }
      }
    }
  },
  watch: {
    userAuth() {
      if (
        this.$store.getters.user === null &&
        this.$store.getters.user === undefined
      ) {
        this.$router.push("/");
      }
    }
  }
};
</script>
