<template>
  <div>
    <Header/>
    <div class="h1tag">
      <h1>Hi! {{ name }}, welcome on Home Page</h1>
      
    </div>
    <div class="external">
    <span><ExportButtons :data="restaurant" /></span> 
    <span><ImportButtons @imported-data="handleImportedData" /></span>
    </div>
    <SortList v-if="restaurant.length > 0" :restaurant="restaurant" :deleteRestaurant="deleteRestaurant" @data-imported="handleDataImported" />
   
  </div>
</template>

<script>
import axios from 'axios'
import Header from './Header.vue'
import SortList from './SortList.vue'
import ExportButtons from './ExportButtons.vue'
import ImportButtons from './ImportButtons.vue'

export default {
  name: 'Home',
  components: {
    Header,
    SortList,
    ExportButtons,
    ImportButtons,
  },
  data() {
    return {
      name: '',
      restaurant: []
    }
  },
  methods: {
    async handleDataImported(importedData) {
     try {
      if (importedData && Array.isArray(importedData)) {
        this.restaurant = [...importedData];
      }
      
     } catch (error) {
      console.log('server is busy')
     } 
    },
    async deleteRestaurant(id) {
      let result = await axios.delete('http://localhost:3000/restaurant/' + id);
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
      let result = await axios.get('http://localhost:3000/restaurant');
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
.external{
  display: flexbox;
}
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
    .external{
      display: flex;
      justify-content: flex-end
    }
    .external span{
      display: inline-flex;
      justify-content: flex-end
    }
</style>
