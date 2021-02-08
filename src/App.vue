<template>
  <v-app>
    <v-app-bar app dark flat color="primary">
      <v-container>
          <v-toolbar-title>Generate password</v-toolbar-title>
      </v-container>
    </v-app-bar>

    <v-main>
      <v-container>
        <generator/>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Generator from './components/Generator';

export default {
  name: 'App',
  components: {
    Generator
  },
  data: () => ({

  }),
};
</script>
