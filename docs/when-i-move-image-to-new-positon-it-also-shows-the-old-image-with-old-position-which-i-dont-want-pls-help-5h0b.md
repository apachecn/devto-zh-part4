# 当我移动图片到新的位置时，它也显示旧的图片和旧的位置，我不想要请帮助

> 原文：<https://dev.to/danish_kalim_1/when-i-move-image-to-new-positon-it-also-shows-the-old-image-with-old-position-which-i-dont-want-pls-help-5h0b>

```
 const cvs = document.getElementById("game");
const ctx = cvs.getContext("2d");
const ROW = 15;
const COL =15;
const VACANT = "WHITE"
const SQ =40;
function drawSquare(x,y,color){
  ctx.fillStyle = color;
  ctx.fillRect(x*SQ,y*SQ,SQ,SQ);
  ctx.strokeStyle = "Black";
  ctx.strokeRect(x*SQ,y*SQ,SQ,SQ);
}
//create board 
let board= [];
for(r =0;r<ROW; r++){
   board[r] = [];
   for(c=0;c<COL;c++){
       board[r][c]=VACANT;
   }
}
//Draw the Board
function drawBoard(){
  for(r =0;r<ROW; r++){
   for(c=0;c<COL;c++){
       drawSquare(c,r,board[r][c]);
   }
  }
}

drawBoard();

let foodImg = new Image();
foodImg.src = "images/food.png" ;
let playerImg1 = new Image();
playerImg1.src = "images/player1.jpg" ;
let playerImg2 = new Image();
playerImg2.src = "images/player2.jpg" ;
//Block image
let blockImg = new Image();
blockImg.src = "images/block.jpg" ;
//let  blockImg = document.getElementById('block1');
let food = {
x: Math.floor(Math.random()*8 + 12) * SQ,
y: Math.floor(Math.random()*13 + 1) * SQ
}
let player1 ={
x: Math.floor(Math.random()*0 + 2) * SQ,
y: Math.floor(Math.random()*4+ 0) * SQ

}
let player2 ={
x: Math.floor(Math.random()*3 + 12) * SQ,
y: Math.floor(Math.random()*13 + 6) * SQ

}
//control Player
let d;
document.addEventListener("keydown",direction)
function direction(event){

  if(event.keyCode == 37){

      player1.x -= SQ * 3;

  }else if(event.keyCode == 38 ){
     player1.y -= SQ * 3;
  }else if(event.keyCode == 39){
     player1.x += SQ * 3;
  }else if(event.keyCode == 40){
     player1.y += SQ * 3;
  }

moveCharacter();
}
function moveCharacter(){
 ctx.clearRect(0,0, player1.x.width, player1.y.height);

  ctx.drawImage(playerImg1,player1.x,player1.y) ;

  ctx.drawImage(playerImg2,player2.x,player2.y);

}
function draw(){

    ctx.drawImage(foodImg,food.x,food.y);
    ctx.drawImage(playerImg1,player1.x,player1.y);

    ctx.drawImage(playerImg2,player2.x,player2.y);
    for(let i = 0 ; i<=10; i++ ){
        let block ={
x: Math.floor(Math.random()*8 + 3) * SQ,
y: Math.floor(Math.random()*13 + 2) * SQ

}    

let j = ctx.drawImage(blockImg,block.x,block.y) ;

      j

    }

}

draw();
var button = document.getElementById("button");
button.addEventListener("click", draw); 
```