<script>
    import { formatISO, differenceInCalendarDays, startOfDay, sub } from 'date-fns';

    import Chart from './chart.svelte';
    import ProductPicker from './product_picker.svelte'

    const isoDate = date => formatISO(date, { representation: 'date' });

    let raw_data = [];

    let start_date_picker;
    let end_date_picker;

    let start_date = startOfDay(sub(new Date(), { years: 1 }));
    let end_date = new Date();

    $: picked_start_date = isoDate(start_date);
    $: picked_end_date = isoDate(end_date);

    function generate_data(product) {
        return {
            t: new Date(Date.parse(picked_start_date) + Math.random() * (Date.parse(picked_end_date) - Date.parse(picked_start_date))), 
            y: Math.random() * 100,
        }
    }

    const available_stores = [
        {id: 1, name: "Store 1"},
        {id: 2, name: "Store 2"},
        {id: 3, name: "Store 3"},
    ];
    let selected_store = available_stores[0];
    let selected_products = new Map();

    $: points_to_generate = Math.floor(differenceInCalendarDays(new Date(picked_end_date), new Date(picked_start_date))/30);
    $: raw_data = Array.from(selected_products.values()).map(product => ({
        product: product,
        data: [...Array(points_to_generate)].map(_ => generate_data(product)).sort((a, b) => a.t - b.t),
    }));
</script>

<Chart {raw_data} />
<label>Start date<input bind:this={start_date_picker} type=date bind:value={picked_start_date}></label>
<label>End date<input bind:this={end_date_picker} type=date bind:value={picked_end_date}></label>

<hr>

<select bind:value={selected_store}>
    {#each available_stores as store (store.id)}
    <option value={store}>{store.name}</option>
    {/each}
</select>
<ProductPicker store={selected_store} bind:selected_products />
