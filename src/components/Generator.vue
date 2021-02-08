<template>
  <div>
    <v-card elevation="0">

      <v-card-text>
        <v-row>
          <v-col>
            <label>Longueur</label>
            <v-slider
              v-model="length"
              min="4"
              max="50"
            ></v-slider>
          </v-col>
          <v-col class="d-flex align-center">
            <span>{{ length }}</span>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-text>
        <v-row>
          <v-col>
            <label>Majuscules</label>
            <v-slider
              v-model="upper"
              :max="length"
              min="0"
            ></v-slider>
          </v-col>
          <v-col class="d-flex align-center">
            <span>{{ upper }}</span>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-text>
        <v-row>
          <v-col>
            <label>Chiffres</label>
            <v-slider
              v-model="digit"
              :max="length"
              min="0"
            ></v-slider>
          </v-col>
          <v-col class="d-flex align-center">
            <span>{{ digit }}</span>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-text>
        <v-row>
          <v-col>
            <label>Caractères spéciaux</label>
            <v-slider
              v-model="symbol"
              :max="length"
              min="0"
            ></v-slider>
          </v-col>
          <v-col class="d-flex align-center">
            <span>{{ symbol }}</span>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-text>
        <v-row>
          <v-col cols="11">
            <label class="mb-6">Robustesse</label>
            <v-progress-linear 
              class="progress"
              :class="{ 'poor': score <= 30, 'fair': score > 30 && score <= 60, 'good': score > 60 && score <= 80, 'excellent': score > 80 }" 
              height="10" 
              v-model="score">
            </v-progress-linear>
          </v-col>
          <v-col>
            <img v-if="score <= 30" src="../assets/poor.svg" alt="poor">
            <img v-if="score > 30 && score <= 60" src="../assets/fair.svg" alt="fair">
            <img v-if="score > 60 && score <= 80" src="../assets/good.svg" alt="good">
            <img v-if="score > 80" src="../assets/excellent.svg" alt="excellent">
          </v-col>
        </v-row>
      </v-card-text>
    
      <v-card-text>
        <v-alert color="grey lighten-4">
          <v-row align="center">
            <v-col class="grow">
              <span class="password">{{ password }}</span>
            </v-col>
            <v-col class="shrink d-flex">
              <v-tooltip top>
                <template v-slot:activator="{ on, attrs }">
                  <v-btn class="mr-2" color="primary" v-bind="attrs" v-on="on" @click="generatePassword()">
                    <v-icon dense>mdi-refresh</v-icon>
                  </v-btn>
                </template>
                <span>Regénérer</span>
              </v-tooltip>
              <v-tooltip top>
                <template v-slot:activator="{ on, attrs }">
                  <v-btn v-if="canCopy" v-bind="attrs" v-on="on" @click="copyPassword()">
                    <v-icon dense>mdi-content-copy</v-icon>
                  </v-btn>
                </template>
                <span>Copier</span>
              </v-tooltip>
            </v-col>
          </v-row>
        </v-alert>
      </v-card-text>
      
    </v-card>
  </div>
</template>

<script>
import shuffle from 'lodash/shuffle';

export default {
  name: 'Generator',
  data: () => ({
    password: '',
    score: 0,
    length: 6,
    lower: 6,
    upper: 0,
    digit: 0,
    symbol: 0,
    canCopy: false,
  }),
  mounted() {
    this.generatePassword();
    this.canCopy = !!navigator.clipboard;
  },
  watch: {
    length() {
      if (this.total < this.length) {
        this.increment()
      }
      if (this.total > this.length) {
        this.decrement();
      }
      this.generatePassword();
    },

    upper() {
      if (this.total < this.length) {
        this.increment();
      }
      if (this.total > this.length) {
        this.decrement('upper');
      }
      this.generatePassword();
    },

    digit() {
      if (this.total < this.length) {
        this.increment();
      }
      if (this.total > this.length) {
        this.decrement('digit');
      }
      this.generatePassword();
    },

    symbol() {
      if (this.total < this.length) {
        this.increment();
      }
      if (this.total > this.length) {
        this.decrement('symbol');
      }
      this.generatePassword();
    },
  },
  computed: {
    total() {
      return this.lower + this.upper + this.digit + this.symbol;
    }, 
    maxValue() {
      return [ 
        { label: 'upper', value: this.upper }, 
        { label: 'digit', value: this.digit }, 
        { label: 'symbol', value: this.symbol }
      ].sort((a, b) => {
        return b.value - a.value
      });
    }
  },
  methods: {
    increment() {
      while (this.total != this.length) {
        this.lower ++;
      }
    },
    decrement(charType = null) {
      while(this.total != this.length) {
        if (this.lower > 0) {
          this.lower--;
        }
        else {
          if (charType != this.maxValue[0].label) {
            this[this.maxValue[0].label]--;
          }
          else {
            this[this.maxValue[1].label]--;
          }
        }
      }
    },

    async copyPassword() {
      await navigator.clipboard.writeText(this.password);
    },

    setPasswordScore() {
      this.score = 0;
      // award every unique letter until 5 repetitions
      var letters = new Object();
      for (var i=0; i<this.password.length; i++) {
        letters[this.password[i]] = (letters[this.password[i]] || 0) + 1;
        this.score += 5.0 / letters[this.password[i]];
      }
      // bonus points for mixing it up
      var variations = {
        digits: /\d/.test(this.password),
        lower: /[a-z]/.test(this.password),
        upper: /[A-Z]/.test(this.password),
        nonWords: /\W/.test(this.password),
      }
      var variationCount = 0;
      for (var check in variations) {
        variationCount += (variations[check] == true) ? 1 : 0;
      }
      this.score += (variationCount - 1) * 10;
    },

    getRandomUpperCase() {
      return String.fromCharCode(Math.floor(Math.random()*26)+65);
    },

    getRandomLowerCase() {
      return String.fromCharCode(Math.floor(Math.random()*26)+97);
    },

    getRandomDigit(){
      return String.fromCharCode(Math.floor(Math.random()*10)+48);
    },

    getRandomSymbol(){
      const symbol = "!@#$%^&*(){}[]=<>/,.|~?";
      return symbol[Math.floor(Math.random()*symbol.length)];
    },

    generatePassword(){
      let characters = [];
      for (let i=0; i<this.upper; i++) {
        characters.push(this.getRandomUpperCase());
      }
      for (let i=0; i<this.lower; i++) {
        characters.push(this.getRandomLowerCase());
      }
      for (let i=0; i<this.digit; i++) {
        characters.push(this.getRandomDigit());
      }
      for (let i=0; i<this.symbol; i++) {
        characters.push(this.getRandomSymbol());
      }
      this.password = shuffle(characters).join('');
      this.setPasswordScore();
    }
  }
}
</script>

<style lang="scss">
  @import url('https://fonts.googleapis.com/css2?family=Roboto+Mono&display=swap');
  
  .password {
    font-family: 'Roboto Mono', monospace;
  }

  .progress {
    &.poor {
      .v-progress-linear__background {
        background-color: #EF5350 !important;
      }
      .v-progress-linear__determinate {
        background-color: #EF5350 !important;
      }
    }
    &.fair {
      .v-progress-linear__background {
        background-color: #FFC107 !important;
      }
      .v-progress-linear__determinate {
        background-color: #FFC107 !important;
      }
    }
    &.good {
      .v-progress-linear__background {
        background-color: #8BC34A !important;
      }
      .v-progress-linear__determinate {
        background-color: #8BC34A !important;
      }
    }
    &.excellent {
      .v-progress-linear__background {
        background-color: #4CAF50 !important;
      }
      .v-progress-linear__determinate {
        background-color: #4CAF50 !important;
      }
    }
  }
</style>