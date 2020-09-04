<template>
  <div class="posts">
    <h1>Cast Ballot</h1>
    <input type="radio" id="one" value="Laura Raffo (Coalición Multicolor)" v-model="picked">
    <label for="one">Laura Raffo (Coalición Multicolor)</label>
    <br>
    <input type="radio" id="two" value="Daniel Martinez (Frente Amplio)" v-model="picked">
    <label for="two">Daniel Martinez (Frente Amplio)</label>
    <br>
    <input type="radio" id="two" value="Carolina Cosse (Frente Amplio)" v-model="picked">
    <label for="two">Carolina Cosse (Frente Amplio)</label>
    <br>
    <input type="radio" id="two" value="Alvaro Villar (Frente Amplio)" v-model="picked">
    <label for="two">Alvaro Villar (Frente Amplio)</label>
    <br>
    <input type="radio" id="two" value="Eduardo Rubio (Unidad Popular)" v-model="picked">
    <label for="two">Eduardo Rubio (Unidad Popular)</label>
    <br>
    <input type="radio" id="two" value="Leonel García Laviano (Partido Verde Animalista)" v-model="picked">
    <label for="two">Leonel García Laviano (Partido Verde Animalista)</label>
    <br>
    <br>
    <span v-if="picked">
      Picked:
      <b>{{ picked }}</b>
    </span>
    <br>
    <br>
    <!--span><b>{{ response }}</b></span><br /-->
    <form v-on:submit="castBallot">
      <!-- <input type="text" value="2sww593dc034wb2twdk91r" v-model="input.electionId"  >
      <br>-->
      <input type="text" v-model="input.voterId" placeholder="Enter VoterId">
      <br>
      <input type="submit" value="Cast Vote">
      <br>
    </form>

    <br>
    <span v-if="response">
      <b>{{ response }}</b>
    </span>
    <br>
    <vue-instant-loading-spinner id="loader" ref="Spinner"></vue-instant-loading-spinner>
  </div>
</template>

<script>
import PostsService from "@/services/apiService";
import VueInstantLoadingSpinner from "vue-instant-loading-spinner/src/components/VueInstantLoadingSpinner.vue";

export default {
  name: "response",
  data() {
    return {
      input: {},
      picked: null,
      response: null
    };
  },
  components: {
    VueInstantLoadingSpinner
  },
  methods: {
    async castBallot() {
      await this.runSpinner();

      const electionRes = await PostsService.queryWithQueryString('election');

      let electionId = electionRes.data[0].Key;

      console.log("picked: ");
      console.log(this.picked);
      console.log("voterId: ");
      console.log(this.input.voterId);
      this.response = null;

 

      //error checking for making sure to vote for a valid party
      if (this.picked === null ) {
        console.log('this.picked === null')

        let response = "Debes elejir un candidado para poder emitir un voto";
        this.response = response;
        await this.hideSpinner();
      
      } else if (this.input.voterId === undefined) {
        console.log('this.voterId === undefined')

        let response = "Debes ingresar tu ID de votación para emitir un voto";
        this.response = response;
        await this.hideSpinner();

      } else {

        const apiResponse = await PostsService.castBallot(
          electionId,
          this.input.voterId,
          this.picked
        );

        console.log('apiResponse: &&&&&&&&&&&&&&&&&&&&&&&');
        console.log(apiResponse);

        if (apiResponse.data.error) {
          this.response= apiResponse.data.error;
          await this.hideSpinner();
        } else if (apiResponse.data.message) {
          this.response= `No se pudo encontrar el ID de votación ${this.input.voterId}
            `;
          await this.hideSpinner();
        } 
        else {
          let response = `Se a registrado exitosamente un votopara  ${this.picked} con el ID ${apiResponse.data.voterId}. Gracias 
           por cumplir con su deber ciudadano`;

          this.response = response;

          console.log("cast ballot");
          console.log(this.input);
          await this.hideSpinner();
        }
      }
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
