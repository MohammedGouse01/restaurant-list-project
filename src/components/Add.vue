<template>
   <Header/>
   <div class="h1tag"><h1>Hi! {{name}}, welcome on Add Restaurant Page</h1></div>
   <form class="add">
      <input type="text" name="name" placeholder="Enter Restaurant Name" v-model="restaurant.name" @keydown.enter.prevent="moveToContact">
      <input type="text" ref="contactInput" name="contact" placeholder="Enter Contact" v-model="restaurant.contact" @keydown.enter.prevent="moveToAddress">
      <input type="text" ref="addressInput" name="address" placeholder="Enter Address" v-model="restaurant.address" @keydown.enter.prevent="addRestaurant">
      <button type="button" v-on:click="addRestaurant">Add New Restaurant</button>
   </form>

</template>
<script>
import Header from './Header.vue'
import axios from 'axios';
export default {
     name: 'Add',
     components:{
      Header
     },
     methods:{
      moveToContact(){
         this.$refs.contactInput.focus()
      },
      moveToAddress(){
         this.$refs.addressInput.focus()
      },
      async addRestaurant(){
         const result= await axios.post('http://localhost:3000/restaurants',{
            name:this.restaurant.name,
            address:this.restaurant.address,
            contact:this.restaurant.contact,
         });
         if(result.status==201){
            this.$router.push({name:'Home'})
         }
         console.warn("result",result)
      }
     },
     data()
     {
        return {
         name:'',
         restaurant:{
            name:'',
            contact:'',
            address:''
        }
     }
     },
     mounted(){
        let user =  localStorage.getItem('user-info');
        if(user){
         this.name =JSON.parse(user).name
        }
        if(!user)
        {
           this.$router.push({name:'SignUp'}).catch(() => {})
        }
     },

}
</script>
