<template>
  <v-main>
    <!-- <classifications-bar class="classifications-menu" @filterProjects="filterProjectsList($event)"></classifications-bar> -->
    <scroll-snap
      :isLoaded="isLoaded"
      ref="scrollsnap"
      @setCustomTheme="setCustomTheme($event)"
      @viewProject="getProjectForViewing($event)"
    >
      <project-tile
        v-for="item in items"
        :key="item.id"
        :item="item"
        class="item"
        v-on:click.native="viewProject(item.id)"
        :isLoaded="isLoaded"
      />
    </scroll-snap>
  </v-main>
</template>

<script
  src="https://code.jquery.com/jquery-3.6.0.min.js"
  integrity="sha256-/xUj+3OJU5yExlq6GSYGSHk7tPXikynS7ogEvDej/m4="
  crossorigin="anonymous"
></script>
<script>
// import ClassificationsBar from '../components/ClassificationsBar.vue';
import ProjectTile from "../components/ProjectTile.vue";
import ScrollSnap from "../components/ScrollSnap.vue";
import CustomThemes from "../plugins/custom_themes";
import { mapGetters } from "vuex";

export default {
  name: `Projects`,
  components: {
    // ClassificationsBar,
    ProjectTile,
    ScrollSnap,
  },
  data() {
    return {
      isLoaded: false,
      filteredItems: [],
    };
  },
  computed: {
    ...mapGetters({
      projects: "projects/getAllProjects",
    }),
    items() {
      return this.projects;
    },
  },
  created() {
    this.$store.dispatch("projects/getAllProjects").then((response) => {
      this.isLoaded = true;
    });
  },
  methods: {
    getProjectForViewing(item_index) {
      this.viewProject(this.items[item_index].id);
    },
    viewProject(item_id) {
      this.$router.push({
        name: "viewproject",
        params: {
          item_id: item_id,
          type: 1,
        },
      });
    },
    filterProjectsList(e) {
      if (e > 0) {
        let filtered = this.projects.filter((row) => row.classification == e);

        this.filteredItems = filtered;
        this.$refs.scrollsnap.init();
      } else {
        this.filteredItems = this.projects;
      }
    },
    setCustomTheme(item_index) {
      let theme = this.items[item_index].display_theme.split("--");
      let themeName = theme[0];
      let themeStyle = theme[1];
      let selectedTheme = CustomThemes[themeName][themeStyle];

      let element = document.getElementById("grdclr");

      Object.keys(selectedTheme).forEach((i) => {
        this.$vuetify.theme.themes.dark[i] = selectedTheme[i];
        this.$vuetify.theme.themes.light[i] = selectedTheme[i];
      });

      this.$vuetify.theme.dark = themeStyle == "dark";
    },
  },
};
</script>

<style scoped>
/* .classifications-menu {
  position: fixed;
  left: 1rem;
  top: 50%;
  transform: translateY(-50%);
} */
</style>
