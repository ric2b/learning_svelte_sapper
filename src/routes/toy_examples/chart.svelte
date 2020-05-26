<script>
	import { onMount } from 'svelte';
	// import chartjs from 'chart.js'
	import { Chart } from 'chart.js'

	let canvas;
	let final_number = 5;
	let chart;

	let update_last_element = () => {}
	$: update_last_element(final_number);
	

	onMount(async () => {
		var ctx = canvas.getContext('2d');	
		var chart = new Chart(ctx, {
		    // The type of chart we want to create
		    type: 'line',

		    // The data for our dataset
		    data: {
		        labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
		        datasets: [{
		            label: 'My First dataset',
		            backgroundColor: 'rgb(255, 99, 132)',
		            borderColor: 'rgb(255, 99, 132)',
		            data: [0, 10, 5, null, 20, 30, 45],
		            fill: false
		        }]
		    },

		    // Configuration options go here
		    options: {spanGaps: true}
		});
		
		update_last_element = (value) => {
			chart.data.datasets[0].data[chart.data.datasets[0].data.length - 1] = final_number;
			chart.update();
		}
	})



</script>

<canvas bind:this={canvas}></canvas>

<input type=range bind:value={final_number} min=0 max=100>