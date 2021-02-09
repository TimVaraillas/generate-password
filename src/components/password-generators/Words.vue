<template>
  <div>
    <v-card elevation="0">

      <v-card-text>
        <v-row>
          <v-col>
            <label>Nombre de mots</label>
            <v-slider
              v-model="words"
              :min="0"
              :max="12"
            ></v-slider>
          </v-col>
          <v-col class="d-flex align-center">
            <span>{{ words }}</span>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-text>
        <v-row>
          <v-col>
            <v-select
              v-model="language"
              :items="languages"
              item-text="label"
              item-value="value"
              label="Langue"
              dense
              outlined>
            </v-select>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-text>
        <v-row>
          <v-col>
            <v-select
              v-model="separator"
              :items="separators"
              item-text="label"
              item-value="value"
              label="Séparateur"
              dense
              outlined>
            </v-select>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-text>
        <Score :password="password" />
      </v-card-text>

      <v-card-text>
        <Password :password="password" @refresh="generatePassword()" />
      </v-card-text>

    </v-card>
  </div>
</template>

<script>
import shuffle from 'lodash/shuffle';
import dictionary from '../../dictionaries';

import Password from '../Password.vue';
import Score from '../Score.vue';

export default {
  name: 'Words',
  components: {
    Password,
    Score,
  },
  data: () => ({
    password: '',
    words: 3,
    separator: '.',
    separators: [
      { label: 'Point', value: '.' },
      { label: 'Virgule', value: ',' },
      { label: 'Espace', value: ' ' },
      { label: 'Point-virgule', value: ';' },
      { label: 'Tiret', value: '-' },
      { label: 'Tiret bas', value: '_' },
    ],
    language: 'fr',
    languages: [
      { label: 'Anglais', value: 'en' },
      { label: 'Français', value: 'fr' },
    ],

  }),
  watch: {
    words() {
      this.generatePassword();
    },
    language() {
      this.generatePassword();
    },
    separator() {
      this.generatePassword();
    },
  },
  mounted() {
    this.generatePassword();
  },
  methods: {
    generatePassword() {
      const dic = shuffle(dictionary[this.language])
        .filter((word) => word.length > 4)
        .filter((word) => {
          const format = /[ `!@#$%^&*()_+\-=[\]{};':"\\|,.<> /?~]/;
          return !format.test(word);
        })
        .map((word) => word.toLowerCase())
        .map((word) => word.normalize('NFD').replace(/[\u0300-\u036f]/g, ''));
      this.password = dic.slice(0, this.words).join(this.separator);
    },
  },
};
</script>

<style lang="scss">

</style>
