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
    let showMenu = false;
    let cardColor = "#212f85";

    $: updateResults("p1", p1);
    $: updateResults("p2", p2);
    $: {
        if(isTurn) {
            isRevealing = true;
            isNew = true;
        }
        else isNewCardSelected = false;
    }

    type PlayingCard = {
        card: number;
        isRevealed: boolean;
    }
    function updateResults(p: string, arr: PlayingCard[]) {
        let cards = 0;
        arr.forEach((card) => {if(card.isRevealed) cards++})
        switch(p){
            case 'p1': {
                revealedCards = cards;
                p1Score = calculateScore(p1);
                if(revealedCards == p1.length) endGame();
                break;
            }
            case 'p2': {
                p2RevealedCards = cards;
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
        revealedCards = 0;
        p2RevealedCards = 0;
        isRevealing = true;
        isTurn = true, isNew = true;
        isNewCardSelected = false;;
        p1Score = 0;
        p2Score = 0;
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
        if(revealedCards < p1.length && p2RevealedCards < p2.length) return;
        if(revealedCards == p2RevealedCards) return;
        if(revealedCards < p1.length){
            for(let i = 0; i < p1.length; i++) if(!p1[i].isRevealed) revealCard(i);
        }
        else{
            for(let i = 0; i < p2.length; i++) if(!p2[i].isRevealed) revealComputerCard(i);
        }
        p1Score = calculateScore(p1);
        p2Score = calculateScore(p2);
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
        if(!isTurn || (isRevealing && p1[n].isRevealed)) return;
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
        if(isRevealing) return;
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
                        else n = i+3;
                        let t = p2[n];
                        p2[n] = pile.pop()!;
                        pile[pile.length-1] = t;
                        pile[pile.length-1].isRevealed = true;
                        isTurn = true;
                        //p2RevealedCards = RevealedCardsCount(p2);
                        console.log(pile);
return;
                    }
                    if(lastPiledCardScore < cardScore(p2[i].card)) {
                        if(selCard == -1) selCard = i;
                        else if(cardScore(p2[i].card) > cardScore(p2[selCard].card)) selCard = i;
                    }
                }
            }
            if(selCard != -1 && lastPiledCardScore < 4){
                if(cardScore(p2[selCard].card) > 6){
                    let flag = true;
                    if(selCard < 3){
                        if(p2[selCard].card%100 == p2[selCard+3].card%100 && p2[selCard+3].isRevealed) flag = false;
                    }
                    else if(p2[selCard].card%100 == p2[selCard-3].card%100 && p2[selCard-3].isRevealed) flag = false;
                    if(flag){
                        let t = p2[selCard];
                        p2[selCard] = pile.pop()!;
                        pile[pile.length-1] = t;
                        pile[pile.length-1].isRevealed = true;
                        isTurn = true;
                        //p2RevealedCards = RevealedCardsCount(p2);
                        console.log(pile);
return;
                    } 
                }
            }
            if(lastPiledCardScore < 4) {
                for(let i = 0; i < p2.length; i++){
                    if(!p2[i].isRevealed){
                        let t = p2[i];
                        p2[i] = pile.pop()!;
                        pile[pile.length-1] = t;
                        pile[pile.length-1].isRevealed = true;
                        isTurn = true;
                        //p2RevealedCards = RevealedCardsCount(p2);
                        console.log(pile);
return;
                    }
                }
            }
            if(p2RevealedCards == p2.length-1){
                if(p2Score + lastPiledCardScore < p1Score){
                    for(let i = 0; i < p2.length; i++){
                        if(!p2[i].isRevealed){
                            let t = p2[i];
                            p2[i] = pile.pop()!;
                            pile[pile.length-1] = t;
                            pile[pile.length-1].isRevealed = true;
                            isTurn = true;
                            //p2RevealedCards = RevealedCardsCount(p2);
                            console.log(pile);
    return;
                        }
                    }
                }
            }
        }

        if(p2RevealedCards < 4) {
            if(Math.random() > 0.7){
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
                        else n = i+3;
                        pile = [...pile, p2[n]];
                        pile[pile.length-1].isRevealed = true;
                        p2[n] = cardDeck.pop()!;
                        if(cardDeck.length == 0) endGame();
                        isTurn = true;
                        //p2RevealedCards = RevealedCardsCount(p2);
                        console.log(pile);
return;
                    }
                    if(lastCardScore < cardScore(p2[i].card)) {
                        if(selCard2 == -1) selCard2 = i;
                        else if(cardScore(p2[i].card) > cardScore(p2[selCard2].card)) selCard2 = i;
                    }
                }
            }
            if(selCard2 != -1 && lastCardScore < 4){
                if(cardScore(p1[selCard2].card) > 6){
                    let flag = true;
                    if(selCard2 < 3){
                        if(p2[selCard2].card%100 == p2[selCard2+3].card%100 && p2[selCard2+3].isRevealed) flag = false;
                    }
                    else if(p2[selCard2].card%100 == p2[selCard2-3].card%100 && p2[selCard2-3].isRevealed) flag = false;
                    if(flag){
                        pile = [...pile, p2[selCard2]];
                        pile[pile.length-1].isRevealed = true;
                        p2[selCard2] = cardDeck.pop()!;
                        if(cardDeck.length == 0) endGame();
                        isTurn = true;
                        //p2RevealedCards = RevealedCardsCount(p2);
                        console.log(pile);
return;
                    }
                }
            }
            if(lastCardScore < 4) {
                for(let i = 0; i < p2.length; i++){
                    if(!p2[i].isRevealed){
                        pile = [...pile, p2[i]];
                        pile[pile.length-1].isRevealed = true;
                        p2[i] = cardDeck.pop()!;
                        if(cardDeck.length == 0) endGame();
                        isTurn = true;
                        //p2RevealedCards = RevealedCardsCount(p2);
                        console.log(pile);
return;
                    }
                }
            }
            if(p2RevealedCards == p2.length-1){
                if(p2Score + lastCardScore < p1Score){
                    for(let i = 0; i < p2.length; i++){
                        if(!p2[i].isRevealed){
                        pile = [...pile, p2[i]];
                        pile[pile.length-1].isRevealed = true;
                        p2[i] = cardDeck.pop()!;
                        if(cardDeck.length == 0) endGame();
                        isTurn = true;
                        //p2RevealedCards = RevealedCardsCount(p2);
                        console.log(pile);
return;
                    }
                    }
                }
            }
            if(selCard2 != -1){
                let flag = true;
                if(selCard2 < 3){
                    if(p2[selCard2].card%100 == p2[selCard2+3].card%100 && p2[selCard2+3].isRevealed) flag = false;
                }
                else if(p2[selCard2].card%100 == p2[selCard2-3].card%100 && p2[selCard2-3].isRevealed) flag = false;
                if(flag){
                    pile = [...pile, p2[selCard2]];
                    pile[pile.length-1].isRevealed = true;
                    p2[selCard2] = cardDeck.pop()!;
                    if(cardDeck.length == 0) endGame();
                    isTurn = true;
                    //p2RevealedCards = RevealedCardsCount(p2);
                    console.log(pile);
return;
                }
            }
        }
        //console.log(cardDeck[cardDeck.length-1]);
        pile = [...pile, cardDeck.pop()!];
        pile[pile.length-1].isRevealed = true;
        if(cardDeck.length == 0) endGame();
        cardDeck[0].isRevealed = false;
        //p2RevealedCards = RevealedCardsCount(p2);
        isTurn = true;
    }
    
</script>

<div class="mainHolder">
    <div class="sideBar" style="width: {showMenu? "35rem":"4rem"}; --bg-color:{showMenu? "#d63b5d":"#0f6f84"}; padding-right: {showMenu? "1rem":"none"}">
        <div class="sideBarSecondHolder">
            <button class="menuButton" on:click={() => {showMenu = !showMenu}}>
                <svg xmlns="http://www.w3.org/2000/svg" height="48" viewBox="0 -960 960 960" width="48">
                    <path d="M103-212v-94h754v94H103Zm0-221v-94h754v94H103Zm0-221v-95h754v95H103Z" fill='white'/>
                </svg>
            </button>
            <span class="title">Golf card game</span>
            <button class="menuButton" on:click={() => {showMenu = !showMenu}}>
                <b>?</b>
            </button>
        </div>
        {#if showMenu}
            <div class="sideBarSecondHolder" style="width: 100%;">
                <div id="colorSelect">
                    <input type="color" bind:value={cardColor}>
                    <span>Choose card color</span>
                </div>
                <span id="menuText">The objective is for players to reduce the value of the cards in front of them by swapping them for lesser value cards. After the last round, the highest score loses the game and the lowest score wins the game (see scoring below). Players can do one of the following:
                    <p>1. Flip a card</p>
                    <p>2. Take a card from the discard pile(if there are more than one cards)</p>
                    <p>3. Take a card from the deck</p>
                    If the player chooses to take a card they can swap it for one of their own cards or place it in the discard pile.
                    The game ends when all six cards of either player are all face up.
                    <p>Scoring:
                        2 = -2;
                        K = 0;
                        A = 1;
                        Every card form 3 to 10 = face value (e.g. 5 = 5);
                        J = 10;
                        Q = 10.
                    </p>
                </span>
            </div>
        {/if}
    </div>
    <div class="holder" style="flex-direction: row;">
        <div class="pileHolder">
            {#if cardDeck != undefined && cardDeck.length != 0}
                <Card card = {cardDeck[cardDeck.length-1].card} isRevealed = {cardDeck[cardDeck.length-1].isRevealed} isSelected={isNewCardSelected} isInteractable={isTurn && revealedCards>=2} cardColor={cardColor} on:click={() => {
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
                    <Card card = {pile[pile.length-1].card} isRevealed = {pile[pile.length-1].isRevealed} isSelected={!isNew} isInteractable={isTurn && revealedCards>=2 && pile.length>1 && !isNewCardSelected} cardColor={cardColor} on:click={() => {
                        if(!isTurn || revealedCards<2 || pile.length<2 || isNewCardSelected) return;
                        isNew = false;
                        isRevealing = false;
                    }}/>
                {/if}
                <button class="place" disabled={!isTurn || isRevealing || !isNew} lang="ts" on:click={() => placeCard()} style="--card-color: {cardColor}">Place card</button>
            </div>
        </div>
        <div class="holder" style="align-items: center; justify-content: space-around; flex-direction: column">
            <div class="cardHolder">
                {#if p2 != undefined}
                    {#each p2 as card (p2.indexOf(card))}
                        <Card {...card} cardColor={cardColor}/>
                    {/each}
                {/if}
            </div>
            <div class="cardHolder">
                {#if p1!=undefined}
                    {#each p1 as card (p1.indexOf(card))}
                        <Card {...card} on:click={() => handlePlayerCardClick(p1.indexOf(card))} isInteractable={isTurn} cardColor={cardColor}/>
                    {/each}
                {/if}
            </div>
        </div>
        <div class="holder" style="flex-direction: column; justify-content: space-around; width: 100%; padding-left: 3rem">
            <span class="score" style="--card-color: {cardColor}"><b>{p2Score}</b></span>
            <span class="instructions" style="--card-color: {cardColor}">{isTurn? (revealedCards >= 2? "Your turn":(revealedCards == 0? "Flip any two cards":"Flip one more card")):"Advanced AI is thinking"}</span>
            <span class="score" style="--card-color: {cardColor}"><b>{p1Score}</b></span>
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
    span.title {
        color: white;
        writing-mode: vertical-rl;
        text-orientation: upright;
        font-size: 30px;
        font-family: 'Courier New', Courier, monospace;
        user-select: none;
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
        border: 2px var(--card-color) solid;
        font-size: 50px;
        height: fit-content;
        width: 5rem;
        color: var(--card-color);
        text-align: center;
        border-radius: 5px;
    }
    span.instructions {
        border: 2px var(--card-color) solid;
        color: var(--card-color);
        font-size: 40px;
        height: fit-content;
        width: fit-content;
        padding: 1rem;
        text-align: center;
        border-radius: 5px;
    }
    button.place {
        border: 2px var(--card-color) solid;
        color: var(--card-color);
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
        background-color: var(--card-color);
        border-color: var(--card-color);
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
    .menuButton {
        width: 90%;
        max-width: 4rem;
        border-radius: 10px;
        background-color: transparent;
        border: none;
        color: white;
        font-size: 40px;
        transition: all ease-in-out 300ms;
        cursor: pointer;
    }
    .menuButton:hover {
        scale: 85%;
    }
    .menuButton:active {
        scale: 75%;
    }
    .sideBar {
        background-color: var(--bg-color);
        height: 100%;
        display: flex;
        flex-direction: row;
        gap: 1rem;
        align-items: center;
        transition: all ease-in-out 300ms;
    }
    .sideBar:has(.menuButton:hover) {
        background-color: #d63b5d;
    }
    .sideBarSecondHolder {
        width: 4rem;
        height: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-around;
    }
    #menuText {
        color: white;
        font-size: 20px;
    }
    #colorSelect {
        color: white;
        font-size: 20px;
        width: 100%;
        display: flex;
        justify-content: space-around;
        align-items: center;
    }
</style>
