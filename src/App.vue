<template>

 <router-view/>

</template>

<script>


export default {
  name: 'App',
components:{
 
}
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
body{
  padding:0;
  margin:0;
}
.add{
  margin-top: 2rem;
}
.register input, .login input, .add input{
width: 300px;
    height: 40px;
    padding-left: 20px;
    display: block;
    margin-bottom: 30px;
    margin-right: auto;
    margin-left: auto;
    border: 1px solid skyblue;
}
.register button, .login button, .add button{
    width: 320px;
    height: 40px;
    border: 1px solid skyblue;
    background: skyblue;
    color: white;
    cursor: pointer;
}
.logo{
    width:100px;
    margin-top: 2rem;
}
.h1tag {
  background-color: gray;
  /* margin-block: 0px; */
}
.h1tag h1{
  background-color: gray;
  margin-block: 0px;
}
 table {
    border-collapse: collapse;
    width: 100%;
  }
  th, td {
    border: 1px solid #ccc;
    padding: 8px;
    text-align: center;
  }
  th {
    background-color: #f2f2f2;
    position: relative;
  }
  tr:nth-child(even) {
    background-color: #f2f2f2;
  }

</style>
