<template>
  <div class="posts">
    <h1>Recuento de votos</h1>
    <div id="votes">
      <div id="AlvaroVillar" ref="AlvaroVillar"></div>
      <div id="CarolinaCosse" ref="CarolinaCosse"></div>
      <div id="DanielMartinez" ref="DanielMartinez"></div>
      <div id="EduardoRubio" ref="EduardoRubio"></div>
      <div id="LeonelGarcíaLaviano" ref="LeonelGarcíaLaviano"></div>
    </div>
    <button v-on:click="getCurrentStanding()">Calcular votos</button>
    <br>
    <span v-if="response">
      <b>{{ response }}</b>
    </span>
    <br>
    <vue-instant-loading-spinner id='loader' ref="Spinner"></vue-instant-loading-spinner>
    <div class="chart-wrapper">
      <chart :options="chartOptionsBar"></chart>
    </div>
  </div>
</template>
<script>
import PostsService from "@/services/apiService";
import VueInstantLoadingSpinner from "vue-instant-loading-spinner/src/components/VueInstantLoadingSpinner.vue";
import { Bar } from "vue-chartjs";
export default {
  extends: Bar,
  name: "response",
  data() {
    return {
      response: null,
      chartOptionsBar: {}
    };
  },
  components: {
    VueInstantLoadingSpinner
  },
  methods: {
    async getCurrentStanding() {
      this.response = null;  
      this.runSpinner();
      const apiResponse = await PostsService.getCurrentStanding();
      let currentStanding = [];
      for (let i = 0; i < apiResponse.data.length; i++) {
        currentStanding[i] = apiResponse.data[i].Record.count;
      }
      this.$refs.AlvaroVillar.innerHTML = "<h3>Alvaro Villar: "+currentStanding[0]+"</h3>";
      this.$refs.CarolinaCosse.innerHTML = "<h3>Carolina Cosse: "+currentStanding[1]+"</h3>";
      this.$refs.DanielMartinez.innerHTML = "<h3>Daniel Martinez: "+currentStanding[2]+"</h3>";
      this.$refs.EduardoRubio.innerHTML = "<h3>Eduardo Rubio: "+currentStanding[3]+"</h3>";
      this.$refs.LauraRaffo.innerHTML = "<h3>Laura Raffo: "+currentStanding[4]+"</h3>";
      this.$refs.LeonelGarcíaLaviano.innerHTML = "<h3>Leonel García Laviano: "+currentStanding[5]+"</h3>";
      this.hideSpinner();
    },
    async runSpinner() {
      this.$refs.Spinner.show();
    },
    async hideSpinner() {
      this.$refs.Spinner.hide();
    }
  }
};
</script>