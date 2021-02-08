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
            <v-slider
              v-model="length"
              label="Longueur"
              min="4"
              max="50"
            ></v-slider>
          </v-col>
          <v-col>
            <span>{{ length }}</span>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-text>
        <v-row>
          <v-col>
            <v-slider
              label="Majuscules" 
              v-model="upper"
              :max="length"
              min="0"
            ></v-slider>
          </v-col>
          <v-col>
            <span>{{ upper }}</span>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-text>
        <v-row>
          <v-col>
            <v-slider
              label="Chiffres" 
              v-model="digit"
              :max="length"
              min="0"
            ></v-slider>
          </v-col>
          <v-col>
            <span>{{ digit }}</span>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-text>
        <v-row>
          <v-col>
            <v-slider
              label="Caractères spéciaux" 
              v-model="symbol"
              :max="length"
              min="0"
            ></v-slider>
          </v-col>
          <v-col>
            <span>{{ symbol }}</span>
          </v-col>
        </v-row>
      </v-card-text>

      <v-alert class="ma-2 mb-6" color="grey lighten-3">
        <span>{{ id }}</span>
      </v-alert>


      <v-card-actions>
        <v-btn dark large @click="generate()">
          Regénérer
        </v-btn>
      </v-card-actions>
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
    symbol: 0
  }),
  mounted() {
    this.generate();
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
