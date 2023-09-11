<script>
import axios from 'axios';
import { state } from '../state';
import AppFilter from '../components/AppFilter.vue';
export default {
  components: {
    AppFilter,
  },
  
  data() {
    return {
        state,
        categoriesId: null,
        arrCategories: [],
        arrRestaurants: [],
        selectedCategories: [],

    }
  },
  created() {
        this.getRestaurants();
        this.getCategories();

  },
  watch: {
        selectedCategories: {
            handler: "getRestaurants",
            deep: true
        }
    },
  methods: {
    getCategories() {
        axios.get(this.state.baseUrl + '/categories',{})
        .then (response=>{this.arrCategories=response.data.categories});
    },
    getRestaurants() {
        axios.get(this.state.baseUrl + '/restaurants',{})
        .then (response=>{this.arrRestaurants=response.data.restaurants})
},
    getSingleRestaurant(id) {

          axios.get(this.state.baseUrl + '/restaurants/'+id,{})
          .then (response=>{this.state.arrMenu=response.data.restaurant})
},
    selectRestaurant(id) {
      this.state.selectedRestaurant=id
      console.log(this.state.selectedRestaurant)
      this.getSingleRestaurant(id);
    },
    manageChangeCategories(categoriesId) {
			this.categoriesId = categoriesId;
			this.getRestaurants();
		},

  },
}


</script>

<template>
  <!-- <div v-for="item in arrCategories" :key="item.id">
    <div>
      {{ item.title }}
    </div>
  </div> -->
  <AppFilter :categories="arrCategories"
  @changeCategories="manageChangeCategories($event)"
  />
  

  <div class="card-container">
        <div class="card" v-for="item in arrRestaurants" :key="item.id">
            <h1>{{ item.activity_name }}</h1>
            <p>{{ item.description }}</p>
            <div class="categories">
              <div class="category" v-for="item in item.categories">
                {{ item.title }}

              </div>
            </div>
            <div class="my-btn" @click="selectRestaurant(item.id)">
              <router-link :to="{ name: 'menu' }" class="nav-link active active-p" >vai al menù</router-link>
            </div>
         </div>
       
  </div>
  <div class="card-home">
    <h2>
    La nostra app è stata creata per offrire la comodità di ordinare cibo dalla vivace scena culinaria di Trieste direttamente a casa propria, specialmente durante la pandemia di COVID-19. Creiamo opportunità lavorative per rider e personale di supporto, semplificando l'esperienza di ordinazione e consegna con un'interfaccia user-friendly e consegne veloci, migliorando così la vita dei residenti di Trieste
  </h2>
  </div>

  <div v-for="item in arrRestaurants" :key="item.id">
    <div>
      {{ item.activity_name }}
    </div>
  </div>



   

</template>

<style scoped lang="scss">
@use '../assets/styles/general.scss' as *;
div {
  color:white;
}
.category {
  color: black;
}

.card-container{
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;

}
.card{
  background-color: white;
  border-radius: 20px;
  margin-left: 50px;
  width: 300px;
  padding: 10px;
  h1{
    color :rgb(192, 192, 43);;
  }
  p{
    color: rgb(20, 20, 104);

  }
}

.card-home{
  background-color: white;
  border-radius: 20px;
  opacity: 85%;
  height: 200px;
  padding: 10px;
}
  h2{
    color: rgb(20, 20, 104);
    margin-top: 20px;}


 //controllare in src/assets/styles/partials/variables.scss


</style>
