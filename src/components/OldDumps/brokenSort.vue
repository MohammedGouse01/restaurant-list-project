<template>
  <div>
    <table>
      <thead>
        <tr>
          <th v-for="header in tableHeaders" :key="header" @click="handleHeaderClick(header)"
              :style="{ backgroundColor: columnColor[`column${header}`] }">
            {{ header }}
            <span v-if="sortedField === header">{{ sortDirection === 'asc' ? '↑' : '↓' }}</span>
          </th>
         <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data, index) in sortedData" :key="index">
          <td v-for="header in tableHeaders" :key="header"
              :style="{ backgroundColor: columnColor[`column${header}`] }">{{ data[header] }}</td>
          <td><router-link :to="'/update/'+data.id" class="icon-button">
              <i class="fas fa-pencil-alt" ></i></router-link><span> or </span>
        <button @click="confirmDelete(data.id)" class="icon-button">
              <i class="fas fa-trash"></i>
</button>
         </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>


<script>
export default {
  props: {
    restaurant: {
      type: Object,
      required: true
    },
    deleteRestaurant: Function,
  },
  data() {
    return {
      sortedField: null,
      sortDirection: 'asc',
      prevCol: '',
      columnColor: {}
    };
  },
  computed: {
    sortedData() {
      return [...this.restaurant].sort((a, b) => {
        const aValue = a[this.sortedField];
        const bValue = b[this.sortedField];

        if (this.sortDirection === 'asc') {
          return aValue > bValue ? 1 : aValue < bValue ? -1 : 0;
        } else {
          return aValue < bValue ? 1 : aValue > bValue ? -1 : 0;
        }
      });
    },
    tableHeaders() {
      if (this.restaurant.length > 0) {
    const firstRestaurant = this.restaurant[0];
    const keysWithoutId = Object.keys(firstRestaurant).filter(key => key !== 'id');
    return ['id', ...keysWithoutId];
  }
  return [];
    }
    
  },
  methods: {
     confirmDelete(id) {
      const shouldDelete = window.confirm("Are you sure you want to delete this restaurant?");
      if (shouldDelete) {
        this.deleteRestaurant(id);
      }
    },
    handleHeaderClick(columnName) {
      if (this.prevCol !== '') {
        this.columnColor = {
          ...this.columnColor,
          [this.prevCol]: ''
        };
      }
      this.columnColor = {
        ...this.columnColor,
        [`column${columnName}`]: '#3498db'
      };
      this.prevCol = `column${columnName}`;
      
      // Update sortedField and sortDirection
      this.sortedField = columnName;
      this.sortDirection = this.sortDirection === 'asc' ? 'desc' : 'asc';
    }
  }
};
</script>

<style scoped>
/* Your styles here */
</style>