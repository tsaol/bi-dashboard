<template>
    <bi-chart ref="biChart"
        :options="chartOption"
        :no-support="noSupport"
        :no-support-tip="noSupportTip"
        :auto-resize="true"></bi-chart>
</template>

<script>
import _ from 'lodash';
import {convertColumnToData} from './chart-util';

export default {
  name: 'ResultChartSunburst',
  props: {
    worksheet: {
      type: Object,
      default() {
        return {};
      }
    },
    viewData: {
      type: Object,
      default() {
        return {};
      }
    },
  },
  data() {
    return {
      chartOption: {},
      noSupport: false,
      noSupportTip: '数据不支持该图表展示，请尝试1个或多个维度列和1个度量值'
    };
  },
  created() {
    if (!_.isEmpty(this.viewData)) {
      this.renderChart(this.viewData);
    }
  },
  watch: {
    ['viewData'](viewData) {
      if (!_.isEmpty(viewData)) {
        this.renderChart(viewData);
      }
    }
  },
  methods: {
    capture() {
      return this.$refs.biChart.capture();
    },
    renderChart(viewData) {
      const {values} = this.worksheet;
      const {columnEnums, rowsData} = viewData;

      this.noSupport = false;
      if (columnEnums.length < 1 || values.length !== 1 || rowsData.length !== 1) {
        this.noSupport = true;
        return;
      }
      const chartOptions = {
        // tooltip: { //echart下一个版本支持tooltip
        //     trigger: 'item',
        // },
      };

      chartOptions.series = {
        type: 'sunburst',
        data: convertColumnToData(rowsData[0], values[0], columnEnums.length)
      };

      this.chartOption = chartOptions;
    }
  },
};
</script>

<style lang="scss">
.bi-chart {
  width: 100% !important;
  height: 100% !important;
}
</style>
