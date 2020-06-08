<script>
    import { onMount, afterUpdate } from 'svelte';
    import { Chart } from 'chart.js'
    import 'chartjs-adapter-date-fns';
    import { formatISO, min, max, startOfDay, sub } from 'date-fns'

    let canvas;
    let start_date = formatISO(new Date(), { representation: 'date' });
    let end_date = formatISO(new Date(), { representation: 'date' });
    let chart;

    function generate_data() {
        return {
            t: new Date(Date.parse('2018') + Math.random() * (Date.parse('2020') - Date.parse('2018'))), 
            y: Math.random() * 100 
        }
    }

    let raw_data = [...Array(200)].map(_x => generate_data()).sort((a, b) => a.t - b.t);
    /*let raw_data = [
        {t: new Date('2013'), y: 0}, 
        {t: new Date('2014'), y: 10}, 
        {t: new Date('2015'), y: 5}, 
        {t: new Date('2016'), y: null}, 
        {t: new Date('2017'), y: 20}, 
        {t: new Date('2018-08-01'), y: 30}, 
        {t: new Date('2019'), y: final_number}
    ];*/

    $: data = raw_data.filter(({t, y}) => Date.parse(start_date) <= t && t <= Date.parse(end_date))
    //$: min_date = new Date(Math.min(...raw_data.map(x => x.t))).toDateString()
    $: min_date = min(raw_data.map(x => x.t))
    $: max_date = max(raw_data.map(x => x.t))

    $: start_date = isoDate(min_date)
    $: end_date = isoDate(max_date)

    let from;
    let to;

    $: if(from) { from.min = isoDate(min_date); from.max = isoDate(max_date); }
    $: if(to) { to.min = isoDate(min_date); to.max = isoDate(max_date); }

    //$: console.log(Date.parse('2018-03-12'))
    $: console.log(startOfDay(sub(new Date(), {weeks: 1})))
    //$: console.log([...Array(5)].map(_x => generate_data()))

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

    const isoDate = date => formatISO(date, { representation: 'date' })
    const dateSub = (date, options) => isoDate(startOfDay(sub(date, options)))
</script>

<canvas bind:this={canvas}></canvas>

<label>Start date<input bind:this={from} type=date bind:value={start_date}></label>
<label>End date<input bind:this={to} type=date bind:value={end_date}></label>

<button on:click={()=> { start_date = dateSub(max_date, { weeks: 1 }) }}>1W</button>
<button on:click={()=> { start_date = dateSub(max_date, { months: 1 }) }}>1M</button>
<button on:click={()=> { start_date = dateSub(max_date, { years: 1 }) }}>1Y</button>
<button on:click={()=> { start_date = isoDate(min_date) }}>All</button>
