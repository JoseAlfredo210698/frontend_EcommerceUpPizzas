<template>
  <v-app>
    <v-toolbar app>
      
      <v-toolbar-title class="headline">
        <span>
          alfredito
          <strong>Pizza's</strong>
        </span>
      </v-toolbar-title>
      <v-spacer></v-spacer>

      <div class="text-xs-center">
        <v-badge overlap>
          <template v-slot:badge>
            <span>6</span>
          </template>
          <v-icon large color="grey lighten-1">shopping_cart</v-icon>
        </v-badge>

        <v-badge overlap color="red">
          <template v-slot:badge>
            <span>!</span>
          </template>
          <v-icon large color="grey">mail</v-icon>
        </v-badge>

        <v-badge color="purple" overlap >
          <template v-slot:badge>
            <v-icon dark small>done</v-icon>
          </template>
          <v-icon color="grey lighten-1" large>account_circle</v-icon>
        </v-badge>
      </div>

      <v-btn flat href="https://github.com/vuetifyjs/vuetify/releases/latest" target="_blank">
        <span class="mr-2">Registrarse</span>
      </v-btn>
    </v-toolbar>

    <v-content>
      <router-view></router-view>
    </v-content>
  </v-app>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",
  components: {
    HelloWorld
  },
  data() {
    return {
      //
    };
  }
};
</script>

<style>
</style>
