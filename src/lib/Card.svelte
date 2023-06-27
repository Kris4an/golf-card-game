<script lang="ts">
	import CardPaint from './CardPaint.svelte';

	export let isRevealed: boolean;
	export let card: number;
	export let isInteractable = false;
	export let isSelected = false;
	export let cardColor: string;
	const cards = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
	$: paintColor = card > 99 && card < 299 ? '#ff0000' : '#000000';
	$: cursor = isInteractable ? 'pointer' : 'default';
	$: shadow = isSelected ? '0px 0px 4px 4px ' + cardColor : '0px 4px 4px rgba(0, 0, 0, 0.25)';
</script>

<button class="mainHolder" style="--cursor: {cursor}; --shadow: {shadow}" on:click>
	{#if isRevealed}
		<div class="holder1">
			<span class="cardValue" style="--theme-color: {paintColor}"
				><b>{cards[(card % 100) - 1]}</b></span
			>
			<CardPaint {card} />
		</div>
		<div id="middlePaint">
			<CardPaint {card} />
		</div>
		<div class="holder1" style="transform: rotate(180deg); --justify-content: flex-end;">
			<span class="cardValue" style="--theme-color: {paintColor}"
				><b>{cards[(card % 100) - 1]}</b></span
			>
			<CardPaint {card} />
		</div>
	{:else}
		<div class="cardBack" style="--card-color: {cardColor}">⨉</div>
	{/if}
</button>

<style>
	.mainHolder {
		aspect-ratio: 0.69;
		width: 142px;
		background-color: #ffffff;
		border: 1px solid #000000;
		box-shadow: var(--shadow);
		border-radius: 18px;
		display: flex;
		justify-content: space-between;
		align-items: center;
		flex-direction: column;
		padding: 0.2rem 0.5rem 0.2rem 0.5rem;
		cursor: var(--cursor);
	}
	.holder1 {
		width: 100%;
		display: flex;

		align-items: center;
		gap: 0.5rem;
	}
	div.cardBack {
		width: 90%;
		height: 100%;
		border-style: solid;
		border-color: var(--card-color);
		border-radius: 18px;
		align-self: center;
		display: flex;
		align-items: center;
		justify-content: center;
		color: var(--card-color);

		user-select: none;
	}
	.cardValue {
		color: var(--theme-color);
		user-select: none;
	}
	@media only screen and (max-width: 1200px) {
		.mainHolder {
			width: 120px;
			max-width: 23vw;
		}
		.cardBack {
			margin: 0.5rem;
			border: 5px;
			font-size: 30px;
		}
		.cardValue {
			font-size: 40px;
		}
	}
	@media only screen and (min-width: 1201px) {
		.cardBack {
			margin: 0.5rem;
			border: 5px;
			font-size: 40px;
		}
		.cardValue {
			font-size: 50px;
		}
	}
	@media only screen and (max-width: 540px) {
		.cardBack {
            margin: 0.3rem;
			width: 95%;
			border: 3px;
			font-size: 20px;
		}
		.cardValue {
			font-size: 30px;
		}
		#middlePaint {
			scale: 0.8;
		}
		.holder1 {
			gap: 5px;
		}
	}
	@media only screen and (max-width: 400px) {
        .cardValue {
            font-size: 26px;
        }
		.holder1 {
			gap: 0px;
		}
    }
</style>
