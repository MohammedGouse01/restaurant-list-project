<template>
  <div class="custom-dropdown" >
    <div class="dropdown-toggle" @click="toggleDropdown">
      Show/Hide Columns
    </div>
    <div class="dropdown-options" :class="{ 'open': isDropdownOpen }">
      <label class="checkbox-option" v-for="(header, index) in tableHeaders" :key="index">
        <input type="checkbox" v-model="selectedOptions" :value="header" @click="applySelection" /> {{ header }}
      </label>
      <button ></button>
    </div>
  </div>
</template>

<script>
export default {
  props: ['tableHeaders'],
  data() {
    return {
      isDropdownOpen: false,
      selectedOptions: []
    };
  },
  methods: {
    toggleDropdown() {
      this.isDropdownOpen = !this.isDropdownOpen;
    },
    applySelection() {
      this.$emit('selection-changed', this.selectedOptions);
      // this.isDropdownOpen = false;
    }
  }
};
</script>

<style scoped>
/* ... (other styles) */

.dropdown-options {
  position: absolute;
  background-color: white;
  border: 1px solid #ccc;
  padding: 8px;
  display: none;
  width: 20%; /* Full width */
}

.dropdown-options.open {
  display: block;
}

.dropdown-toggle {
  cursor: pointer;
  padding: 8px;
  border: 1px solid #ccc;
  background-color: #f9f9f9;
  max-width: 150px; /* Adjust the maximum width as needed */
  white-space: nowrap; /* Prevent text wrapping */
  overflow: hidden; /* Hide overflow if text exceeds max width */
  text-overflow: ellipsis; /* Show ellipsis for truncated text */
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