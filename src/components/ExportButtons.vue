<template>

    <div class="export-dropdown">
      <button @click="toggleExportOptions()" class="export-button">Export Data</button>
      <div class="export-options" v-if="showExportOptions">
        <button @click="exportData('pdf')">Export as PDF</button>
        <button @click="exportData('excel')">Export as Excel</button>
        <button @click="exportData('csv')">Export as CSV</button>
      </div>
    </div>

</template>

<script>
import * as XLSX from 'xlsx';
import jsPDF from 'jspdf';
import 'jspdf-autotable';
import Papa from 'papaparse';
import { saveAs } from 'file-saver';


export default {
  props: {
    data: Array,
  },
  data() {
    return {
      showExportOptions: false,
    };
  },computed:{
    tableHeaders() {
      if (this.data.length > 0) {
        const firstRestaurant = this.data[0];
        const keysWithoutId = Object.keys(firstRestaurant).filter(key => key !== 'id');
        return ['id', ...keysWithoutId];
      }
      return [];
    }
  },
  methods: {
    toggleExportOptions() {
      this.showExportOptions = !this.showExportOptions;
    },
    exportData(format) {
      this.showExportOptions = false;

      const tableData = this.data.map(data => {
        const row = this.tableHeaders.map(header => {
          return data[header];
        });
        return row;
      });

      switch (format) {
        case 'pdf':
          this.exportPDF(tableData);
          break;
        case 'excel':
          this.exportExcel(tableData);
          break;
        case 'csv':
          this.exportCSV(tableData);
          break;
        default:
          break;
      }
    },
    exportPDF(tableData) {
      const doc = new jsPDF();
      const headers = this.tableHeaders.map(header =>(header));

      doc.autoTable({
        head: [headers],
        body: tableData,
      });

      doc.save('restaurant_data.pdf');
    },
  exportExcel(tableData) {
    
      const workbook = XLSX.utils.book_new();
      const worksheet = XLSX.utils.aoa_to_sheet([this.tableHeaders, ...tableData]);

      XLSX.utils.book_append_sheet(workbook, worksheet, 'Restaurant Data');
      XLSX.writeFile(workbook, 'restaurant_data.xlsx');
  
},
    exportCSV(tableData) {
      const csvContent = Papa.unparse([this.tableHeaders, ...tableData]);
      const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8' });
      saveAs(blob, 'restaurant_data.csv');
    },
  }}
</script>

<style>
/* Your styles here */
.export-dropdown {
  position: relative;
  display: inline-block;
}

.export-options {
  position: absolute;
  top: 100%;
  left: 0;
  background-color: white;
  border: 1px solid #ccc;
  padding: 5px;
  display: flex;
  flex-direction: column;
}

.export-button {
  margin-top: 12px;
  cursor: pointer;
  
}
</style>

