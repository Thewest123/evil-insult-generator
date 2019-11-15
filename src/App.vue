<template>
  <v-app>
    <v-content>
      <v-container fluid fill-height>
        <v-layout align-center justify-center>
          <v-flex xs12 sm8 md4>
            <v-card class="elevation-12">
              <v-toolbar color="primary" dark flat>
                <v-toolbar-title>Evil Insult Generator</v-toolbar-title>
                <v-spacer></v-spacer>
                <v-icon>mdi-heart-broken</v-icon>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-select
                    label="Language"
                    prepend-icon="mdi-translate"
                    :items="langList"
                    @change="languageChanged"
                  ></v-select>

                  <!-- Vlastní component -->
                  <insult-text :is-loading="isLoading" :is-shown="isShown" :text="insultText"></insult-text>

                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn :disabled="langSelected === ''" color="primary" @click="getInsult">Insult me!</v-btn>
              </v-card-actions>
            </v-card>
          </v-flex>

          <!-- Error message -->
          <v-snackbar v-model="snackbar">
            {{ snackbarText }}
            <v-btn color="red" text timeout="5000" @click="snackbar = false">Close</v-btn>
          </v-snackbar>

        </v-layout>
      </v-container>
    </v-content>
    <v-footer padless>
      <v-col class="text-center" cols="12">
        You've already insulted yourself
        <strong>{{insultCount}}</strong> times! —
        <strong>Jan Černý</strong>, {{ new Date().getFullYear() }}
      </v-col>
    </v-footer>
  </v-app>
</template>

<script>
import InsultText from "./components/InsultText";
import axios from "axios";

export default {
  name: "App",
  //Komponenty
  components: {
    InsultText
  },
  //Data
  data: () => ({
    langSelected: "",
    langList: [
      {
        value: "cs",
        text: "Czech"
      },
      {
        value: "de",
        text: "Deutsch"
      },
      {
        value: "en",
        text: "English"
      },
      {
        value: "es",
        text: "Spanish"
      }
    ],
    insultText: "",
    isShown: false,
    isLoading: false,
    insultCount: 0,
    snackbar: false,
    snackbarText: ""
  }),
  //Hlidat změnu, update cookie
  watch: {
    insultCount: function() {
      this.$cookies.set("insultCount", this.insultCount);
    }
  },
  //Prvotni nastaveni cookie
  created: function() {
    this.$cookies.config({ expires: "30d" });
    if (this.$cookies.isKey("insultCount")) {
      this.insultCount = this.$cookies.get("insultCount");
    } else {
      this.$cookies.set("insultCount", 0);
    }
  },
  //Metody
  methods: {
    //Odeslani pozadavku po stisku tlacitka
    getInsult: function() {
      //Zobrazeni nacitaciho skeleton-loaderu
      this.isShown = this.isLoading = true;
	  
	  //let url = `https://cors-anywhere.herokuapp.com/https://evilinsult.com/generate_insult.php?lang=${this.langSelected}&type=json`;
      let url = `https://evilinsult.com/generate_insult.php?lang=${this.langSelected}&type=json`;
      axios
        .get(url)
        .then(response => {
          //Odpoved do promenne
          this.insultText = response.data.insult;
          console.log("Response: " + this.insultText);

          //Ukonceni animace nacitani, zobrazeni odpovedi
          this.isLoading = false;
          this.insultCount++;
        })
        .catch(error => {
          this.isShown = this.isLoading = false;
          this.snackbarText = "Error: " + error.response;
          this.snackbar = true;          
          console.log("Error: " + error.response);
        });
    },
    //Zmena jazyka
    languageChanged: function(data) {
      this.langSelected = data;
      console.log("Lang: " + this.langSelected);
    }
  }
};
</script>
