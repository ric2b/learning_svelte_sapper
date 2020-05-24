<script >
	export let segment;
</script>

<h1>Toy examples</h1>

<div class="submenu">
	<a class:selected={segment === "todo"} href="toy_examples/todo">Todo</a>
	<a class:selected={segment === "FizzBuzz"} href="toy_examples/FizzBuzz">FizzBuzz</a>
	<a class:selected={segment === "chart"} href="toy_examples/chart">chart</a>
</div>

<slot></slot>

<style>
	.selected {
		color: red;
	}
</style>