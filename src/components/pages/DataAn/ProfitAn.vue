<template>
  <div>
    <v-chart :force-fit="true" :height="height" :data="data" :scale="scale">
      <v-tooltip />
      <v-axis />
      <v-legend />
      <v-line position="month*money" color="item" />
      <v-point position="month*money" color="item" :size="4" :v-style="style" :shape="'circle'" />
    </v-chart>
  </div>
</template>

<script>
  import DataSet from '@antv/data-set';
  import axios from 'axios'

  export default {
    data() {
      return {
        data: [],
        scale: [{
          dataKey: 'month',
          min: 1,
          max: 12,
        }],
        height: 550,
        style: {
          stroke: '#fff',
          lineWidth: 1
        },
      };
    },
    methods: {
      renderChart() {
        axios.get('api/salesSum/profitAn')//请求数据
          .then(res => {
            let sourceData = [];
            res.data.map(el => {//整理数据
              let obj = {}
              obj['month'] = `${el.month}月`,
                obj['销售总额'] = el.totalSales
              obj['销售利润'] = el.totalProfit
              sourceData.push(obj)//生成数据组
            })
            const dv = new DataSet.View().source(sourceData);//创建Dataset对象
            dv.transform({//数据转换
              type: 'fold',
              fields: ['销售总额', '销售利润'],
              key: 'item',
              value: 'money',
            });
            this.data = dv.rows;
          })
          .catch(err=>console.log(err))
      }
    },
    created() {
      this.renderChart()
    }
  };

</script>
