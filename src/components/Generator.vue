<template>
  <div>
    <v-card class="pa-6 pt-2" elevation="4">

      <v-card-title>
        Générer votre mot de passe
      </v-card-title>

      <v-divider class="mb-6"></v-divider>

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
      
      <v-alert prominent class="ma-2 mb-6" color="grey lighten-3">
        <v-row align="center">
          <v-col class="grow">
            <span class="password">{{ id }}</span>
          </v-col>
          <v-col class="shrink d-flex">
            <v-btn class="mr-2" color="primary" @click="generate()">
              Regénérer
              <v-icon dense right>mdi-refresh</v-icon>
            </v-btn>
            <v-btn v-if="canCopy" @click="copy(id)">
              Copier
              <v-icon dense right>mdi-content-copy</v-icon>
            </v-btn>
          </v-col>
        </v-row>
      </v-alert>

    </v-card>
  </div>
</template>

<script>
import shuffle from 'lodash/shuffle';

export default {
  name: 'Generator',
  data: () => ({
    id: '',
    length: 10,
    lower: 10,
    upper: 0,
    digit: 0,
    symbol: 0,
    canCopy: false,
  }),
  mounted() {
    this.generate();
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
      this.generate();
    },
    upper() {
      if (this.total < this.length) {
        this.increment();
      }
      if (this.total > this.length) {
        this.decrement('upper');
      }
      this.generate();
    },
    digit() {
      if (this.total < this.length) {
        this.increment();
      }
      if (this.total > this.length) {
        this.decrement('digit');
      }
      this.generate();
    },
    symbol() {
      if (this.total < this.length) {
        this.increment();
      }
      if (this.total > this.length) {
        this.decrement('symbol');
      }
      this.generate();
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
    async copy(str) {
      await navigator.clipboard.writeText(str);
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
    generate(){
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
      this.id = shuffle(characters).join('');
    }
  }
}
</script>

<style lang="scss">
  @import url('https://fonts.googleapis.com/css2?family=Courier+Prime&display=swap');
  
  .password {
    font-family: 'Courier Prime', monospace;
  }
</style>