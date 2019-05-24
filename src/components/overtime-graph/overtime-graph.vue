<template>
	<div class="overtime-graph-container">
		<div id="overtime-graph" class="overtime-graph"></div>
	</div>
</template>

<script>
	import Highcharts from "highcharts/highstock";
	import axios from 'axios';

	export default {
		name: 'OvertimeGraph',
		data() {
			return {
				selector: "overtime-graph",
				multiWattLineChart: null,
				deviceOneTotalMeasurements: [],
				deviceTwoTotalMeasurements: [],
				deviceThreeTotalMeasurements: [],
				averageDivider: 20
			}
		},
		computed: {},
		methods: {
			createMultiWattChart() {
				// Mocks the data brought from all devices & massaged to look like so
				let multiSeriesArray = [
					{
						data: [1, 2, 3, 4, 5, 6, 7, 8],
						ip: "http://192.168.1.126/"
					},
					{
						data: [10, 20, 30, 40, 50, 60, 70, 80],
						ip: "http://192.168.1.116/"
					},
					{
						data: [100, 200, 300, 400, 500, 600, 700, 800],
						ip: "http://192.168.1.119/"
					}
				];


				let self = this;
				this.multiWattLineChart = Highcharts.stockChart(this.selector, {
					chart: {
						width: 1500,
						height: 500,
						events: {
							load: function () {
								// Passing Context & series of multi devices object - set up the updating of the chart each second
								console.log('Mock Massaged Data from Multiple Devices - ', multiSeriesArray);
								self.getRealTimeData(self, multiSeriesArray);
							}
						}
					},

					time: {
						useUTC: false
					},

					rangeSelector: {
						buttons: [{
							count: 1,
							type: 'minute',
							text: '1M'
						}, {
							count: 5,
							type: 'minute',
							text: '5M'
						}, {
							type: 'all',
							text: 'All'
						}],
						inputEnabled: false,
						selected: 2 // in buttons - the selected button's index (default is all now)
					},

					title: {
						text: 'Connected Household Devices Electricity Logging Live Feed',
						style: {
							fontSize: "24px"
						}
					},

					exporting: {
						enabled: false
					},

					// * * Real Scenario * *
					// series: [
					// 	{
					// 		name: "1",
					// 		// Data initialized with empty array
					// 		data:  (function () {
					// 				return [];
					// 			}()),
					// 		color: "darkcyan"
					// 	},
					// 	{
					// 		name: "2",
					// 		// Data initialized with empty array
					// 		data: (function () {
					// 			return [];
					// 		}()),
					// 		color: "purple"
					// 	},
					// 	{
					// 		name: "3",
					// 		// Data initialized with empty array
					// 		data: (function () {
					// 			return [];
					// 		}()),
					// 		color: "goldenrod",
					// 	}
					// ],
					// * * Mock Data * *
					series: multiSeriesArray,

					// responsive: {
					// 	rules: [{
					// 		condition: {
					// 			maxWidth: 500
					// 		},
					// 	}]
					// },

					credits: {
						enabled: false
					}
				})
			},
			getRealTimeData(context, seriesArray) {
				setInterval(function () {
					// Device 0 to N
					// TODO -  test refactor working with real data (production) 5.5.19
					seriesArray.forEach((series) => {
						context.getDeviceData(series.ip, context, series)
					});
				}, 1000);
			},
			getDeviceData(ip, context, series) {
				let config = {
					headers: {
						'Content-type': 'application/json'
					}
				};
				console.log('Each Devices ip - ', ip);
				axios.get(ip, config)
					.then(function (response) {
						if (response && response.data) {
							let measurementValue = response.data.value;
							series.name = "Device " + response.data.id;

							// * * setMeasurmentAverage
							if (context.deviceOneTotalMeasurements.length < context.averageDivider) {
								let measurementLength = context.deviceOneTotalMeasurements.length;
								let averageMeasurementValue = measurementValue / measurementLength
								context.deviceOneTotalMeasurements.push(averageMeasurementValue);
							} else if (context.deviceOneTotalMeasurements.length === context.averageDivider) {
								// length >= 20 - take last 2- indexes
								context.deviceOneTotalMeasurements = context.deviceOneTotalMeasurements.splice(context.deviceOneTotalMeasurements.length - context.averageDivider + 1);
							}

							// TODO - refactor to different function updateSeries
							// * * Update Bar Chart with new measurement (just index 0)
							context.multiBarWattChart.update({
								series: [
									{
										data: [
											measurementValue
										]
									},
									{},
									{}
								]
							});

							let currentDeviceValue = response.data.value;
							let x = (new Date()).getTime();
							let y = currentDeviceValue;

							// Dynamically keep it at a specific value
							let shift = series.data.length >= 1000;
							series.addPoint([x, y], true, shift);
						}
					})
					.catch(function (error) {
						console.log('error - ', error);
					});
			}
		},
		beforeCreate() {
		},
		beforeMount() {
		},
		mounted() {
			this.createMultiWattChart();
		}
	}
</script>

<style lang=scss scoped>
	.overtime-graph-container {
		display: flex;
		justify-content: center;
		align-items: center;

		.overtime-graph {
		}
	}
</style>
