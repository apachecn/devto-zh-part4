# èæ¡ç®ä»ð¦â¡ï¸ð§¡

> åæï¼<https://dev.to/sendilkumarn/intro-to-svelte-460b>

# ä¸ºä»ä¹è¿è¦å­¦ä¹ å¦ä¸ç§æ¡æ¶ï¼ð¤¦ââï¸ð¤¦ââï¸

> å¦æä½ æå¿å¤§éçåç«¯æ¡æ¶åå®ä»¬çåç§ï¼ä¸ºä»ä¹è¿è¦å¦ä¸ä¸ªæ¡æ¶å¢ï¼

å AngularãReactJS æ VueJS è¿æ ·çæ¡æ¶å¯ä»¥æ´å®¹æãæ´å¿«å°å¯å¨åºç¨ç¨åºãå¢éä¸­æ´å¥å£®åé«æçåºç¨ç¨åºå¼åã

ä»ä»¬æ¹åäºæä»¬è®¾è®¡ãå¼ååäº¤ä»åºç¨çæ¹å¼ã

è¶æ¥è¶å¤çæ¡æ¶æ¹è¿äºæ¡æ¶ï¼ä½¿å¾å¼åç®åãå¿«éãé«æçåºç¨ç¨åºåå¾æ´å å®¹æã

**æ£±è§åæ** â¤ï¸

Angular æ¯ä¸ä¸ªå®æ´çå¥ä»¶ï¼ä½å®å¾èè¿ã

**ååº**ð

React æ¯ä¸ä¸ª`view only`åºï¼ä½¿ç¨`virtual DOM`ï¼ä½æ¯ React åå¾å¤æäºã

è§å¾ð

VueJS æäºç¼åï¼å¹¶ä¸å·æ React å Angular çæä½³é¨åã

èæ¡çð§¡

Svelte æ¯ä¸ç§æå»ºç¨æ·çé¢çæ°æ¹æ³ãèæ¡çæ²¡æèæç DOMã

èæ¡è®©ä½ åæ´å°çä»£ç å`uses the platform`ã

Svelte æ²¡æèæ DOM å`uses the platform`ã

Svelte å¨æå»ºè¿ç¨ä¸­ä¼åäºåºç¨ç¨åºï¼æé«äºåºç¨ç¨åºçæ§è½ã

# è·å appï¼å¼å§è¿è¡

(æ èç)æ¡æ¶ä»ç»å·²ç»å¤å¤äºï¼è®©æä»¬å¼å§ç¼ç å§ã

*ä»åªéå¼å§ï¼*

ãä½ å¥½ä¸çãT0

```
npx degit sveltejs/template hello-world
cd hello-world 
```

æ¨å·²ç»ä»`sveltejs/template`åéäºä¸ä¸ªæ¨¡æ¿ãç°å¨å®è£ä¾èµé¡¹ï¼å¹¶ä½¿ç¨
æè½¬æå¡å¨

```
npm i
npm run dev 
```

æ¨åºè¯¥ä¼çå°ä¸ä¸ªç®åç`Hello World`åºç¨ç¨åºè¿è¡å¨ [localhost:5000](http://localhost:5000) ä¸ã

[![](img/b4f5764007d3c844c2729d250b17be6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IzLsujJ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/21gxwk0tva19m4awtm8p.png)

è®©æä»¬æ·±å¥ç ç©¶ä¸ä¸ï¼ççäº§çäºä»ä¹ã

`Svelte`ç»ä»¶å¨`.svelte`æä»¶ä¸­å®ä¹ãçæçåºç¨ç¨åºæ`App.svelte`ã

`Svelte`ç»ä»¶åªä¸è¿æ¯ä¸ä¸ªç®åç`HTML`æä»¶ãææç»é¿ç»ä»¶é½å¯ä»¥æä»¥ä¸ä¸ä¸ªé¨å:

*   èæ¬ââå®ä¹ JavaScript
*   æ ·å¼-å®ä¹ CSS
*   ç®åç HTML -åªæä½ ç HTML åèæ¡çåç´ 

å¦ææ¨æ¾ç»ä½¿ç¨è¿ä¸é¢ååºçä»»ä½æ¡æ¶ï¼é£ä¹æ¨å¯è½ç¥éä»ä¹æ¯`props`ã

> `Props`æ¯ä½ æ²¿ç`Components`ä¼ éçå±æ§ã

æ ç­¾`script`ä¸­ç`export let name`è¡¨ç¤º`name`æ¯ä»ç»ä»¶ä¸­å¯¼åºçï¼æä»¬å¯ä»¥éè¿å±æ§åå®åéä¿¡æ¯ã

éå å°ç»ä»¶ä¸ç`props`å°é¦åä»ç»ä»¶ä¸­`exported`åºæ¥ã

```
<script>
        export let name;
</script>

<style>
        h1 {
                color: purple;
        }
</style> 
```

æä»¬å¨`<script>`ç»ä»¶ä¸­å®ä¹çä»»ä½åé(åæ¬éå·)é½å¯ä»¥ç¨`{variable_name}`ç¬¦å·å¨`HTML component`ä¸­ä½¿ç¨ã`{}`ç¨äºå®ä¹å¨`script`æ ç­¾ä¸­å®ä¹çå¼ã

```
<h1>Hello {name}!</h1> 
```

åºç¨ç¨åºå¨`main.js`ä¸­å¯å¨ãæä»¬å¯¼å¥`App.svelte`(è¿éå®ä¹äº`App`ç»ä»¶)ã

```
import App from './App.svelte'; 
```

ç¶åæä»¬ä»å¯¼å¥ç`App`ç»ä»¶åå»ºä¸ä¸ª App å¯¹è±¡ãç¶åæä»¬å®ä¹`target`ï¼å¹¶æ`props`ä¼ éç»ç»ä»¶çæé å½æ°ã

`target`å®ä¹äºç»ä»¶å¨ HTML ææ¡£ä¸­çåç°ä½ç½®ã

`props`æ¯æä»¬å®ä¹å±æ§çå°æ¹ã

```
const app = new App({
        target: document.body,
        props: {
                name: 'world'
        }
}); 
```

æåï¼ä»`main.js`ä¸­å¯¼åº`app`ã

```
export default app; 
```

è¿æè´è´£æç»åæå»ºåºç¨ç¨åºç`rollup.config.js`ã

```
import App from './App.svelte'; 
```

æ³è¦ä¸ä¸ªæ´ç­çéæ©ï¼åè¿ä¸ª`codesandbox`

[https://codesandbox.io/embed/magical-gates-vfhbg](https://codesandbox.io/embed/magical-gates-vfhbg)

# Tic Tac Toe

è®©æä»¬ä»å¸¦æ`Svelte`ç react(å®æ¹)ç¤ºä¾ä¸­éæ°åå»ºç»å¸ç`Tic Tac Toe`ã

åå»ºä¸ä¸ªç»ä»¶æä»¶å¤¹ï¼å¨è¿éæä»¬å°å®ä¹é¤äº`App.svelte`ä¹å¤çææ`Svelte`ç»ä»¶ã

æä»¬å°éè¦ä»¥ä¸ç»ä»¶:

*   `Square.svelte` -äºå­æ¸¸æä¸­çæ¯ä¸ªæ¹åé½æ¯ä¸ä¸ªç¬ç«ç`svelte`ç»ä»¶ã
*   `Board.svelte` -çµè·¯æ¿ç»ä»¶å°å®¹çº³æææ¹å½¢ç»ä»¶ãè¿ä¸ªç»ä»¶å°è´è´£å°æ°æ®ä¼ éç»å®çå­ç»ä»¶`square`,å¹¶å³å®æ¸¸ææ¯ç»§ç»­è¿æ¯ç»æã
*   `Game.svelte` -æ¸¸æç»ä»¶æ¯å´ç»`Board`ç»ä»¶çä¸ä¸ªæ´ä½åè£å¨ã

è®©æä»¬é¦ååå»º`Game.svelte`ç»ä»¶ãæ¸¸æç»ä»¶å°ææ`Board`ç»ä»¶ã

```
<div class="game">
    <Board />
</div> 
```

ç°å¨æä»¬éè¦å¯¼å¥`Board`ç»ä»¶ã

```
<script> 
    import Board from './Board.svelte';
</script> 
```

è®©æä»¬ç¨å¾®è®¾è®¡ä¸ä¸æ¿å­çæ ·å¼ã

```
<style>
.game {
    display: flex;
    flex-direction: row;
}
</style> 
```

* * *

æ£çç»ä»¶æä¸æï¼æ¯æä¸ä¸ªæ¹åã

```
<div class="board-row">
    <Square />
    <Square />
    <Square />
</div>
<div class="board-row">
    <Square />
    <Square />
    <Square />
</div>
<div class="board-row">
    <Square />
    <Square />
    <Square />
</div> 
```

æä»¬éè¦å¨`<script>`é¨åå¯¼å¥`Square`ç»ä»¶ã

```
<script>
import Square from './Square.svelte';
</script> 
```

è®©æä»¬ç»å®ä»¬è®¾è®¡ä¸ç¹é£æ ¼ã

```
<style>
.board-row:after {
    clear: both;
    content: "";
    display: table;
}
</style> 
```

* * *

ç¶åæä»¬å°å¨`Square.svelte`ä¸­å®ä¹`Square`ç»ä»¶ã

```
<style>
 .square {
    background: #fff;
    border: 1px solid #999;
    float: left;
    font-size: 24px;
    font-weight: bold;
    line-height: 34px;
    height: 34px;
    margin-right: -1px;
    margin-top: -1px;
    padding: 0;
    text-align: center;
    width: 34px;
  }

 .square:focus {
    outline: none;
 }

 .square:focus {
    background: #ddd;
 }
</style>

<script>
let value = "";
</script>

<button class="square"> {value} </button> 
```

æä»¬å®ä¹äº`value`å¹¶å¨`button`åç´ ä¸­ä½¿ç¨äºå®ã

å½æä»¬åå»æé®æ¶ï¼å®åºè¯¥å°å¼æ´æ¹ä¸º`X`ãæä»¬å°ä½¿ç¨`on:click`äºä»¶å¤çç¨åºã

```
<button class="square" on:click={() => handleClick}> {value} </button> 
```

`Svelte`ä¸­çäºä»¶å¤çç¨åºè¢«å®ä¹ä¸º`on:<event>`ã

è®©æä»¬å¨`<script>`æ ç­¾ä¸­å®ä¹`handleClick`ã

```
function handleClick() {
   value = 'X';
} 
```

ç°å¨ç¹å»æé®ï¼ä½ åºè¯¥çå°æé®çå¼åæäº`X`ã

> æ²¡æ`this`ï¼æ²¡æå¤æçç»å®ï¼åªæç®åçåéå£°æåæ¹åã

* * *

å¨å­ç»ä»¶ä¸­ç»´æ¤ç¶æå¹¶å°å¶ä¼ æ­ç»ç¶ç»ä»¶å¹¶ä¸å®¹æãç¸åï¼æä»¬å¯ä»¥å°ç¶æè½¬ç§»å°ç¶ç»ä»¶ï¼ç¶åè®©ç¶ç»ä»¶å³å®å¦ä½ç»´æ¤å­ç»ä»¶ãä¸ºæ­¤ï¼è®©æä»¬æ´æ¹`Board`ç»ä»¶ï¼å¹¶éè¿`Board`ç»ä»¶åé`value`å`on:click`äºä»¶å¤çç¨åºã

è®©æä»¬è®¤ä¸º`on:click`å`value`é½æ¯`Square`ç»ä»¶ç`prop`ã

```
<script> 
export let value; 
export let handleClick;
</script>

<button class="square" on:click={handleClick}> {value} </button> 
```

ç°å¨æä»¬å°ä¿®æ¹`Board`ç»ä»¶ãæä»¬å°å®ä¹ä¸ä¸ªæ°ç»`squares`å¹¶ä½¿ç¨å®ï¼èä¸æ¯å®ä¹æ¯ä¸ª`Board`ã

```
<script>
    let squares = Array(9).fill('');
</script> 
```

å¹¶å° HTML æ´æ¹ä¸º

```
 <div class="board-row">
    <Square value={squares[0]} handleClick={() => handleClick(0)}/>
    <Square value={squares[1]} handleClick={() => handleClick(1)}/>
    <Square value={squares[2]} handleClick={() => handleClick(2)}/>
  </div>

  <div class="board-row">
    <Square value={squares[3]} handleClick={() => handleClick(3)} />
    <Square value={squares[4]} handleClick={() => handleClick(4)} />
    <Square value={squares[5]} handleClick={() => handleClick(5)} />
  </div>

  <div class="board-row">
    <Square value={squares[6]} handleClick={() => handleClick(6)} />
    <Square value={squares[7]} handleClick={() => handleClick(7)} />
    <Square value={squares[8]} handleClick={() => handleClick(8)} />
  </div> 
```

æä»¬è¿éè¦å®ä¹`handleClick`æ¹æ³ã

```
function handleClick(i) {
    squares[i] = 'X';
} 
```

ðæ­åå¤ªæ£äºãè®©æä»¬å»ºç«çæ­£çæ¸¸æã

* * *

æ¸¸æäº¤æ¿è¿è¡ï¼å³ä¸åç©å®¶ç¨`X`æ è®°ï¼å¦ä¸åç©å®¶ç¨`O`æ è®°ãè®©æä»¬å°è¯¥æ¡ä»¶æ·»å å°`Board`ç»ä»¶ä¸­ã

```
<!-- Board.svelte -->
let isXTurn = true;

function handleClick(i) {
   squares[i] = isXTurn ? 'X' : 'O';
   isXTurn = !isXTurn;
} 
```

é·ï¼ç°å¨æä»¬è¦è®¡ç®èµ¢å®¶ã

```
function calculateWinner(squares) {
    const lines = [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8],
      [0, 3, 6],
      [1, 4, 7],
      [2, 5, 8],
      [0, 4, 8],
      [2, 4, 6]
    ];
    for (let i = 0; i < lines.length; i++) {
      const [a, b, c] = lines[i];
      if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
        return squares[a];
      }
    }
    return null;
} 
```

å¦ææèµ¢å®¶ï¼ä¸è¿°å½æ°å°è¿å`X`æ`O`ï¼å¦ååªè¿åä¸ä¸ª`null`ã

æä»¬å°ä¸å¾ä¸å¨æ¯æ¬¡ç¨æ·ç¹å»æ¹åæ¶æ£æ¥è¿ä¸ç¹ãæ­¤å¤ï¼ä¸æ¦ç©å®¶ç¹å»ä¸ä¸ªæ¹åï¼æä»¬ä¸åºè¯¥åè®¸ä»»ä½å¶ä»ç¹å»è¯¥æ¹åã

```
function handleClick(i) {
    if (calculateWinner(squares) || squares[i] !== '') {
      return;
    }
   squares[i] = isXTurn ? 'X' : 'O';
   isXTurn = !isXTurn;
} 
```

* * *

æä»¬éè¦åç©å®¶å±ç¤ºæ¸¸æç¶æãæ¸¸æç¶æä¿¡æ¯æ¯å¨æçãä¹å°±æ¯è¯´ï¼å®ä¼æ¹åï¼åºè¯¥æäººå¬å®ï¼å¹¶æ¹åä¸æ¦æ´æ°ççæ³ã

å¨ React ä¸çä¸­ï¼æä»¬å°æ¥æ`state`ãå¨ Svelte ä¸­ï¼æä»¬ç¨`$`ç»å®ç»ä»¶ãè¿äºå¼å°è¢«æ´æ°ã

```
$: winner = calculateWinner(squares)

$: status =  winner ? "Winner: " + winner :"Next player:" + (xIsNext ? "X" : "O"); 
```

æä»¬å°ä½¿ç¨`HTML`ä¸­çç¶æã

```
 <div class="status"> {status} </div>

<style>
.status {
    margin-bottom: 10px;
  }
</style> 
```

ç°å¨ï¼åªè¦æååï¼å°±ä¼éæ°è®¡ç®ç¶æã

[https://codesandbox.io/embed/heuristic-spence-bvzun](https://codesandbox.io/embed/heuristic-spence-bvzun)

* * *

èæ¡çç»ä»¶æ¯èæ¡çï¼æ²¡æé¢å¤çæ ·æ¿ï¼æ²¡æ`this`,å®æ´æ¥è¿å¹³å°ãå¶å®ä»ä»¬æ éå°±æ¯ç®åç`HTML`ã

æ¥ç[èæ¡çä¾å­ç½ç«](https://svelte.dev/examples)è·å¾æ´å¤ç²¾å½©çæ¼ç¤ºã

ç¹å»æ¥çæ´å¤ææ¡£[ã](https://svelte.dev/docs)

æä»¬å°ç»§ç»­å³äº`Svelte`çæ´æ·±å¥çæç¨ã

å¦æä½ åæ¬¢è¿ç¯æç« ï¼è¯·çä¸èµæè¯è®ºãâ¤ï¸

å¦ææ¨è§å¾æç« ä¸­æéè¯¯/éæ¼ä¹å¤ï¼è¯·éæ¶è¯è®º:)

ä½ å¯ä»¥å¨æ¨ç¹ä¸å³æ³¨æã