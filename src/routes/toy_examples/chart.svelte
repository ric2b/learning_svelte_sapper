<script>
    import { onMount, afterUpdate } from 'svelte';
    import { Chart } from 'chart.js';
    import { formatISO, min, max, startOfDay, sub } from 'date-fns';
    import 'chartjs-adapter-date-fns';
    import 'chartjs-plugin-colorschemes';
    import 'chartjs-plugin-crosshair';

    export let raw_data;

    const dateSub = (date, options) => startOfDay(sub(date, options));

    let canvas;
    let chart;

    let min_tick;
    let max_tick;

    $: data = raw_data//.filter(({t, y}) => Date.parse(start_date) <= t && t <= Date.parse(end_date))

    //$: if(chart) { console.log('hey'); chart.data.datasets[0].data = data; chart.update(); }

    onMount(async () => {
        let ctx = canvas.getContext('2d');  
        chart = new Chart(ctx, {
            type: 'line',
            data: {
                datasets: [{
                    label: 'My First dataset',
                    //backgroundColor: 'rgb(255, 99, 132)',
                    //borderColor: 'rgb(255, 99, 132)',
                    data: data,
                    fill: false,
                    steppedLine: true // https://www.chartjs.org/samples/latest/charts/line/stepped.html
                }]
            },
            options: {
                spanGaps: true,
                scales: {
                    xAxes: [{
                        type: 'time',
                        time: {
                            minUnit: 'day',
                            round: 'day',
                        },
                        ticks: { 
                            min: min_tick, 
                            max: max_tick, 
                        }
                        //distribution: 'linear'
                    }]
                },
                tooltips: {
                    mode: 'index',
                    axis: 'x',
                    intersect: false
                },
                plugins: {
                    colorschemes: {
                        scheme: 'tableau.Classic20' // https://nagix.github.io/chartjs-plugin-colorschemes/
                    },
                    crosshair: {
                        zoom: {
                            enabled: true,
                            //zoomButtonText: 'Reset Zoom',                       // reset zoom button text
                            //zoomButtonClass: 'reset-zoom',                      // reset zoom button class
                        }
                    }
                }
            }
        });
    })

    afterUpdate(() => {
        chart.data.datasets[0].data = data
        chart.options.scales.xAxes[0].ticks.min = min_tick;
        chart.options.scales.xAxes[0].ticks.max = max_tick;
        chart.update();
    });
</script>

<canvas bind:this={canvas}></canvas>

<button on:click={()=> { min_tick = null; max_tick = null }}>All</button>
<button on:click={()=> { min_tick = dateSub(new Date(), { years: 1 }); max_tick = new Date(); }}>1Y</button>
<button on:click={()=> { min_tick = dateSub(new Date(), { months: 1 }); max_tick = new Date(); }}>1M</button>
<button on:click={()=> { min_tick = dateSub(new Date(), { weeks: 1 }); max_tick = new Date(); }}>1W</button>
