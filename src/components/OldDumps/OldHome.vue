<template>
   <Header/>
   <div class="h1tag">
      <h1 >Hi! {{name}}, welcome on Home Page</h1>
   </div>
   <h1 >Restaurant List</h1>
   <table border="1px">
      <tr>
         <td>Id</td>
         <td>Name</td>
         <td>Contact</td>
         <td>Address</td>
         <td>Action</td>
      </tr>
      <tr v-for ="item in restaurant" :key = "item.id">
         <td>{{item.id}}</td>
         <td>{{item.name}}</td>
         <td>{{item.contact}}</td>
         <td>{{item.address}}</td>
         <td><router-link :to="'/update/'+item.id" class="icon-button">
              <i class="fas fa-pencil-alt" ></i></router-link><span> or </span>
         <button v-on:click="deleteRestaurant(item.id)" class="icon-button">
              <i class="fas fa-trash"></i>
</button>
         </td>
      </tr>
   </table>
</template>
<script>
import axios from 'axios'
import Header from '../Header.vue'
export default {
     name: 'Home',
     components:{
      Header
     },
     data(){
      return{
         name:'',
         restaurant:[],
      }
     },
     methods:{
     async deleteRestaurant(id){
         let result= await axios.delete('http://localhost:3000/restaurants/'+id);
         console.warn(result)
         if(result.status==200){
            this.loadData()
         }
      },
      async loadData(){
       let user =  localStorage.getItem('user-info');
        if(user){
         this.name =JSON.parse(user).name
        }
        if(!user)
        {
           this.$router.push({name:'SignUp'})
        }
        let result= await axios.get('http://localhost:3000/restaurants')
        this.restaurant=result.data
     }
     },
     async mounted(){
       this.loadData()
     },

}
</script>
<style>
td{
   width:160px;
   height: 40px;
}
 .icon-button {
    background: none;
    border: none;
    cursor: pointer;
    font-size: 16px;
    color: #666;
    margin: 0 5px;
    padding: 0;
    padding-left: 1rem;
    padding-right: 1rem;  }
</style>