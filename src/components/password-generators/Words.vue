<template>
  <div>
      {{ password }}
  </div>
</template>

<script>
import shuffle from 'lodash/shuffle';
import dictionary from '../../dictionaries';

export default {
  name: 'Characters',
  data: () => ({
    password: '',
    nbWords: 10,
    separator: '.',
  }),
  mounted() {
    this.generatePassword();
  },
  methods: {
    generatePassword() {
      const dic = shuffle(dictionary.fr)
        .filter((word) => word.length > 4)
        .filter((word) => {
          const format = /[ `!@#$%^&*()_+\-=[\]{};':"\\|,.<> /?~]/;
          return !format.test(word);
        })
        .map((word) => word.toLowerCase())
        .map((word) => word.normalize('NFD').replace(/[\u0300-\u036f]/g, ''));
      this.password = dic.slice(0, this.nbWords).join(this.separator);
    },
  },
};
</script>

<style lang="scss">

</style>
