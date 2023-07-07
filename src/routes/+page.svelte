<script lang="ts">
	import { onMount } from 'svelte';
	import ShareIcon from '../components/ShareIcon.svelte';
	import LoadingIcon from '../components/LoadingIcon.svelte';
	import ReloadIcon from '../components/ReloadIcon.svelte';

	interface Cat {
		url: string;
	}
	let cat: Cat = { url: '' };
	let shareData: ShareData;
	let extraCute: boolean = false;
	let url: string = 'https://cataas.com/cat?json=true';
	let cuteUrl: string = 'https://cataas.com/cat/cute?json=true';
	onMount(() => {
		getCatIMG();
	});
	function shareIt() {
		if (navigator.canShare && navigator.canShare(shareData)) {
			navigator.share(shareData);
		}
	}
	function getCatIMG() {
		document.querySelector('img#image')?.classList.add('hidden');
		document.querySelector('div#loading')?.classList.remove('hidden');
		document.querySelector('div#loading')?.classList.add('flex');
		fetch(extraCute ? cuteUrl : url)
			.then((res) => res.json())
			.then((out) => {
				cat = out;
				document.querySelector('img#image')?.classList.remove('hidden');
				document.querySelector('div#loading')?.classList.add('hidden');
				document.querySelector('div#loading')?.classList.remove('flex');
				console.log(cat);

				blobIt(`https://cataas.com${cat.url}`);
				// console.log(cat);
			})
			.catch((err) => {
				throw err;
			});
	}
	function blobIt(url: string) {
		fetch(url)
			.then((res) => res.blob())
			.then((blob) => {
				let file = new File([blob], 'cat.jpg', { type: 'image/jpeg' });
				let filesArray = [file];
				shareData = {
					title: '너무 귀여운 고양이',
					text: '너무 귀여운 고양이 사진',
					url: `https://cataas.com${cat.url}`,
					files: filesArray
				};
			});
	}
	// console.log(cat);
</script>

<main>
	<div class="flex flex-col items-start">
		<img src={`https://cataas.com${cat.url}`} alt="고양이 사진" height="400" id="image" />
		<div id="loading" class="w-[400px] h-[400px] hidden justify-center items-center">
			<LoadingIcon />
		</div>
		<div class="flex flex-row space-x-10 items-center justify-end">
			<button class="text-black dark:text-white border w-fit h-fit" on:click={shareIt}
				><ShareIcon /></button
			>
			<button class="text-black dark:text-white border w-fit h-fit" on:click={getCatIMG}
				><ReloadIcon /></button
			>
			<div class="flex">
				<label for="extracute" class="dark:text-white text-black text-xl">추가 귀여움</label>
				<input type="checkbox" bind:checked={extraCute} on:change={getCatIMG} class="ml-2.5" />
			</div>
		</div>
	</div>
</main>

<style>
</style>
