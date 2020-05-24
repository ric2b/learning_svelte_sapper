<script>
	let x = 1;
	const increment = () => { x += 1; }
</script>

<h1>FizzBuzz simulator 2000</h1>

<button on:click={increment}>	Increment: {x}</button>
<button on:click={()=>{x = 1}}>	Reset</button>

{#if x % 3 == 0 && x % 5 == 0}
	<h1>FIZZBUZZ</h1>
{:else}
	{#if x % 3 == 0}
		<h1>FIZZ</h1>
	{/if}
	{#if x % 5 == 0}
		<h1>BUZZ</h1>
	{/if}
{/if}
