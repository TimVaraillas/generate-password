<template>
  <v-row>
    <v-col cols="11">
      <label class="mb-6">Robustesse du mot de passe</label>
      <v-progress-linear
        class="progress"
        :class="{
          'poor': score <= 30,
          'fair': score > 30 && score <= 60,
          'good': score > 60 && score <= 80,
          'excellent': score > 80
        }"
        height="10"
        v-model="score">
      </v-progress-linear>
    </v-col>
    <v-col>
      <img v-if="score <= 30" src="../assets/images/poor.svg" alt="poor">
      <img v-if="score > 30 && score <= 60" src="../assets/images/fair.svg" alt="fair">
      <img v-if="score > 60 && score <= 80" src="../assets/images/good.svg" alt="good">
      <img v-if="score > 80" src="../assets/images/excellent.svg" alt="excellent">
    </v-col>
  </v-row>
</template>

<script>

export default {
  name: 'Score',
  props: {
    password: {
      type: String,
      required: true,
    },
  },
  data: () => ({
    score: 0,
  }),
  watch: {
    password() {
      this.setPasswordScore();
    },
  },
  mounted() {
    this.setPasswordScore();
  },
  methods: {
    setPasswordScore() {
      this.score = 0;
      // award every unique letter until 5 repetitions
      const letters = {};
      for (let i = 0; i < this.password.length; i += 1) {
        letters[this.password[i]] = (letters[this.password[i]] || 0) + 1;
        this.score += 5.0 / letters[this.password[i]];
      }
      // bonus points for mixing it up
      const variations = {
        digits: /\d/.test(this.password),
        lower: /[a-z]/.test(this.password),
        upper: /[A-Z]/.test(this.password),
        nonWords: /\W/.test(this.password),
      };
      let variationCount = 0;
      Object.keys(variations).forEach((key) => {
        variationCount += (variations[key] === true) ? 1 : 0;
      });
      this.score += (variationCount - 1) * 10;
    },
  },
};
</script>

<style scoped lang="scss">
  .progress {
    &.poor {
      ::v-deep{
        .v-progress-linear__background {
          background-color: #EF5350 !important;
        }
        .v-progress-linear__determinate {
          background-color: #EF5350 !important;
        }
      }
    }
    &.fair {
      ::v-deep{
        .v-progress-linear__background {
          background-color: #FFC107 !important;
        }
        .v-progress-linear__determinate {
          background-color: #FFC107 !important;
        }
      }
    }
    &.good {
      ::v-deep{
        .v-progress-linear__background {
          background-color: #8BC34A !important;
        }
        .v-progress-linear__determinate {
          background-color: #8BC34A !important;
        }
      }
    }
    &.excellent {
      ::v-deep{
        .v-progress-linear__background {
          background-color: #4CAF50 !important;
        }
        .v-progress-linear__determinate {
          background-color: #4CAF50 !important;
        }
      }
    }
  }
</style>
