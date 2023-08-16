<template>
  <div>
    <Header/>
    <div class="h1tag">
      <h1>Hi! {{ name }}, welcome on Home Page</h1>
    </div>
    <h1>Restaurant List</h1>
   
    <sort-list v-if="restaurant" :restaurant="restaurant" :deleteRestaurant="deleteRestaurant"/>
  </div>
</template>

<script>
import axios from 'axios'
import Header from './Header.vue'
import SortList from './SortList.vue'

export default {
  name: 'Home',
  components: {
    Header,
    SortList
  },
  data() {
    return {
      name: '',
      restaurant: []
    }
  },
  methods: {
    async deleteRestaurant(id) {
      let result = await axios.delete('http://localhost:3000/restaurants/' + id);
      console.warn(result);
      if (result.status == 200) {
        this.loadData();
      }
    },
    async loadData() {
      let user = localStorage.getItem('user-info');
      if (user) {
        this.name = JSON.parse(user).name;
      }
      if (!user) {
        this.$router.push({ name: 'SignUp' });
      }
      let result = await axios.get('http://localhost:3000/restaurants');
      if (result && result.data) { // Check if result.data exists before assigning
        this.restaurant = result.data;
      }
    }
  },
  async mounted() {
    this.loadData();
  }
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
