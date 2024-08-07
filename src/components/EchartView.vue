<script setup>
import { ref, onMounted, watchEffect, shallowRef, nextTick } from 'vue';
import { useBarChart } from './UseBarChart';
import { useEChart } from './UseEchart';
import { useBarChartOption } from './UseOption';

import * as ExcelJS from 'exceljs';
import { saveAs } from 'file-saver';

const { person, country } = defineProps(['person', 'country']);

const barDivRef_01 = ref(null);
const barDivRef_02 = ref(null);
const treemapDivRef_03 = ref(null);

let barChart_01;
let barChart_02;
let treemapChart_03;

const eCharts = useEChart();
const [ativeOption, resetActiveOption] = useBarChartOption();

const isVisible = ref(true);

const makeOption = () => {
  let option = {
    title: {
      text: 'ECharts 入门示例',
    },
    tooltip: {},
    legend: {
      data: ['销量'],
    },
    xAxis: {
      data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子'],
    },
    yAxis: {},
    series: [
      {
        name: '销量',
        type: 'bar',
        data: [5, 20, 36, 10, 10, 20],
      },
    ],
  };

  return option;
};

const treemapOption = () => {
  let option = {
    series: [
      {
        type: 'treemap',
        data: [
          {
            name: 'Node A',
            value: 10,
            children: [
              {
                name: 'Node A1',
                value: 4,
              },
              {
                name: 'Node A2',
                value: 6,
              },
            ],
          },
          {
            name: 'Node B',
            value: 20,
          },
        ],
      },
    ],
  };

  return option;
};

function handleChange() {
  let newOption = makeOption();
  newOption.series[0].data[5] = 200;
  barChart_01.setOption(newOption);
}

function handleRefresh() {
  let initialOption = makeOption();
  barChart_01.setOption(initialOption);
}

async function handleOnOff() {
  isVisible.value = !isVisible.value;

  await nextTick();
  if (isVisible.value) {
    iniBarChart01();
  } else {
    barChart_01.dispose();
  }
}

function handleChange02() {
  ativeOption.value.series[0].data[5] = 200;
}

function handleRefresh02() {
  resetActiveOption();
}

function iniBarChart01() {
  if (barDivRef_01.value) {
    barChart_01 = eCharts.init(barDivRef_01.value);
    barChart_01.setOption(makeOption());

    barChart_01.on('click', function (params) {
      console.log(params);
    });

    barChart_01.getZr().on('click', function (event) {
      if (!event.target) {
        console.log('点击在了空白处');
      }
    });
  }
}

watchEffect(() => {
  if (barDivRef_02.value) {
    barChart_02.setOption(ativeOption.value);
  }
});

onMounted(() => {
  iniBarChart01();

  if (barDivRef_02.value) {
    barChart_02 = eCharts.init(barDivRef_02.value);
    barChart_02.setOption(ativeOption.value);
  }

  if (treemapDivRef_03.value) {
    treemapChart_03 = eCharts.init(treemapDivRef_03.value);
    treemapChart_03.setOption(treemapOption());
  }
});

// Test Shallow & getter reative
const testRef = ref({ name: '', age: 0 });
const testShallow = shallowRef({ name: '', age: 0 });
let count = () => 0 + testRef.value.age;

function handleChangeShallow(name, age) {
  testRef.value.name = name;
  testRef.value.age = age;

  testShallow.value = { name, age };
}

// Test excel download
function excelDownload() {
  generateExcel(
    [
      {
        values: ['', 'Header1', '', 'Header2', ''],
        ranges: ['', 'B1:C1', 'D1:E1'],
      },
      {
        values: ['', 'Subheader1', 'Subheader2', 'Subheader3', 'Subheader4'],
      },
    ],
    [
      ['Category1', 'Data1', 'Data2', 'Data3', 'Data4'],
      ['Category2', 'Data1', 'Data2', 'Data3', 'Data4'],
    ],
    'Multi-Level Headers.xlsx'
  );
}
async function generateExcel(headerRows, data, fileName = 'Excel Report.xlsx') {
  const workbook = new ExcelJS.Workbook();
  const worksheet = workbook.addWorksheet('My Sheet');

  // Add header rows to the worksheet
  headerRows.forEach((row) => {
    const headerRow = worksheet.addRow(row.values);
    headerRow.height = 20; // set row height

    // Apply styles to the header row
    headerRow.eachCell((cell) => {
      cell.fill = {
        type: 'pattern',
        pattern: 'solid',
        fgColor: { argb: 'FF808080' }, // gray color
      };
      cell.border = {
        top: { style: 'thin' },
        left: { style: 'thin' },
        bottom: { style: 'thin' },
        right: { style: 'thin' },
      };
      cell.font = { bold: true };
    });

    // Merge cells if ranges are provided
    if (row.ranges) {
      row.ranges.forEach((range) => {
        worksheet.mergeCells(range);
      });
    }
  });

  // Add data rows to the worksheet
  data.forEach((row) => {
    worksheet.addRow(row);
  });

  // Style column 'A'
  worksheet
    .getColumn(1)
    .eachCell({ includeEmpty: false }, (cell, rowNumber) => {
      // Skip the header rows
      if (rowNumber > headerRows.length) {
        cell.fill = {
          type: 'pattern',
          pattern: 'solid',
          fgColor: { argb: 'FFFFFF00' }, // yellow color
        };
      }
    });

  // Generate a buffer
  const buffer = await workbook.xlsx.writeBuffer();

  // Use file-saver to trigger download
  const blob = new Blob([buffer], {
    type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
  });
  saveAs(blob, fileName);
}
</script>

<template>
  <div>
    <span>Person : {{ person }}</span> {{ ' -- ' }}
    <span>Counrty : {{ country }}</span> {{ ' -- ' }}
    <button @click="excelDownload">Excel Download With Style</button>

    <h1>EChart View Ver.1</h1>
    <br />
    <button @click="handleChange">Change Ver.1</button>
    <span>{{ ' ' }}</span>
    <button @click="handleRefresh">Refresh Ver.1</button>
    <span>{{ ' ' }}</span>
    <button @click="handleOnOff">Show/Hid</button>
    <br />
    <div
      v-if="isVisible"
      ref="barDivRef_01"
      style="width: 600px; height: 400px"
    >
      test
    </div>

    <br />

    <h1>EChart View Ver.2</h1>
    <button @click="handleChange02">Change Ver.2</button>
    <span>{{ ' ' }}</span>
    <button @click="handleRefresh02">Refresh Ver.2</button>
    <br />
    <div ref="barDivRef_02" style="width: 600px; height: 400px"></div>
    <br />

    <button @click="handleChangeShallow('yun', 42)">Change</button>
    <h1>Ref</h1>
    <span>{{ testRef }}</span>

    <h1>shallowRef</h1>
    <span>{{ testShallow }}</span>

    <h1>Count</h1>
    <span>{{ count() }}</span>

    <h1>EChart View Ver.3 TreeMap</h1>
    <br />
    <div ref="treemapDivRef_03" style="width: 600px; height: 400px"></div>
    <br />
  </div>
</template>
