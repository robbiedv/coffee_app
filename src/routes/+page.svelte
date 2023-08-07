<script lang="ts">
	import dayjs from 'dayjs';

	let beans = 18;
	let ratio = 18;
	let water = beans * ratio;
	let grind = 6;
	let recipes: RecipeProps[] = [];
	let startTime: number;
	let intervalId: number;
	let stopwatch: string = '00:00';
	let notes = '';
	let currentRecipe: RecipeProps = {
		beans: 0,
		ratio: 0,
		water: 0,
		grind: 0,
		time: '',
		created: '',
		notes: ''
	};

	interface RecipeProps {
		beans: number;
		ratio: number;
		water: number;
		grind: number;
		time: string;
		created: string;
		notes: string;
	}

	function setCurrentRecipe(index: number) {
		currentRecipe = recipes[index];
	}

	function addRecipe() {
		const data = {
			beans,
			ratio,
			water: beans * ratio,
			grind,
			time: stopwatch,
			created: dayjs().format('ddd hh:mma'),
			notes: ''
		};
		recipes = [...recipes, data];
	}

	function updateStopwatch() {
		const currentTime = Date.now() - startTime;
		const minutes = Math.floor((currentTime % 3600000) / 60000);
		const seconds = Math.floor((currentTime % 60000) / 1000);
		stopwatch = `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
	}

	function startStop() {
		if (intervalId > 0) {
			clearInterval(intervalId);
			intervalId = 0;
		} else {
			startTime = Date.now() - (intervalId ? intervalId : 0);
			intervalId = setInterval(updateStopwatch, 1000);
		}
	}

	function reset() {
		stopwatch = '00:00';
	}
</script>

<main class="flex container mx-auto h-screen">
	<aside class="max-w-[400px] min-w-[200px] my-6">
		<div class="flex mb-8">
			<div>
				<h1 class="font-bold text-lg text-slate-300">Welcome Friend!</h1>
				<p class="text-slate-300 text-xs italic">
					recipes created will not <br /> be saved after tab is closed
				</p>
			</div>
		</div>
		<h2 class="font-bold text-slate-300">History</h2>
		<hr />
		<ul class="text-slate-300 mt-2">
			{#each recipes.reverse() as recipe, index}
				<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
				<!-- svelte-ignore a11y-click-events-have-key-events -->
				<li
					on:click={() => setCurrentRecipe(index)}
					class="py-1 px-4 ml-2 mr-4 hover:bg-slate-700 rounded-md hover:cursor-pointer"
				>
					{recipe.created}
				</li>
			{/each}
		</ul>
	</aside>
	<section class="grow bg-slate-900 rounded-2xl m-6 p-6 text-slate-300 grid grid-rows-2">
		<div>
			<div class="flex justify-between">
				<h1 class="text-md font-bold">Recipe Parameters</h1>
				<button
					on:click={addRecipe}
					class="border border-slate-700 px-4 py-1 rounded-md hover:bg-slate-800">Save</button
				>
			</div>
			<div class="flex justify-around my-20">
				<div class="w-[300px]">
					<label for="beans">Bean Weight</label>
					<br />
					<input id="beans" bind:value={beans} type="range" min="16" max="32" name="Bean Weight" />
					<span>{beans}</span>
					<br />
					<label for="ratio">Ratio</label>
					<br />
					<input id="ratio" bind:value={ratio} type="range" min="12" max="20" />
					<span>1/{ratio}</span>
					<br />
					<label for="grind">Grind Setting</label>
					<br />
					<input id="grind" bind:value={grind} type="range" min={1} max={20} />
					<span>{grind}</span>
					<br />
					{#if intervalId > 0}
						<button
							on:click={startStop}
							class="border border-slate-700 bg-slate-700 px-4 py-1 rounded-md hover:bg-slate-800 w-[100px] mt-4"
							>stop</button
						>
					{:else}
						<button
							on:click={startStop}
							class="border border-slate-700 bg-slate-700 px-4 py-1 rounded-md hover:bg-slate-800 w-[100px] h-[32px] mt-4"
							>start</button
						>
					{/if}
					{#if stopwatch !== '00:00' && intervalId === 0}
						<button
							on:click={reset}
							class="border border-slate-700 px-4 py-1 rounded-md hover:bg-slate-800 w-[100px] mt-4"
							>clear</button
						>
					{/if}
				</div>
				<div
					class="border-8 rounded-full border-slate-800 h-[200px] w-[200px] grid place-items-center text-slate-300 text-xl"
				>
					{stopwatch}
				</div>
			</div>
			<hr class="border-t border-slate-700" />
		</div>

		<div class="flex">
			<div>
				<h1 class="mt-10 mr-40 mb-6 text-md font-bold">Recipe Details</h1>
				{#if currentRecipe.beans}
					<ul class="ml-4">
						<li class="py-1">Bean Weight: {currentRecipe.beans}g</li>
						<li class="py-1">Ratio: 1/{currentRecipe.ratio}</li>
						<li class="py-1">Water Weight: {currentRecipe.water}g</li>
						<li class="py-1">Grind Setting: {currentRecipe.grind}</li>
						<li class="py-1">Brew Time: {currentRecipe.time}</li>
					</ul>
				{/if}
			</div>
			<div class="mt-10">
				{#if currentRecipe.beans}
					<label for="notes" class="text-sm">Tasting Notes</label>
					<br />
					<textarea id="notes" class="bg-slate-800 p-2" bind:value={currentRecipe.notes} />
				{/if}
			</div>
		</div>
	</section>
</main>
