<template>
  <div>
    <div class="export-dropdown">
      <button @click="toggleExportOptions()" class="export-button">Export Data</button>
      <div class="export-options" v-if="showExportOptions">
        <button @click="exportData('pdf')">Export as PDF</button>
        <button @click="exportData('excel')">Export as Excel</button>
        <button @click="exportData('csv')">Export as CSV</button>
      </div>
    </div>
    <input type="file" @change="importData" accept=".pdf,.xlsx,.csv" />
  </div>
</template>

<script>
import { saveAs } from 'file-saver';
import * as XLSX from 'xlsx';
import jsPDF from 'jspdf';
import Papa from 'papaparse'; // Import Papa object here

export default {
  data() {
    return {
      showExportOptions: false,
    };
  },
  methods: {
    toggleExportOptions() {
      this.showExportOptions = !this.showExportOptions;
    },
    exportData(format) {
      this.showExportOptions = false;

      const tableData = this.$parent.sortedData.map(data => {
        const row = this.$parent.tableHeaders.map(header => {
          return header === 'contact' ? this.$parent.maskContact(data[header]) : data[header];
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
      const headers = this.$parent.tableHeaders.map(header => (header === 'contact' ? 'Contact (Masked)' : header));

      doc.autoTable({
        head: [headers],
        body: tableData,
      });

      doc.save('restaurant_data.pdf');
    },
    exportExcel(tableData) {
      const workbook = XLSX.utils.book_new();
      const worksheet = XLSX.utils.aoa_to_sheet([this.$parent.tableHeaders, ...tableData]);

      XLSX.utils.book_append_sheet(workbook, worksheet, 'Restaurant Data');
      XLSX.writeFile(workbook, 'restaurant_data.xlsx');
    },
    exportCSV(tableData) {
      const csvContent = Papa.unparse([this.$parent.tableHeaders, ...tableData]);
      const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8' });
      saveAs(blob, 'restaurant_data.csv');
    },
     importData(event) {
      const file = event.target.files[0];
      if (!file) return;

      const fileExtension = file.name.split('.').pop().toLowerCase();

      if (fileExtension === 'csv') {
        const reader = new FileReader();
        reader.onload = async (e) => {
          const csvData = e.target.result;
          const parsedData = Papa.parse(csvData, { header: true });

          if (parsedData && parsedData.data && parsedData.data.length > 0) {
            // Send the parsedData to the backend API
            try {
              const response = await axios.post('http://localhost:3000/restaurant', {
                data: parsedData.data
              });

              if (response.status === 200) {
                console.log('Import successful');
                // Potentially trigger a data refresh or update
              }
            } catch (error) {
              console.error('Import failed:', error);
            }
          }
        };
        reader.readAsText(file);
      } else if (fileExtension === 'xlsx') {
        const reader = new FileReader();
        reader.onload = async (e) => {
          const data = new Uint8Array(e.target.result);
          const workbook = XLSX.read(data, { type: 'array' });

          const firstSheetName = workbook.SheetNames[0];
          const worksheet = workbook.Sheets[firstSheetName];
          const parsedData = XLSX.utils.sheet_to_json(worksheet, { header: 1 });

          if (parsedData && parsedData.length > 0) {
            this.$parent.handleImportedData(parsedData.slice(1));
          }
        };
        reader.readAsArrayBuffer(file);
      } else {
        console.log('Unsupported file format');
      }
    },
  },
};
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
  cursor: pointer;
}
</style>

