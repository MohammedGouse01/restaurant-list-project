<template>
   <Header/>
   <div class="h1tags">
   <h1>Hi! {{name}}, welcome to {{restaurant.name}} Restaurant Page</h1>
   </div>
   <div class="over-view">
   <div>
      <h2>{{restaurant.name}}</h2>
   </div>
   
   <div>
      <h3>About Us:</h3>
      <p>{{restaurant.description}}</p>
      </div>
   <div>
      <h3>Menu:</h3>
      <div class="table-like">
   <div class="row header">
      <div class="cell">Items</div>
      <div class="cell">Price</div>
   </div>
   <div class="row" v-for="(data, index) in menu" :key="index">
      <div class="cell">{{data.biryani}}</div>
      <div class="cell">{{data.price}}</div>
   </div>
</div>

      </div>
      <div>
      <h3>Address:</h3>
      <p>{{restaurant.address}}</p>
   </div>
   <div>
      <h3>Contact:</h3>
      <p>{{restaurant.contact}}</p>
   </div>
   </div>
  
</template>
<script>
import Header from './Header.vue'
import axios from 'axios'
export default {
     name: 'Menu',
     data(){
      return {
         name:'',
         restaurant:{},
         menu :[],

      }
     },
components:{
    Header
},
    async mounted(){
        let user =  localStorage.getItem('user-info');
        if(user){
         this.name = JSON.parse(user).name
        }
        if(!user)
        {
           this.$router.push({name:'SignUp'}).catch(() => {})
        }
        const result= await axios.get('http://localhost:3000/restaurant/'+this.$route.params.id)
        console.warn(result.data);
        this.restaurant = result.data
        this.menu= result.data.menu
     },
}
</script>
<style>
.over-view {
   padding-top: 10px;
    text-align: left;
    display: block;
}

.over-view div {
   margin-left: 0; /* No need for left margin when using Grid */
}
.table-like {
   display: flex;
   flex-direction: column;
   width: 30%;
}

.row {
   display: flex;
   justify-content: space-between;
   padding: 8px;
}

.header {
   background-color: #f0f0f0;
   font-weight: bold;
}

.cell {
   flex: 1;
   padding: 4px;
}

</style>