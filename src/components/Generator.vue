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
              label="Minuscules"
              v-model="lower"
              :max="length"
              min="0"
            ></v-slider>
          </v-col>
          <v-col>
            <span>{{ lower }}</span>
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
              v-model="number"
              :max="length"
              min="0"
            ></v-slider>
          </v-col>
          <v-col>
            <span>{{ number }}</span>
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
  name: 'MathRandom',
  data: () => ({
    id: '',
    length: 16,
    upper: 4,
    lower: 4,
    number: 5,
    symbol: 4
  }),
  mounted() {
    this.generate();
  },
  watch: {
    length() {
      this.generate();
    },
    lower() {
      this.generate();
    },
    upper() {
      this.generate();
    },
    number() {
      this.generate();
    },
    symbol() {
      this.generate();
    },
  },
  methods: {
    getRandomUpperCase() {
      return String.fromCharCode(Math.floor(Math.random()*26)+65);
    },
    getRandomLowerCase() {
      return String.fromCharCode(Math.floor(Math.random()*26)+97);
    },
    getRandomNumber(){
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
      for (let i=0; i<this.number; i++) {
        characters.push(this.getRandomNumber());
      }
      for (let i=0; i<this.symbol; i++) {
        characters.push(this.getRandomSymbol());
      }
      this.id = shuffle(characters).join('');
    }
  }
}
</script>
