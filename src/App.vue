<template>
  <v-app>
    <v-app-bar
      app
      dark
      flat
      absolute
      shrink-on-scroll
      prominent
      src="https://picsum.photos/1920/1080?random"
      scroll-target="#scrolling-techniques-3">

      <template v-slot:img="{ props }">
        <v-img
          v-bind="props"
          gradient="to top right, rgba(100,115,201,.7), rgba(25,32,72,.7)"
        ></v-img>
      </template>

      <v-app-bar-nav-icon></v-app-bar-nav-icon>

      <v-app-bar-title class="title">Générateur de mot de passe</v-app-bar-title>

      <v-spacer></v-spacer>

      <v-btn icon>
        <v-icon>mdi-dots-vertical</v-icon>
      </v-btn>

      <template v-slot:extension>
        <v-tabs align-with-title v-model="tab">
          <v-tab>Caractères</v-tab>
          <v-tab>Mots</v-tab>
        </v-tabs>
      </template>
    </v-app-bar>

    <v-main>
      <v-container>
        <v-tabs-items v-model="tab">
          <v-tab-item>
            <characters/>
          </v-tab-item>
          <v-tab-item>
            <words/>
          </v-tab-item>
        </v-tabs-items>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Characters from './components/password-generators/Characters.vue';
import Words from './components/password-generators/Words.vue';

export default {
  name: 'App',
  components: {
    Characters,
    Words,
  },
  data: () => ({
    tab: null,
  }),
};
</script>
