<script lang="ts">
	import CardPaint from "./CardPaint.svelte";
    
    export let isRevealed: boolean;
    export let card: number;
    export let isInteractable = false;
    export let isSelected = false;
    export let cardColor: string;
    const cards = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
    $:paintColor = (card>99 && card<299)? '#ff0000':'#000000';
    $:cursor = isInteractable? 'pointer':'default';
    $:shadow = isSelected? '0px 0px 4px 4px #0f6f84':'0px 4px 4px rgba(0, 0, 0, 0.25)';
</script>
<style>
    button.mainHolder {
        width: 143px;
        height: 208px;
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
    div.holder1 {
        width: 100%;
        display: flex;
        
        align-items: center;
        gap: 0.5rem;
    }
    div.cardBack {
        width: 90%;
        height: 100%;
        border: 5px;
        border-style: solid;
        border-color: var(--card-color);
        border-radius: 18px;
        margin: 0.5rem;
        align-self: center;
        display: flex;
        align-items: center;
        justify-content: center;
        color: var(--card-color);
        font-size: 40px;
        user-select: none;
    }
    span {
        font-size: 50px;
        color: var(--theme-color);
        user-select: none;
    }
</style>

<button class="mainHolder" style="--cursor: {cursor}; --shadow: {shadow}" on:click>
    {#if isRevealed}
    <div class="holder1">
        <span style="--theme-color: {paintColor}"><b>{cards[card%100-1]}</b></span>
        <CardPaint card={card}/>
    </div>
    <CardPaint card={card}/>
    <div class="holder1" style="transform: rotate(180deg); --justify-content: flex-end;">
        <span style="--theme-color: {paintColor}"><b>{cards[card%100-1]}</b></span>
        <CardPaint card={card}/>
    </div>
    {:else}
    <div class="cardBack" style="--card-color: {cardColor}">⨉</div>
    {/if}
</button>