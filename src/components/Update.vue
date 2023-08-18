<template>
   <Header/>
   <div class="h1tags">
   <h1>Hi! {{name}}, welcome on Update Restaurant Page</h1>
   </div>
   <form class="add">
      <input type="text" name="name" placeholder="Enter Restaurant Name" v-model="restaurant.name" @keydown.enter.prevent="moveToContact">
      <input type="text" ref="contactInput" name="contact" placeholder="Enter Contact" v-model="restaurant.contact" @keydown.enter.prevent="moveToAddress">
      <input type="text" ref="addressInput" name="address" placeholder="Enter Address" v-model="restaurant.address" @keydown.enter.prevent="updateRestaurant">
      <button type="button" v-on:click="updateRestaurant">Update Restaurant</button>
   </form>
</template>
<script>
import Header from './Header.vue'
import axios from 'axios'
export default {
     name: 'Update',
     components:{
      Header
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
     methods:{
       moveToContact(){
         this.$refs.contactInput.focus()
      },
      moveToAddress(){
         this.$refs.addressInput.focus()
      },
      async updateRestaurant(){
         const result= await axios.put('http://localhost:3000/restaurant/'+this.$route.params.id,{
            name:this.restaurant.name,
            address:this.restaurant.address,
            contact:this.restaurant.contact,
         });
         if(result.status==200){
            this.$router.push({name:'Home'})
         }
         console.warn("result",result)
      }
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
     },

}
</script>
