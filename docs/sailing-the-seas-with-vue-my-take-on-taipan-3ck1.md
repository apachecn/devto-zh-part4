# 与 Vue 扬帆远航——我的大班观

> 原文：<https://dev.to/raymondcamden/sailing-the-seas-with-vue-my-take-on-taipan-3ck1>

小时候，我在我的苹果 2 上花了很多时间。我玩了许多不同的游戏，但我最喜欢的是[大班！](https://en.wikipedia.org/wiki/Taipan!)。

<figure>[![title screen for Taipain!](img/7cfb2f1e9c524971e8ef2726215cf392.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CMRQey0t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/08/TaipanGameTitle.png) 

<figcaption>按来源，合理使用，[【https://en.wikipedia.org/w/index.php?curid=8888638】](https://en.wikipedia.org/w/index.php?curid=8888638)</figcaption>

</figure>

大班是远东的一个基本贸易模拟器。你有一艘有存储能力的船，可以在多个港口买卖货物。这个游戏有基本的战斗，一个放债人，和其他让事情变得有趣的细节，但对我来说，我的快乐来自纯粹的研磨。我会在晚上玩几个小时，看看能赚多少钱。(当然，一旦我发现放债人的毛病，致富就变得微不足道了。)

作为我今年“获得更多 Vue 应用程序体验”的基本目标的一部分，我决定(尽我所能)重建游戏，当然，使用 Vue.js。虽然我不想完全重建，但在我的版本中我做了一些改变。

*   首先，我摆脱了战斗。我讨厌游戏的战斗部分，因为它感觉慢得令人难以置信。我喜欢这个事实，它增加了比赛的风险，但不喜欢它如何扼杀了节奏。在我的版本中，你可以被海盗攻击，但是他们只会破坏和偷东西。
*   我摆脱了放债人。这是一个有趣的方面，但当到达港口时，它也减慢了游戏的速度。
*   我通过李源摆脱了“勒索”的局面。我也喜欢这方面，可能最终会把它带回来。
*   我把仓库处理掉了。对我来说，这总是让我分心。
*   我也没有让我的一件商品违法。

差不多就是这样，但是还有一些其他更小的模块。我的游戏感觉比原版快多了，这也是我喜欢玩它的原因。

我也试着尽可能多地使用键盘。你可以在这里阅读我在这方面的工作:[在你的 Vue 应用程序中使用键盘](https://www.raymondcamden.com/2019/08/12/working-with-the-keyboard-in-your-vue-app)。我没有让*的一切*键盘可用，但是从一个端口到另一个端口的导航可以完全通过键盘来完成，并且在玩的时候感觉它是一个非常好的设置。所以在我进入代码之前，如果你想试一试，你可以在这里玩:

[https://taipan.raymondcamden.now.sh/](https://taipan.raymondcamden.now.sh/)

您可以在这里查看源代码:

[https://github . com/cfjedimaster/vue-demos/tree/master/taipan/](https://github.com/cfjedimaster/vue-demos/tree/master/taipan/)

好的，让我们看一下代码。我不打算一一介绍，而是从更高的层面来谈谈(对我来说)更有趣的部分。

大班同时使用 Vue 路由器和 Vuex。我使用路由器没什么特别的。有一条回家的路带你去看比赛。一个“设置”路线，只问你的名字。然后游戏路线的大部分工作都完成了。接下来是“旅行”路线，处理从一个港口到另一个港口。最后有一个显示你最终数据的游戏结束路线。

我的 Vuex 用法很有趣。就像我的[柠檬水摊](https://www.raymondcamden.com/2019/08/01/playing-with-vue-and-vuex-lemonade-stand)游戏一样，我花了大量的时间思考什么应该出现在我的视图中，什么应该出现在商店中。我确实认为视图中有一些不应该出现的东西。我认为 Vue 开发的这个特殊方面会随着应用程序的迭代而改变。

让我们看看游戏性是如何发生的。每个回合都包含以下逻辑。

*   首先，我要求 Vuex 考虑随机事件。这确实是整场比赛中最困难的部分。核心的“转到转，买，卖”等逻辑并不太难。但是处理“特殊事件”肯定是有问题的。
*   我的视图提示输入。这可以是购买货物、出售货物、修理损坏、升级船只或移动到另一个港口。

“提示输入”方面与键盘有关。我的解决方案包括根据你正在做的事情的当前“状态”显示一个菜单。所以最初的状态是-显示菜单。但是如果你想买东西，我会切换到另一个菜单，提示你金额和商品。你可以在 Game.vue.
的布局中看到这一点

```
<template>
    <div>
        <p>
            The date is {{ date }}, Captain {{captain}}. You are currently docked at {{ port }}.
        </p>

        <div class="container">
            <Stats />
            <Hold />
            <Prices />
        </div>

        <p v-if="canUpgrade">
            <strong>Good News!</strong> You can upgrade your ship for {{ upgradeCost }}.
            <span v-if="money < upgradeCost">Unfortunately you do not have the funds.</span>
            <span v-else><button @click="doUpgrade">Purchase Upgrade</button></span>
        </p>

        <p v-if="!keyState">
            <b>Menu:</b> Type <code>B</code> to buy, <code>S</code> to sell, 
            <span v-if="damage"><code>R</code> to repair, </span>
            <code>M</code> to go to another port or <code>Q</code> to quit.
        </p>

        <p v-if="keyState == 'Move'">
            Move to 
                <span v-for="(p, i) in ports" :key="i">{{ i+1 }}) {{ p }} </span>
            <br/>
            Or <code>C</code> to cancel.
        </p>

        <p v-if="keyState == 'Buy'">

            Buy 
                <input v-model.number="toBuyQty" type="number" min="0"> units of 
                <select v-model="toBuy">
                <option v-for="(s, i) in prices" :value="s" :key="i">{{ s.name }}</option>
                </select> 
                for {{ purchasePrice | num }}.
                <button :disabled="cantBuy" @click="buyGoods">Purchase</button>
            <br/>
            Or <code>C</code> to cancel.
        </p>

        <p v-if="keyState == 'Sell'">

            Sell 
                <input v-model.number="toSellQty" type="number" min="0"> units of 
                <select v-model="toSell">
                <option v-for="(s, i) in prices" :value="s" :key="i">{{ s.name }}</option>
                </select> 
                for {{ sellPrice | num }}.
                <button :disabled="cantSell" @click="sellGoods">Sell</button>
            <br/>
            Or <code>C</code> to cancel.
        </p>

        <p v-if="keyState == 'Repair'">

            Spend 
                <input v-model.number="toRepairQty" type="number" min="0"> on repairs. 
                <button :disabled="cantRepair" @click="doRepair">Repair</button>
            <br/>
            Or <code>C</code> to cancel.
        </p>

    </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

我把我的很多显示内容移到了组件中，这让这个页面的布局主要集中在响应你的输入上。值是我处理动态改变当前菜单的方式。下面是 JavaScript:

```
import Hold from '@/components/Hold.vue'
import Prices from '@/components/Prices.vue'
import Stats from '@/components/Stats.vue'

export default {
    data() {
        return {
            keyState:null,
            ray:null,
            toBuy:null,
            toBuyQty:0,
            toSell:null,
            toSellQty:0,
            toRepairQty:0
        }
    },
    components:{
        Hold, Prices, Stats
    },
    created() {
        this.$store.commit('newTurn');
        window.addEventListener('keypress', this.doCommand);
    },
    destroyed() {
        window.removeEventListener('keypress', this.doCommand);
    },
    computed: {
        cantBuy() {
            return (
                this.toBuy === null
                ||
                (this.toBuy.price * this.toBuyQty) > this.money
                ||
                this.toBuyQty + this.shipUsedSpace > this.holdSize
            )
        },
        cantRepair() {
            return this.toRepairQty > this.money;
        },
        cantSell() {
            if(this.toSell === null) return true;
            let avail = 0;
            for(let i=0;i<this.hold.length;i++) {
                if(this.hold[i].name === this.toSell.name) {
                    avail = this.hold[i].quantity;
                }
            }
            console.log('avail is '+avail);
            return (
                this.toSellQty > avail
            )
        },
        canUpgrade() {
            return this.$store.state.offerUpgrade;
        },
        captain() {
            return this.$store.state.name;
        },
        damage() {
            return this.$store.state.damage;
        },
        date() {
            return this.$store.getters.gameDate;
        },
        hold() {
            return this.$store.state.hold;
        },
        holdSize() {
            return this.$store.state.holdSize;
        },
        money() {
            return this.$store.state.money;
        },
        port() {
            return this.$store.state.port.name;
        },
        ports() {
            return this.$store.getters.ports;
        },
        prices() {
            return this.$store.state.prices;
        },
        purchasePrice() {
            if(!this.toBuy) return 0;
            /* disabled due to warning about unexpected side effect, which makes sense
            if(this.toBuyQty < 0) this.toBuyQty = 0;
            */
            return this.toBuy.price * this.toBuyQty;
        },
        repairCost() {
            return this.$store.getters.repairCost;
        },
        sellPrice() {
            if(!this.toSell) return 0;
            return this.toSell.price * this.toSellQty;
        },
        shipUsedSpace() {
            return this.$store.getters.shipUsedSpace
        },
        upgradeCost() {
            return this.$store.getters.upgradeCost;
        }
    },
    methods: {
        buyGoods() {
            //in theory not needed due to other checks
            if(!this.toBuy) return;
            if(this.toBuyQty <= 0) return;

            this.$store.commit('purchase', { good: this.toBuy, qty: this.toBuyQty });
            this.keyState = null;
        },
        doUpgrade() {
            this.$store.commit('upgrade', { cost: this.upgradeCost });
        },
        sellGoods() {
            if(!this.toSell) return;
            if(this.toSellQty <= 0) return;

            this.$store.commit('sale', { good: this.toSell, qty: this.toSellQty });
            this.keyState = null;
        },
        doCommand(e) {
            let cmd = String.fromCharCode(e.keyCode).toLowerCase();

            /*
            How we respond depends on our state. If keyState is null, 
            it meand we aren't doing anything, so BSM are valid.
            */
            if(!this.keyState) {

                if(cmd === 'b') {
                    console.log('Buy');
                    this.toBuy = null;
                    this.toBuyQty = 0;
                    this.keyState = 'Buy';
                }

                if(cmd === 's') {
                    console.log('Sell');
                    this.toSell = null;
                    this.toSellQty = 0;
                    this.keyState = 'Sell';
                }

                if(cmd === 'm') {
                    console.log('Move');
                    this.keyState = 'Move';
                }

                if(cmd === 'r') {
                    console.log('Repair');
                    this.keyState = 'Repair';
                }

                if(cmd === 'q') {
                    this.$router.replace('/end');
                }
                return;
            }

            //keystate for move
            if(this.keyState === 'Move') {

                if(cmd === 'c') {
                    this.keyState = null;
                    return;
                }

                cmd = parseInt(cmd, 10);
                for(let i=0;i<this.ports.length;i++) {
                    if(cmd-1 === i) {
                        console.log('going to move to '+this.ports[i]);
                        this.$router.replace({ name:'travel', 
                        params: { 
                            destination: this.ports[i],
                            destinationIndex: i
                        } });
                    }
                }
            }

            //keystate for buy
            if(this.keyState === 'Buy' || this.keyState === 'Sell') {

                if(cmd === 'c') {
                    this.keyState = null;
                    return;
                }

            }

        },
        doRepair() {
            // in theory not needed
            if(this.toRepairQty >= this.money) return;
            if(this.toRepairQty >= this.repairCost) this.toRepairQty = this.repairCost;

            this.$store.commit('repair', { total: this.toRepairQty, repairCost: this.repairCost });
            this.keyState = null;
        }

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是相当多的，我道歉。可能最有趣的方面是`doCommand`，在这里我响应键盘事件，并根据当前状态处理输入。我觉得这可以做得更好，但作为初稿，我很满意。

我不满意的一部分是`computed`中的所有项目，它们只是简单地接触到 Vuex 状态和它们的 getters。我知道我可以使用 [mapState](https://vuex.vuejs.org/guide/state.html#the-mapstate-helper) 来使它更干净一点，但是我决定暂时不做这个。(我准备在 myh 下一个演示中强制自己使用。)

除此之外，这里的大部分代码只是处理输入并与存储交互。这是我令人敬畏的设计的一个快速截屏。

[![Game screen](img/8d046de5ebbaee810a51dae5c02ed500.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ij-_6IzO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/08/taipan1.png)

我们来看看`Travel.vue`。这是您在端口之间移动时看到的临时屏幕。

```
<template>
    <div>
        <h1>On the sea...</h1>
        <p>
            You are on the way to {{ destination }}.
        </p>
        <p v-if="randomEvent">
            {{ randomMessage }}
        </p>

        <p v-if="damage >= 100">
            <strong>Your ship is completely destroyed!</strong>
        </p>
    </div>
</template>

<script>
export default {
    computed: {
        damage() {
            return this.$store.state.damage;
        },
        destination() {
            return this.$route.params.destination;
        },
        randomEvent() {
            return this.randomMessage !== '';
        },
        randomMessage() {
            return this.$store.state.randomMessage;
        }
    },
    created() {
        // check for random event
        this.$store.commit('generateRandomEvent', {destination: this.$route.params.destination});

        // this feels icky
        let destinationIndex = this.$route.params.destinationIndex;
        if(this.$store.state.newPortIndex) {
            destinationIndex = this.$store.state.newPortIndex;
        }

        let timeToWait = 1000;
        // if there was a special event, we need more time to read, and possibly end the game
        if(this.randomEvent) {
            timeToWait += 2000;
        }

        setTimeout(() => {
            console.log('done waiting');
            if(this.damage >= 100) {
                this.$router.replace('/end');
            } else {
                this.$store.commit('setPort', destinationIndex);
                this.$router.replace('/game');
            }
        }, timeToWait);
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这其中最有趣的是`created`中的`setTimeout`。这个想法是你进入这个视图，然后自动移出。通常这是在一秒钟内完成的，但是如果一个随机事件发生了，我会把它延迟到三秒钟，这样你就有时间去了解发生了什么。因为一个随机事件实际上可能会结束你的游戏，我在这里有一些逻辑来移动到结束视图。

最后，我们来看看店铺。我将把它分开一点，而不是整个粘贴上去。

```
/*
starting year for the game
*/
const BASE_YEAR = 1900;

const MONTHS = ["January", "February", "March", "April", "May", "June",
             "July", "August", "September", "October", "November", "December"];

/*
Ports. For now ports just have names but I may add boosts later, like port
X for good Y is good.
*/
const PORTS = [
  {
    name:'Bespin'
  },
  {
    name:'Dagobah'
  },
  {
    name:'Naboo'
  },
  {
    name:'Coruscant'
  },
  {
    name:'New Boston'
  }
];

/*
Goods have a value range representing, generally, what they will sell for.
illegal=true means there is a chance it will be stolen
*/
const GOODS = [
  {
    name:'General',
    salesRange: [5, 20],
    illegal:false
  },
  {
    name:'Arms',
    salesRange: [60, 120],
    illegal:false
  },
  {
    name:'Silk',
    salesRange: [200, 500],
    illegal:false
  },
  {
    name:'Spice',
    salesRange: [3000, 6000],
    illegal:true
  }

];

//how much each upgrade adds
const HOLD_UPGRADE = 10;

function getRandomInt(min, max) {
  min = Math.ceil(min);
  max = Math.floor(max);
  return Math.floor(Math.random() * (max - min)) + min; //The maximum is exclusive and the minimum is inclusive
} 
```

Enter fullscreen mode Exit fullscreen mode

我的商店从影响游戏的各种常数开始。你可以看到港口(是的，我从旧游戏中转移了一点)，货物，等等。港口现在只是名称，但我的意图是允许港口“偏爱”某些货物。商品支持价格下跌的范围，你可以看到我把`Spice`标为非法，但还没有实施。最后我得到了一个随机效用函数，`getRandomInt`。

下面是我的店铺状态:

```
state: {
    name:'',
    port:null,
    money:100000,
    turn:0,
    holdSize:100,
    hold:[],
    prices: [],
    damage:0,
    randomMessage:'',
    newPortIndex:null
}, 
```

Enter fullscreen mode Exit fullscreen mode

其中大部分是不言自明的，但请注意最后两项，`randomMessage`和`newPortIndex`，仅用于旅行时发生的特殊事件。

现在让我们看看各种突变。首先是`bootstrap`，它只是为一个新游戏设置一些东西。

```
bootstrap(state) {
    state.port = PORTS[0];
    GOODS.forEach(g => {
    state.hold.push({name:g.name, quantity: 0});
    });
}, 
```

Enter fullscreen mode Exit fullscreen mode

接下来是我的特殊事件处理:

```
/*
A random event is one of the following:
    Nothing (ie nothing happened, no event
    Storm sends you to X port
    Storm damages you Y percentage points
    Pirates attack - steal items + Y damage

Also note we skip random events for the first ten turns or so

*/
generateRandomEvent(state, info) {
    state.randomMessage = '';
    state.offerUpgrade = false;

    if(state.turn < 10) return;

    let rand = getRandomInt(0, 100);

    //nothing
    if(rand < 60) return;

    if(rand >= 60 && rand < 70) {
        console.log('storm redirection');
        let newPort = null;

        while(!newPort || newPort.name === info.destination.name) {
            state.newPortIndex = getRandomInt(0, PORTS.length);
            newPort = PORTS[state.newPortIndex];
        }
        state.randomMessage = 'A storm has blown you off course to ' + newPort.name;
        console.log(state.randomMessage);
    }

    if(rand >= 70 && rand < 80) {
        let damage = getRandomInt(1, 12);
        console.log('Storm damages you for '+damage);
        state.randomMessage = 'A violent storm damages your ship!';
        state.damage += damage;
    }

    if(rand >= 80 && rand < 90) {
        //note, if your hold is empty, we ignore everything;
        //now get the hold and filter to items with stuff
        let heldItems = state.hold.filter(h => {
            return h.quantity > 0;
        });
        if(heldItems.length === 0) return;

        console.log('pirates attack and damage and steal shit');
        //first, do damange, bit less than storm to be nice
        let damage = getRandomInt(1, 7);
        console.log('Storm damages you for ' + damage);

        console.log('state.hold with items',JSON.stringify(heldItems));
        //select the index to steal
        let stealIndex = getRandomInt(0, heldItems.length);
        console.log('going to steal from '+JSON.stringify(heldItems[stealIndex]));
        let stealAmt = getRandomInt(1, heldItems[stealIndex].quantity + 1);
        console.log('stealing '+stealAmt);
        let target = -1;
        for(let i=0;i<state.hold.length;i++) {
            if(heldItems[stealIndex].name === state.hold[i].name) target = i;
        }

        state.randomMessage = 'Pirates attack your ship and steal some cargo!';
        state.damage += damage;
        state.hold[target].quantity -= stealAmt;
    }

    if(rand >= 90) {
        state.offerUpgrade = true;
    }

}, 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我基本上只是选择一个随机数，根据结果，会发生一些不同的事情。其中一个(`offerUpgrade`)会在你到达港口的时候实际触发，而不是“在途”。

其余的变化是有趣的，因为主要应用商品变化，并进行修理或升级。在`getters`部分，我认为这些部分很整洁。

```
gameDate(state) {
    let years = Math.floor((state.turn-1)/12);
    let month = (state.turn-1) % 12;
    return `${MONTHS[month]} ${BASE_YEAR + years}`;
}, 
```

Enter fullscreen mode Exit fullscreen mode

getter 是我处理显示月与月、年与年之间的日期的方式。

```
rank(state) {
    // your final score is just based on money, cuz life
    if(state.money < 10000) return 'Deck Hand';
    if(state.money < 50000) return 'Ensign';
    if (state.money < 100000) return 'Lieutenant';
    if (state.money < 1000000) return 'Commander';
    //below is 10 million, just fyi ;)
    if (state.money < 10000000) return 'Captain';
    //below is 100 million, just fyi ;)
    if (state.money < 100000000) return 'Admiral';
    return 'Grand Admiral';
}, 
```

Enter fullscreen mode Exit fullscreen mode

getter 只是根据你赚的钱返回一个标签。请注意，我在那里使用了注释来帮助我阅读大数字。有一个关于数字分隔符的 ES 提案，旨在使这变得更容易。例如，假设最后一个条件是:

```
if (state.money < 100_000_000) return 'Admiral'; 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这还没有得到很好的支持。最新的 Chrome 有，Firefox 没有。

最后有趣的一点是处理船只升级的费用:

```
upgradeCost(state) {
    // the cost to upgrade is based on the size of your ship;
    let cost = state.holdSize * 200 * (1 + getRandomInt(5,10)/10);
    return Math.floor(cost);
} 
```

Enter fullscreen mode Exit fullscreen mode

我的目标是让它变得昂贵，并且随着你变得越来越大而逐渐变得昂贵。这是我在人们玩游戏和提供反馈时会做的调整。

无论如何，我希望这个演示对人们来说是有趣的，一如既往，我非常欢迎对我的设计决策的反馈和批评！请在下面给我留言，让我知道你的想法！

*由[约书亚·j·科滕](https://unsplash.com/@jcotten?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 Unsplash 上拍摄的标题照片*