<template>
  <div>
      <h1 @click="refresh()">Restaurant List</h1>
    <table>
      <thead>
        <tr>
          <th v-for="header in tableHeaders" :key="header">
            <div @click="handleHeaderClick(header)" :style="{ backgroundColor: columnColor[`column${header}`] }">
              <div v-if="header !== 'contact'">
                {{ header }}
                <span v-if="sortedField === header">{{ sortDirection === 'asc' ? '↑' : '↓' }}</span>
              </div>
              <div v-if="header === 'contact'">
                {{ header }}
                <span @click="handleHeaderClick(header)" :style="{ backgroundColor: columnColor[`column${header}`] }"
                  v-if="sortedField === header">
                  {{ sortDirection === 'asc' ? '↑' : '↓' }}
                </span>
                <span @click.stop="toggleMaskContact" style="cursor: pointer;">
                  <i class="fa" :class="showMaskedContact ? 'fa-eye-slash' : 'fa-eye'" aria-hidden="true"></i>
                </span>
              </div>
            </div>
          </th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data, index) in sortedData" :key="index">
          <td v-for="header in tableHeaders" :key="header"
            :style="{ backgroundColor: columnColor[`column${header}`] }">
            {{ header === 'contact' ? maskContact(data[header]) : data[header] }}
          </td>
          <td>
            <router-link :to="'/update/' + data.id" class="icon-button">
              <i class="fas fa-pencil-alt"></i>
            </router-link>
            <span> or </span>
            <button @click="confirmDelete(data.id)" class="icon-button">
              <i class="fas fa-trash"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
  <import-export @imported-data="updateRestaurantData"></import-export>
</template>
<script>
import ImportExport from './ImportExport.vue';
export default {components: {
    ImportExport,
  },
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
      columnColor: {},
      showMaskedContact: true,
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
    updateRestaurantData(importedData) {
    this.restaurant = importedData;
  },
   handleImportedData(importedData) {
  if (importedData) {
    this.$emit('imported-data', importedData);
  }
},
    refresh() {
      this.sortedField = null;
      this.sortDirection = 'asc';
      this.columnColor = {};
      this.prevCol = '';
      this.showMaskedContact = true;
    },
    maskContact(contact) {
      if (this.showMaskedContact) {
        const visibleChars = 4;
        const maskedPart = '*'.repeat(contact.length - visibleChars) + contact.slice(-visibleChars);
        return maskedPart;
      }
      return contact;
    },
    toggleMaskContact() {
      this.showMaskedContact = !this.showMaskedContact;
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
    },
    confirmDelete(id) {
      const shouldDelete = window.confirm("Are you sure you want to delete this restaurant?");
      if (shouldDelete) {
        this.deleteRestaurant(id);
      }
    }
  }
};
</script>

<style>

</style>