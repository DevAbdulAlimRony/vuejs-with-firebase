<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading...</p>
      <p v-else-if="!isLoading && error">
        {{ error }}
      </p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">No Data Found</p>
      <ul v-else>
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },
  data(){
    return {
      results: [],
      isLoading: false,
      error: null,
    };
  },
  methods:{
    loadExperiences(){
      this.isLoading = true;
      this.error = null;
       // get method is default, so no need to define
       //We don't have a body, so no need header also
      fetch('https://vuejs-test-4a335-default-rtdb.firebaseio.com/surveys.json').then((response) => {
        // function(response) replaced to (response) =>
        if(response.ok){
          return response.json();
        }
      }).then((data) => {
        //javascript won't wait, will get data immediately, so we reset loading here
        this.isLoading = false;
        const results = [];
        for(const id in data){
          results.push({
            id: id,
            name: data[id].name,
            rating: data[id].rating,
          });
        }
        this.results = results;
        //can use this outside for that use of arrow function
      }).catch((error) => {
        console.log(error);
        this.isLoading = false;
        this.error = 'Failed to Load Data'
      });

     //then promise after getting response and after getting data
    },
  },
  mounted(){
    this.loadExperiences();
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>