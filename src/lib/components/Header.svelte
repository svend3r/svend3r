<script lang="ts">
	import { goto } from '$app/navigation';

	//fetch github stars
	// const fetchStars = (async () => {
	// 	console.log("fetching")
	// 	const response = await fetch('https://api.github.com/repositories/466166070')
    // return await response.json()
	// })()

</script>

<ul class="tablet:relative w-full tablet:w-full text-center tablet:text-left">
	<a href="/" alt="Svend3r Home"><img alt="svend3 chart library" src="/images/logo-beta.png" class="mx-auto tablet:mx-0"/></a>
	<div class=" text-md text-gray-400">Svelte x D3 "plug and play" charting library</div>
	<li class="tablet:absolute tablet:top-0 tablet:right-0 text-center pt-5  tablet:flex">
		<a 
			on:click={() => goto('/')} 
			class="tablet:text-gray-400 text-gray-100 hover:text-gray-100 pr-5" 
			href="#">Home</a
		>
		<a
			on:click={() => goto('/gettingStarted')}
			class="tablet:text-gray-400 text-gray-100 hover:text-gray-100 pr-5"
			href="#">Getting Started</a
		>
		<a
			on:click={() => goto('/contributors')}
			class="tablet:text-gray-400 text-gray-100 hover:text-gray-100 tablet:pr-5"
			href="#">Contributors</a
		>
		<div id="widget" class="tablet:flex justify-center">
			<a href="https://github.com/oslabs-beta/svend3r" alt="Svend3r Github Home"><img class="tablet:mr-2 tablet:my-0 my-2 mx-auto" alt="svend3 chart library" src="/images/github2.png" /></a>
			<section class="flex justify-center">
				<button 
					id="star-button" 
					class="bg-white w-[60px] flex justify-center rounded-l-md items-center"
					on:click={() => location.href = "https://github.com/oslabs-beta/svend3r"} 
					target="_blank"
				>
						<svg
							viewBox="0 0 16 16"
							width="14"
							height="14"
							class="octicon octicon-star"
							aria-hidden="true"
						>
							<path
							fill-rule="evenodd"
							d="M8 .25a.75.75 0 01.673.418l1.882 3.815 4.21.612a.75.75 0 01.416 1.279l-3.046 2.97.719 4.192a.75.75 0 01-1.088.791L8 12.347l-3.766 1.98a.75.75 0 01-1.088-.79l.72-4.194L.818 6.374a.75.75 0 01.416-1.28l4.21-.611L7.327.668A.75.75 0 018 .25zm0 2.445L6.615 5.5a.75.75 0 01-.564.41l-3.097.45 2.24 2.184a.75.75 0 01.216.664l-.528 3.084 2.769-1.456a.75.75 0 01.698 0l2.77 1.456-.53-3.084a.75.75 0 01.216-.664l2.24-2.183-3.096-.45a.75.75 0 01-.564-.41L8 2.694v.001z"
							>
						</svg>
					<span>&nbsp;Star</span>
				</button>
				<button
					id="star-count"
					class="bg-white flex w-[60px] flex justify-center rounded-r-md border-l-2 border-bgGrey"
					on:click={() => location.href = "https://github.com/oslabs-beta/svend3r/stargazers"}
					target="_blank"
				>1000
					<!-- {#await fetchStars}
						loading
					{:then data}
						{data.stargazers_count}
					{:catch error}	
						error
					{/await} -->
				</button>
			</section>
		</div>
	</li>
	<hr
		class="mt-5 bg-gray-200 dark:bg-gray-200 
	border border-gray-200 dark:border-gray-200 rounded-full
	 w-full shadow-3xl"
	/>
</ul>

<style>

</style>
