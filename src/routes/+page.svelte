<script lang='ts'>
	import { onMount } from 'svelte';
    import Card from '../lib/Card.svelte';

    let orderedCardDeck = [{ card: 1, isRevealed: false }, { card: 2, isRevealed: false }, { card: 3, isRevealed: false }, { card: 4, isRevealed: false }, { card: 5, isRevealed: false }, { card: 6, isRevealed: false }, { card: 7, isRevealed: false }, { card: 8, isRevealed: false }, { card: 9, isRevealed: false }, { card: 10, isRevealed: false }, { card: 11, isRevealed: false }, { card: 12, isRevealed: false }, { card: 13, isRevealed: false }, { card: 101, isRevealed: false }, { card: 102, isRevealed: false }, { card: 103, isRevealed: false }, { card: 104, isRevealed: false }, { card: 105, isRevealed: false }, { card: 106, isRevealed: false }, { card: 107, isRevealed: false }, { card: 108, isRevealed: false }, { card: 109, isRevealed: false }, { card: 110, isRevealed: false }, { card: 111, isRevealed: false }, { card: 112, isRevealed: false }, { card: 113, isRevealed: false }, { card: 201, isRevealed: false }, { card: 202, isRevealed: false }, { card: 203, isRevealed: false }, { card: 204, isRevealed: false }, { card: 205, isRevealed: false }, { card: 206, isRevealed: false }, { card: 207, isRevealed: false }, { card: 208, isRevealed: false }, { card: 209, isRevealed: false }, { card: 210, isRevealed: false }, { card: 211, isRevealed: false }, { card: 212, isRevealed: false }, { card: 213, isRevealed: false }, { card: 301, isRevealed: false }, { card: 302, isRevealed: false }, { card: 303, isRevealed: false }, { card: 304, isRevealed: false }, { card: 305, isRevealed: false }, { card: 306, isRevealed: false }, { card: 307, isRevealed: false }, { card: 308, isRevealed: false }, { card: 309, isRevealed: false }, { card: 310, isRevealed: false }, { card: 311, isRevealed: false }, { card: 312, isRevealed: false }, { card: 313, isRevealed: false }];
    let cardDeck = [{ card: 501, isRevealed: false }];
    let p2 = [{ card: 501, isRevealed: false }, { card: 501, isRevealed: false }, { card: 501, isRevealed: false }, { card: 501, isRevealed: false }, { card: 501, isRevealed: false }, { card: 501, isRevealed: false }];
    let p1 = [{ card: 501, isRevealed: false }, { card: 501, isRevealed: false }, { card: 501, isRevealed: false }, { card: 501, isRevealed: false }, { card: 501, isRevealed: false }, { card: 501, isRevealed: false }];
    let pile = [{ card: 501, isRevealed: false }];
    let revealedCards = 0, p2RevealedCards = 0;
    let isRevealing = true, isTurn = true, isNew = true, isNewCardSelected = false;;
    let p1Score = 0, p2Score = 0;
    $: if(cardDeck.length == 0) endGame();
    $: if(!isTurn) isNewCardSelected = false;
    $: updateResults("p1", p1);
    $: updateResults("p2", p2);

    type PlayingCard = {
        card: number;
        isRevealed: boolean;
    }
    function updateResults(p: string, arr: PlayingCard[]) {
        switch(p){
            case 'p1': {
                revealedCards = 0;
                p1.forEach((card) => {if(card.isRevealed) revealedCards++})
                p1Score = calculateScore(p1);
                if(revealedCards == p1.length) endGame();
                break;
            }
            case 'p2': {
                p2RevealedCards = 0;
                p2.forEach((card) => {if(card.isRevealed) p2RevealedCards++})
                p2Score = calculateScore(p2);
                if(p2RevealedCards == p2.length) endGame();
                break;
            }
        }
    }
    function shuffleDeck(){
        cardDeck = orderedCardDeck;
        p2 = [];
        p1 = [];
        pile = [];  
        for(let i = 0; i < cardDeck.length; i++){
            let a:number = Math.floor(Math.random() * 52);
            let t = cardDeck[i];
            cardDeck[i] = cardDeck[a];
            cardDeck[a] = t;
        }
    }
    onMount (async () => {
        startGame();
    });
    function startGame() {
        shuffleDeck();
        for(let i = 0; i < 6; i++){
            p2.push(cardDeck.pop()!);
            p1.push(cardDeck.pop()!);
        }
        let t = cardDeck.pop()!;
        t = {card: t.card, isRevealed: true};
        pile.push(t);
    }
    function revealCard(n: number): boolean {
        if(p1[n].isRevealed) return false;
        p1[n] = {card: p1[n].card, isRevealed:true};
        p1Score = calculateScore(p1);
        return true;
    }
    function endGame() {
        console.log(revealedCards + "  " + p2RevealedCards);
        if(p1Score == p2Score){
            alert("Draw");
            return;
        }
        if(p1Score < p2Score){
            alert("You win!");
            return;
        }
        alert("You lose!");
    }
    function cardScore(a: number):number {
        const b = a%100;
        switch(b){
            case 2: return -2;
            case 11: return 10;
            case 12: return 10;
            case 13: return 0;
            default: return b;
        }
    }
    function calculateScore(arr: PlayingCard[]): number {
        let score = 0;
        switch(arr.length){
            case 6: {
                for(let i = 0; i < 3; i++){
                    if(arr[i].isRevealed && arr[i+3].isRevealed){
                        if(arr[i].card%100 != arr[i+3].card%100){
                            score+=cardScore(arr[i].card);
                            score+=cardScore(arr[i+3].card);
                        }
                    }
                    else {
                        if(arr[i].isRevealed) score+=cardScore(arr[i].card);
                        if(arr[i+3].isRevealed) score+=cardScore(arr[i+3].card);
                    }
                }
                break;
            }
        }
        return score;
    }
    function handlePlayerCardClick(n:number) {
        if(!isTurn) return;
        if(revealedCards<2) {
            if(!revealCard(n)) return;
            revealedCards++;
            if(revealedCards==2) {
                isTurn=false;
                computerRevealStartCards();
                return;
            }
            else return;
        }
        else {
            if(isRevealing) {
                if(revealCard(n)) revealedCards++;
                if(revealedCards == p1.length) endGame();
            }
            else {
                //if(!p1[n].isRevealed) revealedCards++;
                switch(isNew) {
                    case true:{
                        pile = [...pile, p1[n]];
                        pile[pile.length-1].isRevealed = true;
                        p1[n] = cardDeck.pop()!;
                        if(cardDeck.length == 0) endGame();
                        cardDeck[0].isRevealed = false;
                        break;
                    }
                    case false:{
                        let t = p1[n];
                        p1[n] = pile.pop()!;
                        pile[pile.length-1] = t;
                        pile[pile.length-1].isRevealed = true;
                        break;
                    }
                }
                isRevealing = true;
                p1Score = calculateScore(p1);
                if(revealedCards == p1.length) endGame();
            }
        }
        isTurn = false;
        isNew = true;
        if(revealedCards >= 2) setTimeout(() => computerMove(), 500);
    }
    function placeCard() {
        pile = [...pile, cardDeck.pop()!];
        if(cardDeck.length == 0) endGame();
        cardDeck[0].isRevealed = false;
        isTurn = false;
        setTimeout(() => computerMove(), 500);
    }

    function revealComputerCard(n: number){
        p2[n] = {card: p2[n].card, isRevealed:true};
        //p2RevealedCards++;
        p2Score = calculateScore(p2);
    }
    function computerRevealStartCards(){
        isTurn = false;
        setTimeout(() => revealComputerCard(Math.floor(Math.random() * p2.length/2)), 500);
        setTimeout(() => {
            revealComputerCard(Math.floor(Math.random() * p2.length/2 + 3))
            isTurn = true;
        }, 1000);
    }
    function computerMove() {
        if(pile.length > 1){
            const lastPiledCardScore = cardScore(pile[pile.length-1].card);
            let selCard = -1;
            for(let i = 0; i < p2.length; i++) {
                if(p2[i].isRevealed){
                    if(p2[i].card%100 == pile[pile.length-1].card%100 && p2[i].card%100!=2) {
                        let n = 0;
                        if(i>2) n=i-3;
                        else n = n+3;
                        let t = p2[n];
                        p2[n] = pile.pop()!;
                        pile[pile.length-1] = t;
                        pile[pile.length-1].isRevealed = true;
                        isTurn = true;
                        //p2RevealedCards = RevealedCardsCount(p2);
                        return;
                    }
                    if(lastPiledCardScore < 4 && lastPiledCardScore < cardScore(p2[i].card)) {
                        if(selCard == -1) selCard = i;
                        else if(cardScore(p2[i].card) > cardScore(p2[selCard].card)) selCard = i;
                    }
                }
            }
            if(selCard != -1){
                let t = p2[selCard];
                p2[selCard] = pile.pop()!;
                pile[pile.length-1] = t;
                pile[pile.length-1].isRevealed = true;
                isTurn = true;
                //p2RevealedCards = RevealedCardsCount(p2);
                return;
            }
            else if(lastPiledCardScore < 4) {
                for(let i = 0; i < p2.length; i++){
                    if(!p2[i].isRevealed){
                        let t = p2[i];
                        p2[i] = pile.pop()!;
                        pile[pile.length-1] = t;
                        pile[pile.length-1].isRevealed = true;
                        isTurn = true;
                        //p2RevealedCards = RevealedCardsCount(p2);
                        return;
                    }
                }
            }
        }
        if(p2RevealedCards < 4) {
            if(Math.random() > 0.8){
                let a = Math.floor(Math.random() * p2.length);
                while(p2[a].isRevealed) {
                    a++;
                    if(a>=p2.length) a = 0;
                }
                revealComputerCard(a);
            }
        }
        else {
            cardDeck[cardDeck.length-1].isRevealed = true;
            const lastCardScore = cardScore(cardDeck[cardDeck.length-1].card);
            let selCard2 = -1;
            for(let i = 0; i < p2.length; i++) {
                if(p2[i].isRevealed){
                    if(p2[i].card%100 == cardDeck[cardDeck.length-1].card%100 && p2[i].card%100!=2) {
                        let n = 0;
                        if(i>2) n=i-3;
                        else n = n+3;
                        pile = [...pile, p2[n]];
                        pile[pile.length-1].isRevealed = true;
                        p2[n] = cardDeck.pop()!;
                        if(cardDeck.length == 0) endGame();
                        isTurn = true;
                        //p2RevealedCards = RevealedCardsCount(p2);
                        return;
                    }
                    if(lastCardScore < 6 && lastCardScore < cardScore(p2[i].card)) {
                        if(selCard2 == -1) selCard2 = i;
                        else if(cardScore(p2[i].card) > cardScore(p2[selCard2].card)) selCard2 = i;
                    }
                }
            }
            if(selCard2 != -1){
                pile = [...pile, p2[selCard2]];
                pile[pile.length-1].isRevealed = true;
                p2[selCard2] = cardDeck.pop()!;
                if(cardDeck.length == 0) endGame();
                isTurn = true;
                //p2RevealedCards = RevealedCardsCount(p2);
                return;
            }
            else if(lastCardScore < 5) {
                for(let i = 0; i < p2.length; i++){
                    if(!p2[i].isRevealed){
                        pile = [...pile, p2[i]];
                        pile[pile.length-1].isRevealed = true;
                        p2[i] = cardDeck.pop()!;
                        if(cardDeck.length == 0) endGame();
                        isTurn = true;
                        //p2RevealedCards = RevealedCardsCount(p2);
                        return;
                    }
                }
            }
        }
        console.log(cardDeck[cardDeck.length-1]);
        pile = [...pile, cardDeck.pop()!];
        pile[pile.length-1].isRevealed = true;
        if(cardDeck.length == 0) endGame();
        cardDeck[0].isRevealed = false;
        //p2RevealedCards = RevealedCardsCount(p2);
        isTurn = true;
    }
    
</script>

<div class="mainHolder">
    <div class="sideBar">
        <span class="title">Golf card game</span>
    </div>
    <div class="holder" style="flex-direction: row;">
        <div class="pileHolder">
            {#if cardDeck != undefined && cardDeck.length != 0}
                <Card card = {cardDeck[cardDeck.length-1].card} isRevealed = {cardDeck[cardDeck.length-1].isRevealed} isSelected={isNewCardSelected} isInteractable={isTurn && revealedCards>=2} on:click={() => {
                    if(!isTurn || revealedCards<2) return;
                    isNewCardSelected = true;
                    isNew = true;
                    isRevealing = false;
                    cardDeck[cardDeck.length-1].isRevealed = true;
                }}/>
            {:else}
                <div style="width: 143px; height: 208px;"></div>
            {/if}
            <div style="position: relative;">
                {#if pile != undefined}
                    <Card card = {pile[pile.length-1].card} isRevealed = {pile[pile.length-1].isRevealed} isSelected={!isNew} isInteractable={isTurn && revealedCards>=2 && pile.length>1 && !isNewCardSelected} on:click={() => {
                        if(!isTurn || revealedCards<2 || pile.length<2 || isNewCardSelected) return;
                        isNew = false;
                        isRevealing = false;
                    }}/>
                {/if}
                <button class="place" disabled={!isTurn || isRevealing || !isNew} lang="ts" on:click={() => placeCard()}>Place card</button>
            </div>
        </div>
        <div class="holder" style="align-items: center; justify-content: space-around; flex-direction: column">
            <div class="cardHolder">
                {#if p2 != undefined}
                    {#each p2 as card (p2.indexOf(card))}
                        <Card {...card} />
                    {/each}
                {/if}
            </div>
            <div class="cardHolder">
                {#if p1!=undefined}
                    {#each p1 as card (p1.indexOf(card))}
                        <Card {...card} on:click={() => handlePlayerCardClick(p1.indexOf(card))} isInteractable={isTurn}/>
                    {/each}
                {/if}
            </div>
        </div>
        <div class="holder" style="flex-direction: column; justify-content: space-around; width: 100%; padding-left: 3rem">
            <span class="score"><b>{p2Score}</b></span>
            <span class="instructions">Flip any 2 cards</span>
            <span class="score"><b>{p1Score}</b></span>
        </div>
    </div>
</div>
<style>
    div.mainHolder {
        background-color: #e6e6e6;
        width: 100vw;
        height: 100vh;
        position: absolute;
        top: 0px;
        left: 0px;
        display: flex;
        flex-direction: row;
    }
    div.sideBar {
        background-color: #0f6f84;
        width: 4rem;
        height: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }
    span.title {
        color: white;
        writing-mode: vertical-rl;
        text-orientation: upright;
        font-size: 30px;
        font-family: 'Courier New', Courier, monospace;
    }
    div.holder {
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        position: relative;
    }
    div.cardHolder {
        display: grid;
        grid-template-columns: auto auto auto;
        gap: 1rem;
    }
    span.score {
        border: 2px #212f85 solid;
        font-size: 50px;
        height: fit-content;
        width: 5rem;
        color: #212f85;
        text-align: center;
        border-radius: 5px;
    }
    span.instructions {
        border: 2px #212f85 solid;
        color: #212f85;
        font-size: 40px;
        height: fit-content;
        width: fit-content;
        padding: 1rem;
        text-align: center;
        border-radius: 5px;
    }
    button.place {
        border: 2px #212f85 solid;
        color: #212f85;
        width: 100%;
        max-width: 13rem;
        font-size: 25px;
        text-align: center;
        border-radius: 5px;
        position: absolute;
        left: 0;
        top: 240px;
        cursor: pointer;
        transition: all ease-in-out 300ms;
    }
    .place:hover {
        background-color: #0f6f84;
        border-color: #0f6f84;
        color: white;
    }
    .place:active {
        background-color: #212f85;
        border-color: #212f85;
        color: white;
    }
    .place:disabled {
        border-color: #6a6a6b;
        color: #6a6a6b;
        cursor: default;
        background-color: transparent;
    }
    div.pileHolder {
        width: 75%;
        height: 100%;
        display: flex;
        justify-content: space-around;
        align-items: center;
    }
</style>
