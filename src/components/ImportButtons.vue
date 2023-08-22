<template>
  <div class="container">
    <button class="button" @click="handleUploadClick">
      Upload
    </button>
    <button @click="deleteAllItems">Upload Data</button>
  </div>
</template>

<script>
import axios from 'axios';
import * as XLSX from 'xlsx';

export default {
  data() {
    return {
      selectedFile: null,
      dataList: [],
      columnNames: [],
    };
  },
  methods: {
     async handleUploadClick() {
  const fileInput = document.createElement('input');
  fileInput.type = 'file';
  fileInput.accept = '.xlsx, .xls';
  fileInput.style.display = 'none';
  document.body.appendChild(fileInput);

  return new Promise(resolve => {
    fileInput.addEventListener('change', event => {
      this.handleFileSelection(event).then(() => resolve());
    });

    fileInput.click();
  });
},

async handleFileSelection(event) {
  this.selectedFile = event.target.files[0];
  return this.extractDataFromFile();
},

async extractDataFromFile() {
  const fileReader = new FileReader();

  fileReader.readAsBinaryString(this.selectedFile);
  this.selectedFile=null;
  return new Promise(resolve => {
    fileReader.onload = async event => {
      const workbook = XLSX.read(event.target.result, { type: 'binary' });
      const sheetName = workbook.SheetNames[0];
      const sheet = workbook.Sheets[sheetName];
      const rows = XLSX.utils.sheet_to_json(sheet, { header: 1, defval: '' });

      this.columnNames = rows[0];

      const data = rows.slice(1);

      const dataArray = data.map(item => {
        const dataObject = {};

        this.columnNames.forEach((column, index) => {
          dataObject[column] = item[index];
        });

        return dataObject;
      });

      this.dataList = dataArray;
      console.warn(typeof dataArray)
      resolve();
    };
  });
 
},
toggleInput(){

},

async uploadData() {
  try {
    let i=0;
    for (const dataItem of this.dataList) {
    const response = await axios.post("http://localhost:3000/restaurant", dataItem);
    console.log("Data uploaded successfully:", response.data);
      
      const lastIndex = this.dataList.length - 1
      if(lastIndex==i){
        this.handleImportClick()
      }
        i++;
      
    }
    
    console.log("All data uploaded successfully!");
  } catch (error) {
    console.error("Error uploading data:", error);
  }
},
async handleImportClick() {
      try {


        setTimeout(() => {
        const importedData = [...this.dataList];
        this.dataList = []; // Clear the dataList
        this.columnNames = [];
        this.fileInput = null;
        this.$emit('imported-data', importedData);
        console.warn('this is emited')
        }, 1000);

      } catch (error) {
        console.error('Error importing data:', error);
      }
    },

  async deleteAllItems() {
    try {
      const itemsToDelete = await this.fetchItems();
      if (itemsToDelete.length > 0) {
      for (const previtem of itemsToDelete) {
  const response = await axios.delete(`http://localhost:3000/restaurant/${previtem.id}`);
  console.log(`Deleting item ${previtem.id}:`, response.data);
}
    }else {
      console.log("No items to delete. Uploading data...");
    }
    await this.uploadData()
    } catch (error) {
      console.error("Error deleting items:", error);
    }
  },

async fetchItems() {
  try {
    const result = await axios.get('http://localhost:3000/restaurant');
    if (result && result.data) {
      return result.data;
    }
    return [];
  } catch (error) {
    console.error("Error fetching items:", error);
    return [];
  }
},

  },
};
</script>

<style scoped>
.button {
  background-color: #008CBA;
  border: none;
  color: white;
  padding: 12px 24px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  border-radius: 4px;
  cursor: pointer;
  transition-duration: 0.4s;
}

.button:hover {
  background-color: #006B95;
}
</style>
