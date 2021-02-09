<template>
  <v-alert color="grey lighten-4">
    <v-row align="center">
      <v-col class="grow">
        <span class="password">{{ password }}</span>
      </v-col>
      <v-col class="shrink d-flex">
        <v-tooltip top>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              class="mr-2"
              color="primary"
              v-bind="attrs"
              v-on="on"
              @click="refresh()">
              <v-icon dense>mdi-refresh</v-icon>
            </v-btn>
          </template>
          <span>Regénérer</span>
        </v-tooltip>
        <v-tooltip top>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              v-if="canCopy"
              v-bind="attrs"
              v-on="on"
              @click="copyPassword()">
              <v-icon dense>mdi-content-copy</v-icon>
            </v-btn>
          </template>
          <span>Copier</span>
        </v-tooltip>
      </v-col>
    </v-row>
  </v-alert>
</template>

<script>

export default {
  name: 'Password',
  props: {
    password: {
      type: String,
      required: true,
    },
  },
  data: () => ({
    canCopy: false,
  }),
  mounted() {
    this.canCopy = !!navigator.clipboard;
  },
  methods: {
    async copyPassword() {
      await navigator.clipboard.writeText(this.password);
    },
    refresh() {
      this.$emit('refresh');
    },
  },
};
</script>
