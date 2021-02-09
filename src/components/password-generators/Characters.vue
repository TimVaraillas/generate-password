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

import Password from '../Password.vue';
import Score from '../Score.vue';

export default {
  name: 'Characters',
  components: {
    Password,
    Score,
  },
  data: () => ({
    password: '',
    length: 6,
    lower: 6,
    upper: 0,
    digit: 0,
    symbol: 0,
  }),
  mounted() {
    this.generatePassword();
  },
  watch: {
    length() {
      if (this.total < this.length) {
        this.increment();
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
        { label: 'symbol', value: this.symbol },
      ].sort((a, b) => b.value - a.value);
    },
  },
  methods: {
    increment() {
      while (this.total !== this.length) {
        this.lower += 1;
      }
    },
    decrement(charType = null) {
      while (this.total !== this.length) {
        if (this.lower > 0) {
          this.lower -= 1;
        } else if (charType !== this.maxValue[0].label) {
          this[this.maxValue[0].label] -= 1;
        } else {
          this[this.maxValue[1].label] -= 1;
        }
      }
    },

    getRandomUpperCase() {
      return String.fromCharCode(Math.floor(Math.random() * 26) + 65);
    },

    getRandomLowerCase() {
      return String.fromCharCode(Math.floor(Math.random() * 26) + 97);
    },

    getRandomDigit() {
      return String.fromCharCode(Math.floor(Math.random() * 10) + 48);
    },

    getRandomSymbol() {
      const symbol = '!@#$%^&*(){}[]=<>/,.|~?';
      return symbol[Math.floor(Math.random() * symbol.length)];
    },

    generatePassword() {
      const characters = [];
      for (let i = 0; i < this.upper; i += 1) {
        characters.push(this.getRandomUpperCase());
      }
      for (let i = 0; i < this.lower; i += 1) {
        characters.push(this.getRandomLowerCase());
      }
      for (let i = 0; i < this.digit; i += 1) {
        characters.push(this.getRandomDigit());
      }
      for (let i = 0; i < this.symbol; i += 1) {
        characters.push(this.getRandomSymbol());
      }
      this.password = shuffle(characters).join('');
    },
  },
};
</script>
