<script lang="ts">
import { teleport } from "$lib/utils/teleport.js";

type FilterInfo = {
	name: string;
	list: string[]
}

export let info: FilterInfo 
$: open = false
$: filter = null
$: selected = []

const selectItem = (item: number) => {
	console.log(item, selected)
	if(selected.includes(item)) {
		selected = selected.filter(i => i !== item)
	} else {
		selected = [...selected, item]
	}
}
</script>

<div class="filter">
	<button bind:this={filter} class="filter-header" on:click={() => {open = !open}}>
		{info.name} {selected.length > 0 ? `(${selected.length})` : ''}
		<div class="filter-icon">{open ? '-' : '+'}</div>
	</button>
	{#if open}
		<div class="filter-list" 
		style="
			top: {filter.getBoundingClientRect().top + window.scrollY + filter.getBoundingClientRect().height}px;
			left: {filter.getBoundingClientRect().left}px;
		"
			use:teleport={'teleport'}
		>
			{#each info.list as item, index}
				<button class="filter-item" on:click={() => selectItem(index)} class:selected={selected.includes(index)}>
					{#if selected.includes(index)}
						<span class="filter-item-icon">X</span>
					{/if}
					{item}
				</button>
			{/each}
		</div>
	{/if}
</div>

<style>
.filter-header {
	@apply flex items-center justify-center bg-[#fff] text-[12px] border-t w-[176px] h-[24px];
	cursor: pointer;
	position: relative;
	line-height: 150%;
}
.filter-header:hover {
	border-color: #2D63B0;
	color: #2D63B0;
}
.filter-list {
	display: flex;
	flex-direction: column;
	position: absolute;
	width: 176px;
	background: #fff;
	padding: 10px 6px 3px 13px;
	max-height: 132px;
	overflow: auto;
}
.filter-item {
	color: #000;
	font-family: Overpass;
	font-size: 12px;
	font-style: normal;
	font-weight: 400;
	line-height: 150%; /* 18px */
	letter-spacing: -0.132px;
	text-align: left;
	position: relative;
}
.filter-item.selected {
	color: #C96198;
}
.filter-item-icon {
	position: absolute;
	top: 0;
	left: -11px;
}
.filter-icon {
	position: absolute;
	right: 2px;
	top: 2px;
	width: 14px;
	color: inherit;
	text-align: center;
	font-family: Overpass;
	font-size: 12px;
	font-style: normal;
	font-weight: 500;
	line-height: 150%; /* 18px */
	letter-spacing: -0.132px;
	text-transform: uppercase;
	
}

</style>