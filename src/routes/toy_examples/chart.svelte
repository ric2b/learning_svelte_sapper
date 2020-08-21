<script>
    import { onMount, afterUpdate } from 'svelte';
    import { Chart } from 'chart.js';
    import { formatISO, startOfDay, sub } from 'date-fns';
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

    onMount(async () => {
        let ctx = canvas.getContext('2d');  
        chart = new Chart(ctx, {
            type: 'line',
            options: {
                spanGaps: true,
                tooltips: {
                    mode: 'index',
                    //axis: 'x',
                    intersect: false
                },
                plugins: {
                    colorschemes: {
                        scheme: 'tableau.Classic20' // https://nagix.github.io/chartjs-plugin-colorschemes/
                    },
                    crosshair: {
                        zoom: {
                            enabled: false,
                            //enabled: true,
                            //zoomButtonText: 'Reset Zoom',                       // reset zoom button text
                            //zoomButtonClass: 'reset-zoom',                      // reset zoom button class
                        }
                    }
                }
            }
        });
    })

    afterUpdate(() => {
        const datasets = [...Array(data.length)];
        const xAxes = [...Array(data.length)];

        data.forEach((product_data, i) => {
            datasets[i] = {
                label: `${product_data.product.store.name} - ${product_data.product.name}`,
                //backgroundColor: 'rgb(255, 99, 132)',
                //borderColor: 'rgb(255, 99, 132)',
                data: product_data.data,
                fill: false,
                steppedLine: true, // https://www.chartjs.org/samples/latest/charts/line/stepped.html
            };

            xAxes[i] = {
                type: 'time',
                time: {
                    minUnit: 'day',
                    round: 'day',
                },
                ticks: { 
                    min: min_tick, 
                    max: max_tick, 
                },
                // workaround for zoom from crosshair plugin
                time: {
                    min: min_tick,
                    max: max_tick,
                },
                //distribution: 'linear',
            };
        });

        chart.data.datasets = datasets;
        chart.options.scales.xAxes = xAxes.slice(0, 1);

        if (chart.crosshair.button && chart.crosshair.button.parentNode) {
            chart.crosshair.button.parentNode.removeChild(chart.crosshair.button);
            chart.crosshair.button = false;
        }

        chart.update();
    });
</script>

<canvas bind:this={canvas}></canvas>

<button on:click={()=> { min_tick = null; max_tick = null }}>All</button>
<button on:click={()=> { min_tick = dateSub(new Date(), { years: 1 }); max_tick = new Date(); }}>1Y</button>
<button on:click={()=> { min_tick = dateSub(new Date(), { months: 1 }); max_tick = new Date(); }}>1M</button>
<button on:click={()=> { min_tick = dateSub(new Date(), { weeks: 1 }); max_tick = new Date(); }}>1W</button>
