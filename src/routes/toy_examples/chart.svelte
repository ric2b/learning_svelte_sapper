<script>
	import { onMount, afterUpdate } from 'svelte';
	import { Chart } from 'chart.js'

	let canvas;
	let final_number = 5;
	let chart;
	$: data = [
		{t: new Date('2013'), y: 0}, 
		{t: new Date('2014'), y: 10}, 
		{t: new Date('2015'), y: 5}, 
		{t: new Date('2016'), y: null}, 
		{t: new Date('2017'), y: 20}, 
		{t: new Date('2018-08-01'), y: 30}, 
		{t: new Date('2019'), y: final_number}
	];
	

	onMount(async () => {
		let ctx = canvas.getContext('2d');	
		chart = new Chart(ctx, {
		    // The type of chart we want to create
		    type: 'line',

		    // The data for our dataset
		    data: {
		        datasets: [{
		            label: 'My First dataset',
		            backgroundColor: 'rgb(255, 99, 132)',
		            borderColor: 'rgb(255, 99, 132)',
		            data: data,
		            fill: false,
		            steppedLine: true // https://www.chartjs.org/samples/latest/charts/line/stepped.html
		        }]
		    },

		    // Configuration options go here
		    options: {
		    	spanGaps: true,
		        scales: {
		            xAxes: [{
		                type: 'time',
		                //distribution: 'linear'
		            }]
        		}
    		}
		});
	})

	afterUpdate(() => {
		chart.data.datasets[0].data = data
		chart.update();
	});
</script>

<canvas bind:this={canvas}></canvas>

<input type=range bind:value={final_number} min=0 max=100>
