<script lang="ts">
	import Map, { type Brand } from '../components/Map.svelte';
	import mapData from '../components/data.json';

	let brandDialog: HTMLDialogElement;
	let brand: Brand;

	function showBrand(brandToShow: Brand) {
		brand = brandToShow;
		brandDialog.showModal();
	}

	function getProvinceFromKey(provinceKey: string) {
		return mapData[provinceKey as keyof typeof mapData].name;
	}

	interface ClickOutsideNode extends HTMLElement {
		contains: (element: Node) => boolean;
		dispatchEvent: (event: Event) => boolean;
	}

	export function clickOutside(node: ClickOutsideNode): { destroy: () => void } {
		const handleClick = (event: MouseEvent) => {
			if (!node.contains(event.target as Node)) {
				node.dispatchEvent(new CustomEvent('outclick'));
			}
		};

		document.addEventListener('click', handleClick, true);

		return {
			destroy() {
				document.removeEventListener('click', handleClick, true);
			}
		};
	}
</script>

<main class="bg-blue-200 h-[100svh] relative">
	<h1 class="absolute top-8 left-8 font-sans text-2xl font-bold">Open Alcohol Map</h1>
	<Map onBrandClick={(brand) => showBrand(brand)} />

	<dialog bind:this={brandDialog} class="container">
		<div
			use:clickOutside
			on:outclick={() => brandDialog.close()}
			class="flex flex-col items-center gap-2"
		>
			<button class="absolute top-2 right-2 p-2" on:click={() => brandDialog.close()}>X</button>

			{#if brand}
				<h1 class="text-2xl">{brand.name}</h1>

				{#if brand.image}
					<img src={brand.image} alt={brand.name} class="w-32 mx-auto mt-2" />
				{/if}

				<!-- Province -->
				{#if getProvinceFromKey(brand.province)}
					<p class="text-xl">จังหวัด: {getProvinceFromKey(brand.province)}</p>
				{/if}

				<!-- {#if brand.description} -->
				<!-- <p>{brand.description}</p> -->
			{/if}

			<form on:submit={() => brandDialog.close()}>
				<button class="mt-4 border rounded-sm px-4 py-2">ปิด</button>
			</form>
		</div>
	</dialog>
</main>
