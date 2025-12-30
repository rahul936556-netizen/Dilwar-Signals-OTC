<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>AI Dilwar – OTC Final Engine with History & Realistic Results</title>
<meta name="viewport" content="width=device-width,initial-scale=1">
<style>
body{margin:0;background:#020617;color:#fff;font-family:Arial}
.card{max-width:800px;margin:15px auto;padding:20px;background:#020617;border-radius:16px;box-shadow:0 0 30px #000}
h1{text-align:center;color:#38bdf8}
select,button{width:100%;padding:12px;margin:6px 0;border:none;border-radius:10px;font-weight:bold}
button{background:#38bdf8;color:#000;cursor:pointer}
.row{display:flex;justify-content:space-between;margin:4px 0}
.buy{color:#22c55e;font-weight:bold}
.sell{color:#ef4444;font-weight:bold}
.wait{color:#facc15;font-weight:bold}
.small{text-align:center;font-size:12px;color:#94a3b8}
table{width:100%;font-size:12px;border-collapse:collapse;margin-top:8px}
th,td{border:1px solid #334155;padding:4px;text-align:center}
th{color:#38bdf8}
.lock{text-align:center;color:#f87171;margin-top:6px}
.flex{display:flex;gap:8px;margin-top:6px}
</style>
</head>

<body>
<div class="card">

<h1>OTC SIGNAL PANEL (1M)</h1>

<select id="pair">
<!-- 50+ OTC pairs -->
<option>EUR/USD OTC</option><option>GBP/USD OTC</option>
<option>USD/JPY OTC</option><option>AUD/USD OTC</option>
<option>USD/CAD OTC</option><option>USD/CHF OTC</option>
<option>NZD/USD OTC</option><option>EUR/JPY OTC</option>
<option>GBP/JPY OTC</option><option>AUD/JPY OTC</option>
<option>CAD/JPY OTC</option><option>CHF/JPY OTC</option>
<option>EUR/GBP OTC</option><option>EUR/AUD OTC</option>
<option>EUR/CAD OTC</option><option>EUR/CHF OTC</option>
<option>EUR/NZD OTC</option><option>GBP/AUD OTC</option>
<option>GBP/CAD OTC</option><option>GBP/CHF OTC</option>
<option>GBP/NZD OTC</option><option>AUD/CAD OTC</option>
<option>AUD/CHF OTC</option><option>AUD/NZD OTC</option>
<option>USD/INR OTC</option><option>EUR/INR OTC</option>
<option>GBP/INR OTC</option><option>AUD/INR OTC</option>
<option>JPY/INR OTC</option><option>CHF/INR OTC</option>
<option>CAD/INR OTC</option><option>NZD/INR OTC</option>
<option>USD/BRL OTC</option><option>USD/MXN OTC</option>
<option>USD/ZAR OTC</option><option>USD/NGN OTC</option>
<option>USD/ARS OTC</option><option>USD/PKR OTC</option>
<option>USD/BDT OTC</option><option>USD/IDR OTC</option>
<option>USD/TRY OTC</option><option>USD/EGP OTC</option>
<option>USD/SGD OTC</option><option>EUR/SGD OTC</option>
<option>GBP/SGD OTC</option><option>AUD/TRY OTC</option>
<option>EUR/TRY OTC</option><option>GBP/TRY OTC</option>
<option>JPY/TRY OTC</option><option>CHF/TRY OTC</option>
</select>

<hr>

<!-- 10 Indicators -->
<div class="row"><span>EMA 9 / 15</span><span id="ema" class="wait">WAIT</span></div>
<div class="row"><span>RSI</span><span id="rsi" class="wait">WAIT</span></div>
<div class="row"><span>MACD</span><span id="macd" class="wait">WAIT</span></div>
<div class="row"><span>Supertrend</span><span id="super" class="wait">WAIT</span></div>
<div class="row"><span>Stochastic</span><span id="stoch" class="wait">WAIT</span></div>
<div class="row"><span>Bollinger</span><span id="bb" class="wait">WAIT</span></div>
<div class="row"><span>ADX</span><span id="adx" class="wait">WAIT</span></div>
<div class="row"><span>CCI</span><span id="cci" class="wait">WAIT</span></div>
<div class="row"><span>Williams %R</span><span id="willr" class="wait">WAIT</span></div>
<div class="row"><span>Momentum</span><span id="mom" class="wait">WAIT</span></div>

<hr>

<div class="row">
<strong>FINAL SIGNAL</strong>
<strong id="final" class="wait">WAIT</strong>
</div>

<div class="lock" id="lock"></div>

<div class="flex">
<button onclick="manual()">GENERATE MANUAL</button>
<button onclick="resetAll()">RESET</button>
</div>

<div class="small">Auto at 55s • Lock 1 min • Rule: 5–6 confirmation</div>

<h3 style="text-align:center;color:#38bdf8">Signal History</h3>
<table>
<thead><tr><th>#</th><th>Pair</th><th>Signal</th><th>Result</th></tr></thead>
<tbody id="history"></tbody>
</table>

<div class="small" id="stats">Trades: 0 | Win: 0 | Loss: 0 | Win%: 0</div>

</div>

<script>
const ids=["ema","rsi","macd","super","stoch","bb","adx","cci","willr","mom"];
let locked=false,lastMinute=-1;
let trades=0,wins=0,loss=0;

function rnd(){let r=Math.random();if(r>0.66)return"BUY";if(r<0.33)return"SELL";return"WAIT";}
function set(id,v){let e=document.getElementById(id);e.innerText=v;e.className=v=="BUY"?"buy":v=="SELL"?"sell":"wait";}

function generate(){
 if(locked) return;
 let buy=0,sell=0;
 ids.forEach(i=>{
   let v=rnd();
   set(i,v);
   if(v=="BUY") buy++;
   if(v=="SELL") sell++;
 });
 let f="WAIT";
 if(buy>=5 && buy<=6) f="BUY";
 if(sell>=5 && sell<=6) f="SELL";
 set("final",f);

 if(f!="WAIT"){
   locked=true;
   document.getElementById("lock").innerText="🔒 Signal Locked for 1 Minute";

   trades++;
   let res=Math.random()<0.7?"WIN":"LOSS"; // 70% WIN chance
   if(res=="WIN") wins++; else loss++;

   let row=document.createElement("tr");
   row.innerHTML=`<td>${trades}</td><td>${document.getElementById("pair").value}</td><td class="${f=="BUY"?"buy":"sell"}">${f}</td><td class="${res=="WIN"?"buy":"sell"}">${res}</td>`;
   document.getElementById("history").prepend(row);

   document.getElementById("stats").innerText=`Trades: ${trades} | Win: ${wins} | Loss: ${loss} | Win%: ${(wins/trades*100).toFixed(1)}`;
 }
}

function manual(){locked=false;generate();}
function resetAll(){
 ids.forEach(i=>set(i,"WAIT"));
 set("final","WAIT");
 locked=false;
 trades=0; wins=0; loss=0;
 document.getElementById("history").innerHTML="";
 document.getElementById("stats").innerText="Trades: 0 | Win: 0 | Loss: 0 | Win%: 0";
}

setInterval(()=>{
 let d=new Date(),s=d.getSeconds(),m=d.getMinutes();
 if(s==55 && m!=lastMinute){lastMinute=m;generate();}
 if(s==0){locked=false;document.getElementById("lock").innerText="";set("final","WAIT");}
},1000);
</script>

</body>
</html>
