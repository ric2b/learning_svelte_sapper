<script>
    import { formatISO, differenceInCalendarDays, min, max, startOfDay, sub } from 'date-fns';

    import Chart from './chart.svelte';

    const isoDate = date => formatISO(date, { representation: 'date' });

    let raw_data = [];

    let start_date_picker;
    let end_date_picker;

    let start_date = startOfDay(sub(new Date(), { years: 4 }));
    let end_date = new Date();

    let picked_start_date = isoDate(start_date);
    let picked_end_date = isoDate(end_date);

    // let min_date;
    // let max_date;

    $: picked_start_date = isoDate(start_date);
    $: picked_end_date = isoDate(end_date);

    // $: min_date = min(raw_data.map(x => x.t))
    // $: max_date = max(raw_data.map(x => x.t))

    // $: if(start_date_picker) { start_date_picker.min = min_date; start_date_picker.max = max_date; }
    // $: if(end_date_picker) { end_date_picker.min = min_date; end_date_picker.max = max_date; }

    function generate_data() {
        return {
            t: new Date(Date.parse(picked_start_date) + Math.random() * (Date.parse(picked_end_date) - Date.parse(picked_start_date))), 
            y: Math.random() * 100 
        }
    }

    $: raw_data = [...Array(Math.floor(differenceInCalendarDays(new Date(picked_end_date), new Date(picked_start_date))/10))].map(_x => generate_data()).sort((a, b) => a.t - b.t);
</script>

<Chart {raw_data}></Chart>
<label>Start date<input bind:this={start_date_picker} type=date bind:value={picked_start_date}></label>
<label>End date<input bind:this={end_date_picker} type=date bind:value={picked_end_date}></label>
