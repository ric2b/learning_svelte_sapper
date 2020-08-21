<script type="text/javascript">
    export let store;
    export const selected_products = new Map();

    $: available_products = [
        {id: `${store.id}-1`, store: store, name: "Pringle 1"}, 
        {id: `${store.id}-2`, store: store, name: "Pringle 2"}, 
        {id: `${store.id}-3`, store: store, name: "Lays"},
    ];

    let search_expression = "";

    $: search_results = available_products.filter(({id, name}) => name.startsWith(search_expression));

    function select_product(product) {
        selected_products.set(product.id, product);
        selected_products = selected_products;
    }

    function unselect_product(product) {
        selected_products.delete(product.id);
        selected_products = selected_products;
    }
</script>

<label>Search
    <input type="text" placeholder="Search products..." bind:value={search_expression}>
</label>

{#if search_expression}
<figure>
    <figcaption>Results</figcaption>
    <ul>
        {#each search_results as product (product.id)}
        <li>
            <label>
                {product.name} 
                <button on:click={() => select_product(product)} disabled={selected_products.has(product.id)}>+</button>
            </label> 
        </li> 
        {/each}
    </ul>
</figure>
{/if}

{#if selected_products.size > 0 }
<figure>
    <figcaption>Selected products</figcaption>
    <ul>
        {#each [...selected_products.values()] as product (product.id)}
        <li> <label>
            {product.store.name} - {product.name} <button on:click={() => unselect_product(product)}>x</button>
        </label> </li> 
        {/each}
    </ul>
</figure>
{/if}