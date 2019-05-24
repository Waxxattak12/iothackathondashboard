<template>
	<div class="multi-bar-container">
		<div id="multi-bar" class="multi-bar"></div>
	</div>
</template>

<script>
	import Highcharts from "highcharts/highstock";
	import axios from 'axios';

	export default {
		name: 'OvertimeGraph',
		props: {
			devices: {
				type: Array,
				default: []
			}
		},
		data() {
			return {
				selector: "multi-bar",
				multiBarWattChart: null,
				deviceOneTotalMeasurements: [],
				deviceTwoTotalMeasurements: [],
				deviceThreeTotalMeasurements: [],
				averageDivider: 20
			}
		},
		computed: {},
		methods: {
			createMultiBarChart() {

				this.multiBarWattChart = Highcharts.chart('multi-bar', {
					chart: {
						width: 1500,
						height: 400,
						type: 'column'
					},
					title: {
						text: 'Connected Household Devices Electricity Consumption Average',
						style: {
							fontSize: "24px"
						}
					},
					subtitle: {
						text: null
					},
					xAxis: {
						categories: null,
						crosshair: false,
						labels: {
							enabled: false
						}
					},
					yAxis: {
						min: 0,
						title: {
							text: 'Watt'
						}
					},
					tooltip: {
						enabled: true,
						headerFormat: '<b>{series.name}</b><br/>',
						pointFormat: '<br/>{point.y}'
					},
					plotOptions: {
						column: {
							pointPadding: 0.2,
							borderWidth: 0
						}
					},

					// * * Real Scenario * *
					// series: [
					// 	{
					// 		name: 'Device ' + this.rawData.devicesArray[0].id,
					// 		data: [this.rawData.devicesArray[0].watt],
					// 		color: this.rawData.devicesArray[0].color
					// 	},
					// 	{
					// 		name: 'Device ' + this.electrologRawData.devicesArray[1].id,
					// 		data: [this.rawData.devicesArray[1].watt],
					// 		color: this.rawData.devicesArray[1].color
					// 	},
					// 	{
					// 		name: 'Device ' + this.electrologRawData.devicesArray[2].id,
					// 		data: [this.rawData.devicesArray[1].watt],
					// 		color: this.rawData.devicesArray[2].color
					// 	}
					// ],
					// Mock Data
					series: [{data: [1]}, {data: [2]}, {data: [3]}],

					legend: {
						enabled: false
					},
					credits: {
						enabled: false
					}
				})
			},
		},
		beforeCreate() {},
		beforeMount() {},
		mounted() {
			this.createMultiBarChart();
		}
	}
</script>

<style lang=scss scoped>
	.multi-bar-container {
		display: flex;
		justify-content: center;
		align-items: center;
		.multi-bar {}
	}
</style>
