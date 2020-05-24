<div style="background-color: white">
    <img alt='Svelte icon' width="70px" src='favicon.png'>

	<h1>Svelte to do</h1>

	<ul>
		{#each items as {id, value}, i}
		<li>
		{value} 	<button value={id} on:click={removeItem}>X</button>
	</li>
	
		{/each}
	</ul>
	
	<input bind:value={newItem}>
	<button on:click={addItem}>+</button>
</div>

<script>
	let items = [];
	let newItem;

	function addItem() {
		items = [...items, {id: items.length, value: newItem}]
	}

	function removeItem(event) {
		items = items.filter(item => event.currentTarget.value != item.id)
	}
</script>

<style>
	.done {
		opacity: 0.4;
	}
</style>