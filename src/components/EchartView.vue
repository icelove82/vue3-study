<script setup>
import { ref, onMounted, watchEffect } from 'vue';
import { useBarChart } from './UseBarChart';
import { useEChart } from './UseEchart';
import { useBarChartOption } from './UseOption';

const eCharts = useEChart();
const ativeOption = useBarChartOption();

const barDivRef_01 = ref(null);
const barDivRef_02 = ref(null);

let barChart_01;
let barChart_02;

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

function handleChange() {
  let newOption = makeOption();
  newOption.series[0].data[5] = 200;
  barChart_01.setOption(newOption);
}

function handleRefresh() {
  let initialOption = makeOption();
  barChart_01.setOption(initialOption);
}

function handleChange02() {
  ativeOption.value.series[0].data[5] = 200;
}

function handleRefresh02() {
  ativeOption.value = makeOption();
}

watchEffect(() => {
  if (barDivRef_02.value) {
    barChart_02.setOption(ativeOption.value);
  }
});

onMounted(() => {
  if (barDivRef_01.value) {
    barChart_01 = eCharts.init(barDivRef_01.value);
    barChart_01.setOption(makeOption());
  }

  if (barDivRef_02.value) {
    barChart_02 = eCharts.init(barDivRef_02.value);
    barChart_02.setOption(ativeOption.value);
  }
});
</script>

<template>
  <div>
    <h1>EChart View Ver.1</h1>
    <br />
    <button @click="handleChange">Change Ver.1</button>
    <span>{{ ' ' }}</span>
    <button @click="handleRefresh">Refresh Ver.1</button>
    <br />
    <div ref="barDivRef_01" style="width: 600px; height: 400px">test</div>

    <br />

    <h1>EChart View Ver.2</h1>
    <button @click="handleChange02">Change Ver.2</button>
    <span>{{ ' ' }}</span>
    <button @click="handleRefresh02">Refresh Ver.2</button>
    <br />
    <div ref="barDivRef_02" style="width: 600px; height: 400px">test</div>
    <br />
  </div>
</template>
