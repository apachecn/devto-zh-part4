# 如果语句不进行比较，则直接转到 Else

> 原文：<https://dev.to/harishhrk/if-statement-is-not-comparing-it-goes-directly-to-else-442p>

var colors =[rgb(255，0，0]、[RGB(255，255，0]、[rgb(0，255，255)]、rgb(0，255)、RGB(0，255)、RGB(255，255)

var squares = document . query selector all("。方形”)；
var color picked = colors[3]；
var color display = document . getelementbyid(" color display ")；
var message display = document . query selector(" # message display ")；

color display . text content = color picked；

for(var I = 0；I<squares.length>squares[I]. style . background = colors[I]；</squares.length>

```
squares[i].addEventListener("click", function(){
    var clickedColor=this.style.background;

    if(clickedColor === colorPicked){
        messageDisplay.textContent = "Correct";
    }else{
    this.style.background="#232323";
        messageDisplay.textContent = "Try again";

    }
}); 
```

}