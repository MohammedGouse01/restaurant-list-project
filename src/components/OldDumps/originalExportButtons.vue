<template>
  <div class="export-dropdown">
      <button @click="toggleExportOptions()" class="export-button">Export Data</button>
      <div class="export-options" v-if="showExportOptions">
        <button @click="exportData('pdf')" v-if="data.length > 0">Export as PDF</button>
        <button @click="exportData('excel')" v-if="data.length > 0">Export as Excel</button>
        <button @click="exportData('csv')" v-if="data.length > 0">Export as CSV</button>
      </div>
    </div>
</template>

<script>
import XLSX from 'xlsx';
import jsPDF from 'jspdf';
import 'jspdf-autotable';
import Papa from 'papaparse';
import { saveAs } from 'file-saver';

export default {
  props: {
    data: Array,
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
    exportToPdf() {
      if (this.data.length === 0) {
        return;
      }

      const pdf = new jsPDF();
      pdf.text('Restaurant Data', 10, 10);
      pdf.autoTable({ head: [this.tableHeaders], body: this.data.map(obj=>this.tableHeaders.map(header=>obj[header])) });
      pdf.save('restaurant_data.pdf');
    },
    exportToExcel() {
      if (this.data.length === 0) {
        return;
      }

      const worksheet = XLSX.utils.json_to_sheet(this.data);
      const workbook = XLSX.utils.book_new();
      XLSX.utils.book_append_sheet(workbook, worksheet, 'Sheet1');
      const excelBuffer = XLSX.write(workbook, { bookType: 'xlsx', type: 'array' });
      const excelBlob = new Blob([excelBuffer], { type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet' });
      saveAs(excelBlob, 'restaurant_data.xlsx');
    },
    exportToCsv() {
      if (this.data.length === 0) {
        return;
      }

      const csvData = Papa.unparse(this.data);
      const csvBlob = new Blob([csvData], { type: 'text/csv;charset=utf-8;' });
      saveAs(csvBlob, 'restaurant_data.csv');
    },
  },
};
</script>

<style>
/* Add your component styles here */
</style>
