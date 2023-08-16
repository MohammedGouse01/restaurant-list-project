<template>
  <div>
    <h2>Super Market Data Table</h2>
    <table>
      <thead>
        <tr>
          <th>S.No</th>
          <th v-for="header in tableHeaders" :key="header" @click="handleHeaderClick(header)"
              :style="{ backgroundColor: columnColor[`column${header}`] }">
            {{ header }}
            <span v-if="sortedField === header">{{ sortDirection === 'asc' ? '↑' : '↓' }}</span>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data, index) in sortedData" :key="index">
          <td>{{ index + 1 }}</td>
          <td v-for="header in tableHeaders" :key="header"
              :style="{ backgroundColor: columnColor[`column${header}`] }">{{ data[header] }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  props: {
    props: {
    restaurant: {
      type: Object,
      required: true
    }
  }
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
    tableHeaders(){
    
      return Object.keys(this.restaurant[0])
    }
  },
  methods: {
    handleHeaderClick(columnName) {
      if (this.prevCol !== this.columnColor) {
        this.columnColor = {
          ...this.columnColor,
          [this.prevCol]: ''
        };
        this.columnColor = {
          ...this.columnColor,
          [`column${columnName}`]: '#3498db'
        };
        this.prevCol = `column${columnName}`;
      }
    }
  }
};
</script>

<style scoped>
/* Your styles here */
</style>





<template>
  <div class="super-market-data-table">
    <h2>Super Market Data Table</h2>
    <table>
      <thead>
        <tr>
          <th v-for="header in tableHeaders" :key="header" @click="handleHeaderClick(header)" :style="getColumnHeaderStyle(header)">
            {{ header }}
            <span v-if="sortedField === header">{{ sortDirection === 'asc' ? '↑' : '↓' }}</span>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data, index) in sortedData" :key="index">
          <td v-for="header in tableHeaders" :key="header" :style="getColumnCellStyle(header)">
            {{ data[header] }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
// export default {
//   props: {
//     restaurant: {
//       type: Object,
//       required: true
//     }
//   },
//   data() {
//     return {
//       sortedField: null,
//       sortDirection: 'asc',
//       prevCol: '',
//       columnColor: {}
//     };
//   },
//   computed: {
//     sortedData() {
//       return [...this.restaurant].sort((a, b) => {
//         const aValue = a[this.sortedField];
//         const bValue = b[this.sortedField];
//         return this.sortDirection === 'asc' ? aValue.localeCompare(bValue) : bValue.localeCompare(aValue);
//       });
//     },
//     tableHeaders() {
//       if (this.restaurant.length > 0) {
//         const firstRestaurant = this.restaurant[0];
//         const keysWithoutId = Object.keys(firstRestaurant).filter(key => key !== 'id');
//         return ['id', ...keysWithoutId];
//       }
//       return [];
//     }
//   },
//   methods: {
//     handleHeaderClick(columnName) {
//       if (this.prevCol !== '') {
//         this.columnColor = {
//           ...this.columnColor,
//           [this.prevCol]: ''
//         };
//       }
//       this.columnColor = {
//         ...this.columnColor,
//         [`column${columnName}`]: '#3498db'
//       };
//       this.prevCol = `column${columnName}`;
      
//       this.sortedField = columnName;
//       this.sortDirection = this.sortDirection === 'asc' ? 'desc' : 'asc';
//     },
//     getColumnHeaderStyle(header) {
//       return {
//         backgroundColor: this.columnColor[`column${header}`]
//       };
//     },
//     getColumnCellStyle(header) {
//       return {
//         backgroundColor: this.columnColor[`column${header}`]
//       };
//     }
//   }
// };
// </script>

/* <style scoped>
.super-market-data-table {
  margin:0rem;
}
</style> */
