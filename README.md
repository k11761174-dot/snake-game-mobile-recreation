# snake-game-mobile-recreation
its just a snake game but in mobile


<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Mobile Snake</title>
<style>
body {
 background:#080818;color:#00ff66;
 font-family:monospace;text-align:center;
 margin:0;padding:10px;
 touch-action:manipulation;
}
h1 {text-shadow:0 0 12px #00ff66}
canvas {
 width:min(92vw,400px);height:auto;
 background:black;border:3px solid #00ff66;
 box-shadow:0 0 15px #00ff66;
 touch-action:none;
}
button {
 background:#00ff66;color:black;border:0;
 border-radius:10px;padding:14px;
 font-size:18px;font-weight:bold;
}
.controls {
 display:grid;
 grid-template-columns:65px 65px 65px;
 grid-template-rows:55px 55px;
 gap:8px;justify-content:center;margin:15px;
}
.controls button {background:#087d45;color:white}
.up {grid-column:2}
.left {grid-column:1;grid-row:2}
.down {grid-column:2;grid-row:2}
.right {grid-column:3;grid-row:2}
</style>
</head>
<body>

<h1>🐍 RETRO SNAKE</h1>
<h2>Score: <span id="score">0</span></h2>

<canvas id="game" width="400" height="400"></canvas>

<p>
<button onclick="restart()">RESTART</button>
<button onclick="pause()">PAUSE</button>
</p>

<div class="controls">
<button class="up" onclick="turn(0,-1)">⬆️</button>
<button class="left" onclick="turn(-1,0)">⬅️</button>
<button class="down" onclick="turn(0,1)">⬇️</button>
<button class="right" onclick="turn(1,0)">➡️</button>
</div>

<p>Swipe the screen or tap the arrows!</p>

<script>
const canvas=document.getElementById("game");
const ctx=canvas.getContext("2d");
const SIZE=20;
const CELLS=20;

let snake,food,dir,nextDir;
let score,timer,speed;
let running=false,paused=false,over=false;

function newFood(){
 let f;
 do {
  f={
   x:Math.floor(Math.random()*CELLS),
   y:Math.floor(Math.random()*CELLS)
  };
 } while(snake.some(s=>s.x===f.x&&s.y===f.y));
 return f;
}

function restart(){
 clearInterval(timer);
 snake=[{x:10,y:10},{x:9,y:10},{x:8,y:10}];
 dir={x:1,y:0};
 nextDir={x:1,y:0};
 score=0;speed=140;
 food=newFood();
 running=true;paused=false;over=false;
 updateScore();
 draw();
 timer=setInterval(step,speed);
}

function updateScore(){
 document.getElementById("score").textContent=score;
}

function turn(x,y){
 if(!running||paused||over)return;
 if(x===-dir.x&&y===-dir.y)return;
 nextDir={x,y};
}

function step(){
 if(!running||paused||over)return;

 dir=nextDir;
 let head={
  x:snake[0].x+dir.x,
  y:snake[0].y+dir.y
 };

 let eating=head.x===food.x&&head.y===food.y;

 if(head.x<0||head.x>=CELLS||
    head.y<0||head.y>=CELLS||
    snake.slice(0,eating?snake.length:snake.length-1)
    .some(s=>s.x===head.x&&s.y===head.y)){
  over=true;clearInterval(timer);draw();return;
 }

 snake.unshift(head);

 if(eating){
  score+=10;
  updateScore();
  food=newFood();

  clearInterval(timer);
  speed=Math.max(55,speed-2);
  timer=setInterval(step,speed);
 }else{
  snake.pop();
 }
 draw();
}

function draw(){
 ctx.fillStyle="black";
 ctx.fillRect(0,0,400,400);

 ctx.fillStyle="red";
 ctx.fillRect(food.x*SIZE,food.y*SIZE,SIZE,SIZE);

 snake.forEach((s,i)=>{
  ctx.fillStyle=i===0?"#00ff66":"#00aa44";
  ctx.fillRect(s.x*SIZE+1,s.y*SIZE+1,18,18);
 });

 if(paused||over){
  ctx.fillStyle="white";
  ctx.font="bold 25px monospace";
  ctx.textAlign="center";
  ctx.fillText(over?"GAME OVER":"PAUSED",200,190);
  ctx.font="14px monospace";
  ctx.fillText("Press RESTART",200,220);
  ctx.textAlign="left";
 }
}

function pause(){
 if(!running||over)return;
 paused=!paused;
 draw();
}

let sx=0,sy=0;
canvas.addEventListener("touchstart",e=>{
 sx=e.touches[0].clientX;
 sy=e.touches[0].clientY;
},{passive:true});

canvas.addEventListener("touchend",e=>{
 let dx=e.changedTouches[0].clientX-sx;
 let dy=e.changedTouches[0].clientY-sy;
 if(Math.max(Math.abs(dx),Math.abs(dy))<15)return;
 if(Math.abs(dx)>Math.abs(dy))
  turn(dx>0?1:-1,0);
 else
  turn(0,dy>0?1:-1);
},{passive:true});

document.addEventListener("keydown",e=>{
 const k={
  ArrowUp:[0,-1],ArrowDown:[0,1],
  ArrowLeft:[-1,0],ArrowRight:[1,0]
 };
 if(k[e.key]){
  e.preventDefault();
  turn(...k[e.key]);
 }
});

restart();
</script>
</body>
</html>
