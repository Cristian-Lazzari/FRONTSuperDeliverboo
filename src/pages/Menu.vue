<script >
  import {state} from '../state.js';
  import axios from 'axios'

  export default {
  components:{  },

    data(){
        return{     
            state,
            arrCart:[],
            totcart: 0,
            resturant_id:'',
            status:'',
            name:'',
            surname:'',
            phone:'',
            address:'',
            time:'',
            colldish:[],
            success:{},
            
        }
    },
    methods:{
      sendOrder(){
      axios
      .post(this.state.baseUrl + '/orders/store', 
        {
          resturant_id: this.resturant_id,
          status: this.status,
          name: this.name,
          surname: this.surname,
          phone: this.phone,
          address: this.address,
          time: this.time,
          arr_dish: this.colldish
        } 
      )
      .then(response => {  this.succes=response})
      },
      nwItemcl(id_dish, quantity_item) {
        let newitemcl={
          id_dish,
          quantity_item
        }
        return newitemcl;
      }
      ,
      nwItem(title, counter, tprice, price) {
        let newitem={
          title,
          counter,
          totprice: tprice,
          price: parseInt(price),
        }
        return newitem;
      },
      addUpItem(item){
        this.colldish.forEach(element => {
          if(element.id_dish === item.id_dish){
          element.quantity_item += item.quantity_item
          }
        });

      },
      addItemToArrCart(t, n, tp, id){
        if(n<1){
          console.log('ci hai provato amico')
          return 0;
        }
        let newitem= this.nwItem(t, n, tp);
        let newitemcl= this.nwItemcl(id, n);
        let checkIsSet = false;
        this.arrCart.forEach(element => {
          if(element.title == newitem.title){ checkIsSet = true }
        });

        if(checkIsSet){
          this.addUpItem(newitemcl)
          this.arrCart.forEach(element => {
            if(element.title == newitem.title){
            
            element.counter += newitem.counter;

            element.totprice += newitem.totprice
          }
          });  
        }else{
          this.arrCart.push(newitem);
          this.colldish.push(newitemcl);
        }
        this.arrCart.forEach(element => {
          console.log(element)
        });
      },
      totprice(n,p){
        let mult =  parseInt(n) * parseInt(p);
        return mult;
      },
      
      getTotCart(){
        let total=0;
        this.arrCart.forEach(element => {
          total += element.totprice
        });
        return total
      },
      deleteItemToCart(item){
        // if(counter>1){
        //   this.arrCart.forEach(element => {
        //   if(element.title == item){ 
        //     element.counter = element.counter -1
        //     element.totprice = parseInt(element.totprice) - parseInt(element.price)
        //    }
        // });
        // }else{
          let newarrCart = this.arrCart.filter((element) => {
            return element.title !== item;
          });
          this.arrCart=[];
          this.arrCart = newarrCart;
          this.arrCart.forEach(element => {
            console.log(element)
          });
      
        },
        getSingleRestaurant(id) {

        axios.get(this.state.baseUrl + '/restaurants/'+id,{})
       .then (response=>{this.state.arrMenu=response.data.restaurant})
      },
     // }
    

      
    },
    created(){
      this.getSingleRestaurant(this.state.selectedRestaurant);
      
    }
  }
</script>

<template>

 <div class="menu">
  <div class="item" v-for="(item, index) in state.arrMenu.dishes" :key="index">
    <h5>{{ item.name }}</h5>
    <span>€{{ item.price }}</span>
    
    <button @click="addItemToArrCart(item.name, 1, totprice(1, item.price), item.id)" > AGGIUNGI </button>

  </div>
 </div>
  <!-- con contatore -->
 <div class="menu">
  <div class="item" v-for="(item, index) in state.arrMenu.dishes" :key="index">
    <h5>{{ item.name }}</h5>
    <span>€{{ item.price }}</span>
     <input class="counter" type="number" :name="item.counter" :id="item.counter+item.name" v-model="item.counter" >
    <button @click="addItemToArrCart(item.name, item.counter, totprice(item.counter, item.price), item.price)" > AGGIUNGI </button>

  </div>

 </div>


<form method="post" @submit.prevent="sendOrder"  class="cart">
  <div v-for="(item, index) in arrCart" :key="index">
    <div class="row">
      <h3 name="">{{ item.title }}</h3>
      <h4>€{{ item.totprice }}</h4>
      <input v-model="item.id" type="hidden" name="id_dish"><input v-model="item.counter" type="hidden" name="quantity_item">
      <span>{{ item.counter }}</span>
      <button class="mybtn" @click="deleteItemToCart(item.title, item.counter)">ELIMINA</button>
    </div>
    
  </div>
  <div class="total">
    il totale è: €
    {{getTotCart()}}
    <input type="hidden" name="">
  </div>

  

  <input v-model="state.arrMenu.user.id" name="restaurant_id" type="hidden" >
  <input v-model="name" name="name" type="text"> <label for="">nome</label> <br>
  <input v-model="surname" name="surname" type="text"> <label for="">cognome</label> <br>
  <input v-model="phone" name="phone" type="text"> <label for="">n.telefono</label> <br>
  <input v-model="address" name="address" type="text"> <label for="">indirizzo</label> <br>
  <input v-model="totcart" name="total_price" type="text"> <label for="">prezzo finale</label> <br>
  <input v-model="time" name="time" type="text"> <label for="">orario consegna</label> <br>
  <button type="submit" class="mybtn" >
    Invio
  </button>
</form>
<div v-for="(item, index) in colldish" :key="index" >
    <div class="row">
      <h3 name="">{{ item.id_dish }}</h3> <br>
      <span>{{ item.quantity_item }}</span>
    </div>
    
  </div>
 
</template>

<style scoped lang="scss">
@use '../assets/styles/general.scss' as *;

button{
  background-color: black;
  border: 2px solid white;
  color: white;
  padding: 7px;
  border-radius: 10px
}
.menu{
  @include dfc;
  gap: 1rem;
  flex-wrap: wrap;
  margin: 2rem;
  .item{
    @include dfc;
    gap: 1rem;
    border: 2px solid rgb(214, 214, 214);
    padding: 1rem;
    width: 450px;
  }
  .cart{
    display: flex;
    flex-direction: column;
    width: 100%;
    margin: 2rem;


    
  }
}
.row{
      display: flex;
      justify-content: space-between;
      align-items: center;
      border: 2px white solid;
      padding: 1rem;
      margin-bottom: 3px;
    }
</style>
