<script>
	import { onMount, afterUpdate } from 'svelte';
	import { Chart } from 'chart.js'

	let canvas;
	let final_number = 5;
	let chart;
	$: data = [0, 10, 5, null, 20, 30, final_number];

	onMount(async () => {
		let ctx = canvas.getContext('2d');	
		chart = new Chart(ctx, {
		    // The type of chart we want to create
		    type: 'line',

		    // The data for our dataset
		    data: {
		        labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
		        datasets: [{
		            label: 'My First dataset',
		            backgroundColor: 'rgb(255, 99, 132)',
		            borderColor: 'rgb(255, 99, 132)',
		            data: data,
		            fill: false
		        }]
		    },

		    // Configuration options go here
		    options: {spanGaps: true}
		});
	})

	afterUpdate(() => {
		chart.data.datasets[0].data = data
		chart.update();
	});
</script>

<canvas bind:this={canvas}></canvas>

<input type=range bind:value={final_number} min=0 max=100>
