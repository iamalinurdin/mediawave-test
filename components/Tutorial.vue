<template>
  <div>
    <highchart
      :options="chartOptions"
      :modules="['sankey', 'dependency-wheel']"
    />
  </div>
</template>

<script>
export default {
  props: ['series', 'categories'],
  name: "index",
  data() {
    return {
      chartOptions: {
        chart: {
        type: 'column'
    },
    title: {
        text: 'Covid data from 2020',
        align: 'left'
    },
    subtitle: {
        text:
            'Source: <a target="_blank" ' +
            'href="https://www.indexmundi.com/agriculture/?commodity=corn">indexmundi</a>',
        align: 'left'
    },
    xAxis: {
        categories: this.categories,
        crosshair: true,
        accessibility: {
            description: 'Countries'
        }
    },
    plotOptions: {
        column: {
            pointPadding: 0.2,
            borderWidth: 0
        }
    },
    series: [
        {
            name: 'Total',
            data: [406292, 260000, 107000, 68300, 27500, 14500]
        },
        {
            name: 'New',
            data: [51086, 136000, 5500, 141000, 107180, 77000]
        },
    ]
      }
    };
  },
};
</script>
