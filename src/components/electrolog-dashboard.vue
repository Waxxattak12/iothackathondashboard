<template>
	<div class="electrolog-dashboard">

		<overtime-graph></overtime-graph>

		<!--TODO Test the real time updating of bidned data in production-->
		<multi-bar :devices="electrologRawData.devicesArray"></multi-bar>

	</div>
</template>

<script>
	// import Highcharts from 'Highcharts';
	import Highcharts from "highcharts/highstock";
	import axios from 'axios';

	import overtimeGraph from './overtime-graph/overtime-graph';
	import multiBar from './multi-bar/multi-bar';

	export default {
		name: 'ElectrologDashboard',
		components: {
			overtimeGraph: overtimeGraph,
			multiBar: multiBar
		},
		data() {
			return {
				electrologRawData: {
					"timestamp": 1231481251,
					"devicesArray": [
						{
							"id": 1,
							"name": "Fridge",
							"watt": 0,
							"color": "darkcyan"
						},
						{
							"id": 2,
							"name": "Microwave",
							"watt": 0,
							"color": "purple"
						},
						{
							"id": 3,
							"name": "Television",
							"watt": 0,
							"color": "goldenRod"
						}
					]
				},
				// multiBarWattChart: null,
				// multiWattLineChart: null,
				devicesWatt: [],
				devicesNames: [],

				deviceOneTotalMeasurements: [],
				deviceTwoTotalMeasurements: [],
				deviceThreeTotalMeasurements: [],
				averageDivider: 20
			}
		},
		computed: {},
		methods: {
			// * * * General * * *
			mapDevicesWatt() {
				this.devicesWatt = [];
				this.electrologRawData.devicesArray.forEach((device) => {
					this.devicesWatt.push(device.watt);
				});
			},
			mapDevicesNames() {
				this.devicesNames = [];
				this.electrologRawData.devicesArray.forEach((device) => {
					this.devicesNames.push('Device' + device.id);
				});
			},
			randomizeDevicesWatt() {
				// Change Rate - 0 to 10% change at the moment each iteration
				this.devicesWatt.forEach((watt, i) => {
					let randomChange = this.getRandomRange(0, 0.1);
					this.devicesWatt[i] += this.devicesWatt[i] * randomChange;
				})
			},
			// * * * Util * * * //
			getRandomRange(min, max) {
				return (Math.random() * (max - min) + min);
			}
		},
		beforeCreate() {},
		beforeMount() {
			this.mapDevicesWatt();
			this.mapDevicesNames();

		},
		mounted() {
			this.randomizeDevicesWatt();
		}
	}
</script>

<style lang=scss scoped>
	.electrolog-dashboard {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		.multi-bar-container {
			display: flex;
			justify-content: center;
			align-items: center;
			.multi-bar {

			}
		}
		.multi-watt-over-time-container {
			display: flex;
			justify-content: center;
			align-items: center;
			.multi-watt-over-time {
			}
		}
	}
</style>
