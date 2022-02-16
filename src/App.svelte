<script>
	// import packages
	import { csv } from "d3";

	// Import components
		import LineChart from "./LineChart.svelte";
		

	let dataset = get_csv();

    
	// NEW SOLUTION: Await is more stable and allows for error messaging upon render.
	async function get_csv() {
		const dataset = await csv("data/film_permits.csv", (d) => {
			return d;
		});
		return dataset;
	}

	// OLD SOLUTION: Reactive variable that updates when data repopulates.
	// $:    csv("data/piv_dataset.csv").then((data) => {
	// 	dataset = data;
	// }
	// )

	let width;
	

</script>

<main style="width: {width}">	
	
	 <h1>NYC Film permit grants over the pandemic, 2020-2021.</h1>
	<p>Number of granted film, TV, and theatre permits by month.</p>

	<div id = "line_container" style="width: {width}; overflow: show;">
		<div id = "line_chart" bind:clientWidth={width}  style="width: {width}">
			{#await dataset}
				<p>...waiting</p>
			{:then dataset}
					{#if dataset.length != 0}
						<LineChart {dataset} {width}/>
					{/if}
			{:catch error}
				<p style="color: red">{error.message}</p>
			{/await}

		</div>
	</div>

	<!-- OLD SOLUTION -->
	<!-- <h1>NYC Film permit grants over the pandemic, 2020-2021.</h1>
	<p>Description tbd</p>
	<div id = "line_container">
		<div id = "line_chart">
			{#if dataset.length > 0}
			<LineChart {dataset} />
			{/if}
		</div>
	</div> -->
	
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 840px;
		margin: 0 auto;
		height: 900px;
	}

	#line_container{
		width: 80%;
		max-width: 700px; 
		height: 85%;
		margin-left: auto;
		margin-right: auto;
		display: block;
	}

	h1 {
		color: black;
		/* text-transform: uppercase; */
		font-size: 2em;
		font-weight: 200;
		min-width: 70%;
	}

	@media (min-width: 500px) {
		main {
			max-width: none;
		}

	}

	@media (max-width: 500px) {

	#line_container{
		width: 110%;
		max-width: 700px; 
		height: 80%;
		margin-left: auto;
		margin-right: auto;
		display: block;
	}
}


@media (max-width: 400px) {

#line_container{
	width: 120%;
	max-width: 700px; 
	height: 80%;
	margin-left: -1rem;
	margin-right: auto;
	display: block;
}


}


</style>