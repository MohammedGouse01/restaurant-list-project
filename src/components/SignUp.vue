<template>
 <img class="logo" src="..\assets\resto_logo.jpg">
   <h1>Sign Up</h1> 
   <div class= "register">
    <input type="text" v-model="name" placeholder="Enter Name" @keydown.enter.prevent="moveToEmail">
    <input type="text" ref="emailInput" v-model="email" placeholder="Enter Email" @keydown.enter.prevent="moveToPassword">
    <input type="password" ref="passwordInput" v-model="password" placeholder="Enter Password" @keydown.enter.prevent="signUp">
    <button v-on:click="signUp">Sign Up</button>
    <p><router-link to="/login">login</router-link></p>
   </div>
</template>
<script>
import axios from 'axios'

export default {
     name:'SignUp',
     data()
     {
        return {
            name:'',
            email:'',
            password:''
        }
     },
     methods:{
         moveToPassword() {
            
            this.$refs.passwordInput.focus();
        }, moveToEmail() {
            
            this.$refs.emailInput.focus();
        },
        async signUp(){
            let result = await axios.post("http://localhost:3000/users",{
                email:this.email,
                password:this.password,
                name:this.name,
            });
            console.warn(result)
            if(result.status==201){
                localStorage.setItem("user-info",JSON.stringify(result.data));
                this.$router.push({name:'Home'})
            }
        }
     },
     mounted(){
        let user= localStorage.getItem('user-info');
        if(user)
        {
             this.$router.push({name:'Home'})
            
        }
     }
}
</script>
<style>

</style>
