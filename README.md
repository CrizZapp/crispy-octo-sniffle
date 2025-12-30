<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mini Pou Interactivo</title>
<style>
* { margin:0; padding:0; box-sizing:border-box; }

body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display:flex;
  flex-direction:column;
  align-items:center;
  justify-content:center;
  min-height:100vh;
  padding:20px;
  transition: background 1s ease;
}

body.sleeping { background: linear-gradient(135deg, #2c003e 0%, #1a0033 100%); }
body.playing { background: linear-gradient(135deg, #ffd89b 0%, #19547b 100%); }

h1 {
  color:white;
  text-shadow: 2px 2px 8px rgba(0,0,0,0.3);
  margin-bottom:20px;
  font-size:2.5em;
  text-align:center;
}

.stats-container {
  display:flex;
  gap:20px;
  margin-bottom:20px;
  flex-wrap:wrap;
  justify-content:center;
}

.stat-card {
  background: rgba(255,255,255,0.95);
  padding:15px 25px;
  border-radius:15px;
  box-shadow: 0 8px 20px rgba(0,0,0,0.2);
  min-width:150px;
}

.stat-label {
  font-weight:bold;
  color:#555;
  font-size:0.9em;
  margin-bottom:8px;
  display:flex;
  align-items:center;
  gap:8px;
}

.stat-icon { font-size:1.3em; }

.bar {
  width:100%;
  height:20px;
  background:#e0e0e0;
  border-radius:10px;
  overflow:hidden;
  position:relative;
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.1);
}

.fill {
  height:100%;
  border-radius:10px;
  transition: width 0.5s ease, background 0.3s ease;
  position:relative;
  overflow:hidden;
}

.fill::after {
  content:'';
  position:absolute;
  top:0; left:0; right:0; bottom:0;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.3), transparent);
  animation: shimmer 2s infinite;
}

@keyframes shimmer {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(100%); }
}

.hunger-fill { background: linear-gradient(to right, #ff6b6b, #ee5a6f); }
.fun-fill { background: linear-gradient(to right, #4facfe, #00f2fe); }
.energy-fill { background: linear-gradient(to right, #43e97b, #38f9d7); }

.stat-value {
  position:absolute;
  top:50%; left:50%;
  transform:translate(-50%, -50%);
  font-weight:bold;
  font-size:0.75em;
  color:white;
  text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
  z-index:1;
}

#game {
  position:relative;
  width:450px;
  max-width:95vw;
  height:550px;
  border:4px solid rgba(255,255,255,0.3);
  border-radius:30px;
  overflow:hidden;
  background: linear-gradient(180deg, #87ceeb 0%, #98d8c8 100%);
  box-shadow: 0 20px 60px rgba(0,0,0,0.4);
  transition: background 0.5s ease, transform 0.3s ease;
}

#game:hover { transform: scale(1.02); }

.floor {
  position:absolute;
  bottom:0;
  width:100%;
  height:80px;
  background: linear-gradient(180deg, #8b7355 0%, #6b5344 100%);
  border-top:3px solid #5a4233;
}

#pou {
  position:absolute;
  width:110px; height:130px;
  bottom:80px;
  left:50%;
  transform: translateX(-50%);
  background: radial-gradient(circle at 30% 30%, #ffdd99, #ffcc66);
  border-radius: 45% 45% 50% 50%;
  border:3px solid #d9a84e;
  display:flex;
  flex-direction:column;
  justify-content:center;
  align-items:center;
  box-shadow:0 15px 30px rgba(0,0,0,0.3);
  transition: all 0.3s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  z-index:10;
}

#pou.bouncing { animation: bounce 0.6s ease; }

@keyframes bounce {
  0%,100%{ transform: translateX(-50%) translateY(0);}
  25%{ transform: translateX(-50%) translateY(-30px);}
  50%{ transform: translateX(-50%) translateY(-50px);}
  75%{ transform: translateX(-50%) translateY(-20px);}
}

.eye-container {
  display:flex;
  justify-content:space-between;
  width:55%;
  margin-top:20px;
}

.eye {
  width:18px; height:18px;
  background: radial-gradient(circle at 40% 40%, #333, #000);
  border-radius:50%;
  position:relative;
  transition: all 0.2s ease;
}

.eye::after {
  content:'';
  position:absolute;
  width:6px; height:6px;
  background:white;
  border-radius:50%;
  top:3px; left:4px;
}

.mouth {
  width:45px; height:15px;
  border-bottom:5px solid #333;
  border-radius:0 0 20px 20px;
  margin-top:15px;
  transition: all 0.3s ease;
}

.mouth.happy { border-radius:0 0 25px 25px; width:50px; }
.mouth.sad { border-bottom:none; border-top:5px solid #333; border-radius:25px 25px 0 0; margin-top:20px;}

.arm {
  width:14px; height:40px;
  background: linear-gradient(to bottom, #ffdd99, #ffcc66);
  position:absolute;
  top:55px;
  border-radius:7px;
  border:2px solid #d9a84e;
  transform-origin: top center;
  transition: all 0.2s ease;
}

.arm.left{ left:-14px; } .arm.right{ right:-14px; }

.leg {
  width:30px; height:18px;
  background: linear-gradient(to bottom, #ffcc66, #ffbb44);
  position:absolute;
  bottom:-15px;
  border-radius:0 0 15px 15px;
  border:2px solid #d9a84e;
  border-top:none;
}

.leg.left{ left:15px; } .leg.right{ right:15px; }

.controls {
  display:flex;
  gap:12px;
  margin-top:25px;
  flex-wrap:wrap;
  justify-content:center;
}

button {
  padding:14px 28px;
  border:none;
  border-radius:15px;
  color:white;
  font-weight:bold;
  font-size:1em;
  cursor:pointer;
  box-shadow:0 6px 15px rgba(0,0,0,0.3);
  transition:all 0.3s ease;
  display:flex;
  align-items:center;
  gap:8px;
}

button:hover { transform:translateY(-3px); box-shadow:0 10px 25px rgba(0,0,0,0.4);}
button:active { transform:translateY(0); }

button.food-btn{ background: linear-gradient(135deg,#f093fb 0%,#f5576c 100%);}
button.play-btn{ background: linear-gradient(135deg,#4facfe 0%,#00f2fe 100%);}
button.clean-btn{ background: linear-gradient(135deg,#43e97b 0%,#38f9d7 100%);}
button.sleep-btn{ background: linear-gradient(135deg,#fa709a 0%,#fee140 100%);}
button.reset-btn{ background: linear-gradient(135deg,#30cfd0 0%,#330867 100%);}

.food {
  position:absolute;
  width:55px; height:55px;
  cursor:grab;
  user-select:none;
  transition: transform 0.2s ease, filter 0.2s ease;
  filter:drop-shadow(0 6px 12px rgba(0,0,0,0.3));
  z-index:5;
  animation: foodAppear 0.4s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

@keyframes foodAppear{
  0%{ transform: scale(0) rotate(0deg);}
  100%{ transform: scale(1) rotate(360deg);}
}

.food:active{ cursor:grabbing; transform: scale(1.1); }

.pillow{
  position:absolute;
  width:130px; height:25px;
  background: linear-gradient(135deg,#fff4e6,#ffe4b5);
  border:3px solid #d4af37;
  bottom:75px; left:50%;
  transform:translateX(-50%);
  border-radius:15px;
  display:none;
  box-shadow:0 5px 15px rgba(0,0,0,0.3);
}

.bubble{
  position:absolute;
  border-radius:50%;
  background: radial-gradient(circle at 30% 30%, rgba(255,255,255,0.8), rgba(200,230,255,0.4));
  box-shadow: inset 0 -2px 5px rgba(0,0,0,0.1);
  animation: float 4s ease-in-out infinite;
  pointer-events:none;
}

@keyframes float{
  0%{ transform:translateY(0) scale(0.3); opacity:0.9;}
  50%{ transform:translateY(-80px) scale(1); opacity:0.6;}
  100%{ transform:translateY(-150px) scale(0.4); opacity:0;}
}

.particle{ position:absolute; pointer-events:none; animation:particleFade 1s ease-out forwards;}
@keyframes particleFade{ 0%{transform:translateY(0) scale(1);opacity:1;} 100%{transform:translateY(-50px) scale(0);opacity:0;}}

.cloud{
  position:absolute;
  background:rgba(255,255,255,0.7);
  border-radius:100px;
  animation: cloudFloat 20s linear infinite;
}

@keyframes cloudFloat{
  0%{ left:-100px;} 100%{ left:110%;}
}

.status-message{
  position:absolute;
  top:20px;
  left:50%;
  transform:translateX(-50%);
  background: rgba(0,0,0,0.7);
  color:white;
  padding:10px 20px;
  border-radius:20px;
  font-size:0.9em;
  opacity:0;
  transition:opacity 0.3s ease;
  pointer-events:none;
  z-index:100;
}

.status-message.show{ opacity:1; }

@media(max-width:600px){
  h1{ font-size:1.8em;}
  #game{ height:450px;}
  .controls{ gap:8px;}
  button{ padding:10px 18px; font-size:0.9em;}
}
</style>
</head>
<body>
<h1>🎮 Mini Pou Interactivo</h1>

<div class="stats-container">
  <div class="stat-card">
    <div class="stat-label"><span class="stat-icon">🍕</span> Hambre</div>
    <div class="bar"><div id="hunger" class="fill hunger-fill"></div><div class="stat-value" id="hunger-value">100%</div></div>
  </div>
  <div class="stat-card">
    <div class="stat-label"><span class="stat-icon">🎉</span> Diversión</div>
    <div class="bar"><div id="fun" class="fill fun-fill"></div><div class="stat-value" id="fun-value">100%</div></div>
  </div>
  <div class="stat-card">
    <div class="stat-label"><span class="stat-icon">⚡</span> Energía</div>
    <div class="bar"><div id="energy" class="fill energy-fill"></div><div class="stat-value" id="energy-value">100%</div></div>
  </div>
</div>

<div id="game">
  <div class="status-message" id="status-message"></div>
  <div class="floor"></div>
  <div id="pou">
    <div class="eye-container">
      <div class="eye" id="eye-left"></div>
      <div class="eye" id="eye-right"></div>
    </div>
    <div class="mouth" id="mouth"></div>
    <div class="arm left" id="arm-left"></div>
    <div class="arm right" id="arm-right"></div>
    <div class="leg left"></div>
    <div class="leg right"></div>
  </div>
  <div id="pillow" class="pillow"></div>
</div>

<div class="controls">
  <button class="food-btn" onclick="generateFood()">🍎 Comida</button>
  <button class="play-btn" onclick="play()">⚽ Jugar</button>
  <button class="clean-btn" onclick="clean()">🧼 Limpiar</button>
  <button class="sleep-btn" onclick="sleep()">😴 Dormir</button>
  <button class="reset-btn" onclick="resetPou()">🔄 Reiniciar</button>
</div>

<!-- SCRIPT COMPLETO -->
<script>
let hunger = 100, fun = 100, energy = 100, sleeping = false;  
const game = document.getElementById('game');  
const pou = document.getElementById('pou');  
const eyeL = document.getElementById('eye-left');  
const eyeR = document.getElementById('eye-right');  
const mouth = document.getElementById('mouth');  
const armL = document.getElementById('arm-left');  
const armR = document.getElementById('arm-right');  
const pillow = document.getElementById('pillow');  
const statusMsg = document.getElementById('status-message');  

// Actualiza barras
function updateBars() {
  document.getElementById('hunger').style.width = hunger + '%';
  document.getElementById('fun').style.width = fun + '%';
  document.getElementById('energy').style.width = energy + '%';
  document.getElementById('hunger-value').textContent = Math.round(hunger) + '%';
  document.getElementById('fun-value').textContent = Math.round(fun) + '%';
  document.getElementById('energy-value').textContent = Math.round(energy) + '%';
  updateMood();
}

// Cambia expresión de Pou
function updateMood() {
  const avg = (hunger + fun + energy)/3;
  if(avg > 60) mouth.className = 'mouth happy';
  else if(avg < 30) mouth.className = 'mouth sad';
  else mouth.className = 'mouth';
}

// Mensaje flotante
function showStatus(msg){
  statusMsg.textContent = msg;
  statusMsg.classList.add('show');
  setTimeout(()=>statusMsg.classList.remove('show'),2000);
}

// Animaciones de Pou
function pouAnimate(action){
  if(action === 'play'){
    pou.classList.add('bouncing');
    armL.style.transform='rotate(-50deg)';
    armR.style.transform='rotate(50deg)';
    document.body.className='playing';
    showStatus('¡Qué divertido! 🎉');
    setTimeout(()=>{ 
      pou.classList.remove('bouncing');
      armL.style.transform='rotate(0deg)';
      armR.style.transform='rotate(0deg)';
      document.body.className='';
    },1000);
  } else if(action==='clean'){
    pou.style.transform='translateX(-50%) rotate(360deg)';
    armL.style.transform='rotate(60deg)';
    armR.style.transform='rotate(-60deg)';
    showStatus('¡Qué limpio! 🧼');
    setTimeout(()=>{
      pou.style.transform='translateX(-50%) rotate(0deg)';
      armL.style.transform='rotate(0deg)';
      armR.style.transform='rotate(0deg)';
    },800);
  } else if(action==='sleep'){
    sleeping = true;
    eyeL.style.height='3px';
    eyeR.style.height='3px';
    mouth.style.opacity='0.3';
    document.body.className='sleeping';
    pillow.style.display='block';
    showStatus('Zzz... 💤');
    createBubbles();
    let breathe=setInterval(()=>{
      pou.style.transform='translateX(-50%) scale(1.03)';
      setTimeout(()=>pou.style.transform='translateX(-50%) scale(1)',500);
    },1200);
    setTimeout(()=>{
      sleeping=false;
      clearInterval(breathe);
      eyeL.style.height='18px';
      eyeR.style.height='18px';
      mouth.style.opacity='1';
      pou.style.transform='translateX(-50%) scale(1)';
      document.body.className='';
      pillow.style.display='none';
      removeBubbles();
      showStatus('¡Buenos días! 🌅');
    },6000);
  } else if(action==='eat'){
    armL.style.transform='rotate(-45deg)';
    armR.style.transform='rotate(45deg)';
    pou.style.transform='translateX(-50%) scale(1.08)';
    showStatus('¡Delicioso! 🍕');
    setTimeout(()=>{
      armL.style.transform='rotate(0deg)';
      armR.style.transform='rotate(0deg)';
      pou.style.transform='translateX(-50%) scale(1)';
    },800);
  }
}

// Acciones
function play(){
  if(!sleeping && energy>20){
    fun+=25; energy-=15;
    if(fun>100) fun=100; if(energy<0) energy=0;
    updateBars();
    pouAnimate('play');
  } else showStatus('¡Muy cansado! 😴');
}
function clean(){
  if(!sleeping){
    fun+=15; if(fun>100) fun=100;
    updateBars();
    pouAnimate('clean');
  }
}
function sleep(){
  if(!sleeping && energy<80) pouAnimate('sleep');
  else if(energy>=80) showStatus('¡No tengo sueño! 😊');
}
function resetPou(){
  hunger=100; fun=100; energy=100;
  updateBars();
  document.querySelectorAll('.food').forEach(f=>f.remove());
  showStatus('¡Reiniciado! ✨');
}

// Comida
const foodTypes=[
  {name:'apple',emoji:'🍎',nutrition:20},
  {name:'pizza',emoji:'🍕',nutrition:30},
  {name:'donut',emoji:'🍩',nutrition:15}
];

function generateFood(){
  if(document.querySelectorAll('.food').length>=5){
    showStatus('¡Demasiada comida! 🍕'); return;
  }
  const food=document.createElement('div');
  const type=foodTypes[Math.floor(Math.random()*foodTypes.length)];
  food.className='food';
  food.dataset.nutrition=type.nutrition;
  food.textContent=type.emoji;
  food.style.top=Math.random()*350+'px';
  food.style.left=Math.random()*(game.offsetWidth-60)+'px';
  food.style.fontSize='45px';
  game.appendChild(food);
  initDrag(food);
}

// Drag & Drop
function initDrag(food){
  let offsetX=0, offsetY=0, dragging=false;
  const start=(e)=>{
    dragging=true;
    const clientX=e.type.includes('touch')?e.touches[0].clientX:e.clientX;
    const clientY=e.type.includes('touch')?e.touches[0].clientY:e.clientY;
    const rect=food.getBoundingClientRect();
    offsetX=clientX-rect.left;
    offsetY=clientY-rect.top;
    food.style.zIndex='15';
  };
  const move=(e)=>{
    if(!dragging) return;
    const clientX=e.type.includes('touch')?e.touches[0].clientX:e.clientX;
    const clientY=e.type.includes('touch')?e.touches[0].clientY:e.clientY;
    const gameRect=game.getBoundingClientRect();
    food.style.left=(clientX-offsetX-gameRect.left)+'px';
    food.style.top=(clientY-offsetY-gameRect.top)+'px';
  };
  const end=()=>{
    dragging=false;
    food.style.zIndex='5';
  };
  food.addEventListener('mousedown',start);
  document.addEventListener('mousemove',move);
  document.addEventListener('mouseup',end);
  food.addEventListener('touchstart',start);
  document.addEventListener('touchmove',move);
  document.addEventListener('touchend',end);

  const collision=setInterval(()=>{
    if(!document.body.contains(food)){ clearInterval(collision); return; }
    const fRect=food.getBoundingClientRect();
    const mRect=mouth.getBoundingClientRect();
    if(!(fRect.right<mRect.left || fRect.left>mRect.right || fRect.bottom<mRect.top || fRect.top>mRect.bottom)){
      hunger+=parseInt(food.dataset.nutrition);
      if(hunger>100) hunger=100;
      updateBars();
      pouAnimate('eat');
      clearInterval(collision);
      food.remove();
    }
  },50);
}

// Burbujas al dormir
function createBubbles(){
  for(let i=0;i<8;i++){
    setTimeout(()=>{
      const b=document.createElement('div');
      b.className='bubble';
      const size=Math.random()*15+10;
      b.style.width=size+'px';
      b.style.height=size+'px';
      b.style.left=(pou.offsetLeft+Math.random()*60+25)+'px';
      b.style.bottom='130px';
      b.style.animationDelay=Math.random()*2+'s';
      game.appendChild(b);
      setTimeout(()=>b.remove(),5000);
    },i*400);
  }
}
function removeBubbles(){ document.querySelectorAll('.bubble').forEach(b=>b.remove()); }

// Disminución automática
setInterval(()=>{
  if(!sleeping){ hunger-=0.8; fun-=0.6; energy-=0.4; }
  else{ hunger-=0.2; fun+=0.1; energy+=1.5; }
  if(hunger<0) hunger=0; if(fun<0) fun=0; if(energy<0) energy=0;
  if(energy>100) energy=100;
  updateBars();
},2500);

// Parpadeo
setInterval(()=>{
  if(!sleeping){
    eyeL.style.height='3px'; eyeR.style.height='3px';
    setTimeout(()=>{ eyeL.style.height='18px'; eyeR.style.height='18px'; },150);
  }
},4500);

updateBars();
showStatus('¡Hola! 🎮');
</script>

</body>
</html>
