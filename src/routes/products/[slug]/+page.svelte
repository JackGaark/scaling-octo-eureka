<script lang="ts">
	import type { ProductNode } from '$lib/models/shopifyTypes';
	import Accordion from '$lib/components/Accordion.svelte';
	import ProductTags from './ProductTags.svelte';
	import { onMount } from 'svelte';
	import MiniProductCard from '$lib/components/MiniProductCard.svelte';
	import FavoriteButton from '$lib/components/FavoriteButton.svelte';
	import { cartStore } from '$lib/store';
	import { addToCart } from '$lib/cart';

	export let data;
	let product: ProductNode;
	$: product = data.product;
	let recommendedProducts;

	onMount(async () => {
		console.log(product);
		const productId = product.id;
		const response = await fetch('/api/getRecommendedProducts', {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({ productId })
		});

		recommendedProducts = await response.json();
	});
</script>

<div class="mt-[40px]">
	<div>
		<a href="/products">
			<button><img src="/icons/arrow.svg" alt="back" /></button>
		</a>

		<div class="flex gap-[32px] mt-[16px]">
			<img
				class="w-[544px] h-[648px] rounded-[6px] bg-no-repeat object-cover"
				src={product?.images?.edges[0]?.node?.originalSrc}
				alt="Wine"
			/>

			<div class="mt-[120px] flex flex-col justify-between">
				<div>
					<ProductTags {product} />
					<div class="od my-[16px] h-[304px] w-[448px] overflow-hidden">
						{@html product?.descriptionHtml ?? ''}
					</div>
				</div>
				<div class="flex gap-[42px] items-center">
					<div class="tpr p-[8px] w-fit" style="border-bottom: 1px solid var(--border--02);">
						<div>
							<span class="tpb">R</span>
							{parseInt(product?.variants?.edges[0]?.node?.priceV2?.amount / 6) ?? 'N/A'}$/btl
						</div>
						<div class="text-contents">
							<span class="tpb">P</span>
							{parseInt(product?.variants?.edges[0]?.node?.priceV2?.amount) ?? 'N/A'}$/btl
						</div>
					</div>
					<button
						on:click={() => {
							addToCart(product, 1);
						}}
						class="button px-[9px] py-[12px] rounded-[4px] border"
					>
						add to cart
					</button>
					<FavoriteButton />
				</div>
			</div>
		</div>
	</div>

	<div class="mt-[48px] flex flex-col gap-[48px]">
		<Accordion title="Try it before you buy it">
			<div>body</div>
		</Accordion>
		<Accordion title="About this producer">
			<div>body</div>
		</Accordion>
		<Accordion title="More from this Producer">
			<div>body</div>
		</Accordion>
		<Accordion title="You might also like">
			{#if recommendedProducts}
				<div class="flex gap-[32px] mt-[16px]">
					{#each recommendedProducts.slice(0, 4) as p}
						<MiniProductCard title={p.handle} image={p.featured_image} id={p.id} />
					{/each}
				</div>
			{/if}
		</Accordion>
	</div>
</div>
