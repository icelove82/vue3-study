<script setup>
import { ref, onMounted } from 'vue';
import { useBarChart } from './UseBarChart';
import { useEChart } from './UseEchart';

const eCharts = useEChart();

const barDivRef_01 = ref(null);
let barChart_01;

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

onMounted(() => {
  if (barDivRef_01.value) {
    barChart_01 = eCharts.init(barDivRef_01.value);
    barChart_01.setOption(makeOption());
  }
});
</script>

<template>
  <div>
    <h1>EChart View</h1>
    <br />
    <button @click="handleChange">Change</button>
    <span>{{ ' ' }}</span>
    <button @click="handleRefresh">Refresh</button>
    <br />
    <div ref="barDivRef_01" style="width: 600px; height: 400px">test</div>
  </div>
</template>
