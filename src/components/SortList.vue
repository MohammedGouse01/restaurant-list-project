<template>
  <div>
      
      <div class="dropDown">
      <div class="toggleListButton" @click="toggleDropdown">Show/Hide Columns</div>
      <div class="dropDownOptions" :class="{'open': isDropdownOpen}">
      <label class="checkbox-option" v-for="(header, index) in tableHeaders" :key="index">
      <input type="checkbox" v-model="optionChecked[header]" v-bind="header.isVisible"/> {{ header }}</label>
      </div>
      </div>

      <h1 @click="refresh()">Restaurant List</h1>
    <table>
      <thead>
        <tr>
          <th v-for="header in visibleTableHeaders" :key="header">
            <div @click="handleHeaderClick(header)" :style="{ backgroundColor: columnColor[`column${header}`] }">
              <div v-if="header !== 'contact'">
                {{ header }}<span v-if="sortedField !== header" class="clickSort">⧋</span>
                <span v-if="sortedField === header">{{ sortDirection === 'asc' ? '▲' : '▼' }}</span>
              </div>
              <div v-if="header === 'contact'">
                {{ header }}<span v-if="sortedField !== header" class="clickSort">⧋</span>
                <span @click="handleHeaderClick(header)" :style="{ backgroundColor: columnColor[`column${header}`] }"
                  v-if="sortedField === header">
                  {{ sortDirection === 'asc' ? '▲' : '▼' }}
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
      <td v-for="header in visibleTableHeaders" :key="header" :style="{ backgroundColor: columnColor[`column${header}`] }">
        <router-link v-if="header!=='id'" :to="'/Menu/' + data.id" class="icon-button">{{ header === 'contact' ? maskContact(data[header]) : data[header] }}</router-link>
        {{ header === 'id' ? data[header]:'' }}
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
      optionChecked: {},
      isDropdownOpen: false,
      sortedField: 'id',
      sortDirection: 'asc',
      prevCol: '',
      columnColor: {},
      showMaskedContact: true,
    };
  },
  created() {
     this.tableHeaders.forEach(header => {
      this.optionChecked[header] = true; // You can set this to false if you want them unchecked by default
    });
  },
  computed: {
    visibleTableHeaders() {
    return this.tableHeaders.filter(header => this.optionChecked[header]);
  },
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
        const keysWithoutId = Object.keys(firstRestaurant).filter(key => key !== 'id' && key !== "description" && key !== "menu");
        return ['id', ...keysWithoutId];
      }
      return [];
    }
  },
  methods: {
    refresh() {
      this.sortedField = null;
      this.sortDirection = 'asc';
      this.columnColor = {};
      this.prevCol = '';
      this.showMaskedContact = true;
    },
    maskContact(contact) {
  const contactString = String(contact); // Convert to string
  if (this.showMaskedContact) {
    const visibleChars = 4;
    const maskedPart = '*'.repeat(contactString.length - visibleChars) + contactString.slice(-visibleChars);
    return maskedPart;
  }
  return contactString;
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
    },
    toggleDropdown(){
      this.isDropdownOpen=!this.isDropdownOpen;
    }
  }
};
</script>

<style scope>
.dropDown {
      margin-bottom: 3%;
      margin-left: 0;
}
 .toggleListButton {cursor: pointer;
  padding: 8px;
  border: 1px solid #ccc;
  background-color: #f9f9f9;
  max-width: 150px; /* Adjust the maximum width as needed */
  white-space: nowrap; /* Prevent text wrapping */
  overflow: hidden; /* Hide overflow if text exceeds max width */
  text-overflow: ellipsis; /* Show ellipsis for truncated text */
}

.dropDownOptions {
  position: absolute;
    width: 150px;
    background-color: white;
    border: 1px solid #ccc;
    padding: 8px;
  display: none;
  z-index: 1; /* Full width */
}
.dropDownOptions.open {
  display: block;
  }

.checkbox-option {
  display: flex;
  align-items: center;
  margin-bottom: 5px; /* Add spacing between options */
}

.checkbox-option input[type="checkbox"] {
  margin-right: 5px; /* Add spacing between checkbox and label */
}
  






</style>
