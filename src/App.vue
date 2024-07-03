<script setup>
import HelloWorld from './components/HelloWorld.vue';
import TheWelcome from './components/TheWelcome.vue';
import EChartView from './components/EchartView.vue';
import { ref } from 'vue';

const person = 'YUN';
const country = 'KOR';

function excelDownload() {
  var data = [
    { name: 'John', city: 'Seattle' },
    { name: 'Mike', city: 'Los Angeles' },
    { name: 'Zane', city: 'New York' },
  ];

  // process the data into suitable format
  var ws_data = data.map((obj) => Object.values(obj));

  ws_data.unshift(Object.keys(data[0])); // add header row

  // create a worksheet
  var ws = XLSX.utils.aoa_to_sheet(ws_data);

  // create a new blank workbook, and add the worksheet to it
  var wb = XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(wb, ws, 'Sheet1');

  // generate XLSX file and send to client
  XLSX.writeFile(wb, 'Data.xlsx');
}

function excelDownload2() {
  var data = [
    ['', 'Group1', '', 'Group2', ''],
    ['', 'Header1', 'Header2', 'Header3', 'Header4'],
    ['Row1', 'Data11', 'Data12', 'Data13', 'Data14'],
    ['Row2', 'Data21', 'Data22', 'Data23', 'Data24'],
  ];

  // process the data into suitable format
  var wb = XLSX.utils.book_new();
  var ws = XLSX.utils.aoa_to_sheet(data);

  // IMPORTANT: This is how you add a merge to your sheet
  ws['!merges'] = [
    // Merge cell A1:B1 (Merge 'Group1' Header)
    { s: { r: 0, c: 1 }, e: { r: 0, c: 2 } },
    // Merge cell C1:D1 (Merge 'Group2' Header)
    { s: { r: 0, c: 3 }, e: { r: 0, c: 4 } },
  ];

  XLSX.utils.book_append_sheet(wb, ws, 'Sheet1');
  XLSX.writeFile(wb, 'MultiLevelHeaders.xlsx');
}
</script>

<template>
  <header>
    <img
      alt="Vue logo"
      class="logo"
      src="./assets/logo.svg"
      width="125"
      height="125"
    />

    <div class="wrapper">
      <button @click="excelDownload2">Excel Download</button>
      <HelloWorld msg="You did it!" />
    </div>
  </header>

  <main>
    <EChartView :person="person" :country="country" />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
