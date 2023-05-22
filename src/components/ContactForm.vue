<template>
  <v-form ref="form" v-model="valid" lazy-validation class="form">
       <v-card>
            <v-card-text>
                 <v-text-field v-model="name" :rules="nameRules" label="Name" required clearable></v-text-field>
                 <v-text-field v-model="lastname" :rules="nameRules" label="Last Name" required clearable></v-text-field>
                 <v-text-field prepend-icon="mdi-email" v-model="email" :rules="emailRules" label="Contact e-mail" required clearable></v-text-field>
                 <v-autocomplete
                      v-model="country"
                      @change="country !== null ? (selectedCountry = true) : (selectedCountry = false)"
                      :items="countries"
                      item-text="name"
                      item-value="alpha2Code"
                      :rules="[(v) => !!v || 'field is required']"
                      label="Purpose of the trip"
                      return-object
                      required
                      append-icon="mdi-airplane-search"
                      hide-no-data
                      :loading="loading"
                      clearable></v-autocomplete>
                 <!-- Bagin of informations about the selected country -->
                 <div v-if="selectedCountry" class="container">
                      <div class="card">
                           <img src="../assets/icons/government.png" alt="government" />
                           <div class="data">
                                <h1>{{ country.name }}</h1>
                                <span>Capital</span>
                           </div>
                      </div>
                      <div class="card">
                           <img src="../assets/icons/language.png" alt="language" />
                           <div class="data">
                                <h1 v-for="lang in country.language" :key="lang">{{ lang }}</h1>
                                <span>Language</span>
                           </div>
                      </div>
                      <div class="card">
                           <img src="../assets/icons/people.png" alt="people" />
                           <div class="data">
                                <h1>{{ country.population }}</h1>
                                <span>Population</span>
                           </div>
                      </div>
                      <div class="card">
                           <img src="../assets/icons/location.png" alt="location" />
                           <div class="data">
                                <h1>{{ country.region }}</h1>
                                <span>Region</span>
                           </div>
                      </div>
                 </div>
                 <!-- End of informations about the selected country -->
                 <v-checkbox v-model="checkbox" :rules="[(v) => !!v || 'You must agree to continue!']" label="I agree to the terms and conditions" required hide-details></v-checkbox>
            </v-card-text>
            <v-card-actions>
                 <v-btn :disabled="!valid" color="primary" @click="send" block> Send an inquiry </v-btn>
            </v-card-actions>
            <v-divider></v-divider>
            <v-btn color="info" small text @click="reset" block> reset </v-btn>
       </v-card>
  </v-form>
</template>

<script>
export default {
  name: "ContactForm",
  data: () => ({
       loading: false,
       valid: false,
       name: "",
       lastname: "",
       nameRules: [(v) => !!v || "Name is required", (v) => (v && v.length >= 3) || "Name must be more than 2 characters"],
       email: "",
       emailRules: [(v) => !!v || "E-mail is required", (v) => /.+@.+\..+/.test(v) || "E-mail must be valid"],
       country: null, //selected country
       countries: [], //list of available countries
       checkbox: false,
       selectedCountry: false,
  }),
  async created() {
       this.loading = true;
       this.countries = await this.getCountries();
       this.loading = false;
  },
  methods: {
       async getCountries() {
            let countries = [];
            let allPages = false;
            let page = 1;

            while (!allPages) {
                 await fetch("https://jsonmock.hackerrank.com/api/countries?page=" + page)
                      .then((res) => res.json())
                      .then((result) => {
                           result.data.map((country) => {
                                countries.push(country);
                           });

                           if (result.data.length === 0) {
                                allPages = true;
                           }
                           page++;
                      });
            }
            
            return countries;
       },
       send() {
            this.valid = this.validation();
            if (this.valid) alert("The application has been sent!");
       },
       reset() {
            this.$refs.form.reset();
       },
       validation() {
            return this.$refs.form.validate();
       },
  },
};
</script>
<style>
.form {
  width: 467px;
}
.container {
  padding: 10px;
  display: flex;
  flex-wrap: wrap;
}

.card {
  width: 50%;
  display: flex;
  align-items: center;
  padding: 10px;
}

.card img {
  width: 28px;
  height: 28px;
}

.data {
  margin: 10px;
  display: flex;
  flex-flow: column;
  justify-content: center;
}

.data h1 {
  font-size: 15px;
  margin: 0px;
  padding: 0px;
}

.data p {
  margin: 0px 0px 0px 0px;
}
</style>