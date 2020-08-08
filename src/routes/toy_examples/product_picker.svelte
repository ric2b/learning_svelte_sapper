<script type="text/javascript">
    export const selected_products = new Map();

    const products = [
        {id: 1, name: "Pringle 1"}, 
        {id: 2, name: "Pringle 2"}, 
        {id: 3, name: "Lays"},
    ];

    let search_expression = "";

    $: search_results = products.filter(({id, name}) => name.startsWith(search_expression));

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
        <li> <label>
            {product.name} <button on:click={() => select_product(product)}>+</button>
        </label> </li> 
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
            {product.name} <button on:click={() => unselect_product(product)}>x</button>
        </label> </li> 
        {/each}
    </ul>
</figure>
{/if}