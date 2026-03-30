<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Ranking List</title>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  :root {
    --bg: #f8f7f4;
    --surface: #ffffff;
    --surface2: #f1efe8;
    --border: rgba(0,0,0,0.1);
    --border2: rgba(0,0,0,0.18);
    --text: #1a1a18;
    --text2: #5f5e5a;
    --text3: #888780;
    --accent: #378ADD;
    --gold: #B37A1C;
    --radius: 10px;
    --radius-sm: 6px;
  }
  body { font-family: 'Georgia', serif; background: var(--bg); color: var(--text); min-height: 100vh; padding: 2rem 1rem; }
  h1 { font-size: 28px; font-weight: normal; letter-spacing: -0.5px; margin-bottom: 4px; }
  .subtitle { font-size: 14px; color: var(--text2); margin-bottom: 1.5rem; font-family: 'Courier New', monospace; }
  .controls { display: flex; gap: 8px; margin-bottom: 1.25rem; flex-wrap: wrap; }
  .controls input, .controls select {
    padding: 7px 12px; font-size: 13px; border: 1px solid var(--border2);
    border-radius: var(--radius-sm); background: var(--surface); color: var(--text);
    font-family: 'Courier New', monospace; outline: none;
  }
  .controls input { flex: 1; min-width: 160px; }
  .controls input:focus, .controls select:focus { border-color: var(--accent); }
  .summary { display: flex; gap: 10px; margin-bottom: 1.25rem; flex-wrap: wrap; }
  .card { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 10px 16px; flex: 1; min-width: 100px; }
  .card-label { font-size: 11px; color: var(--text3); margin-bottom: 3px; text-transform: uppercase; letter-spacing: 0.5px; font-family: 'Courier New', monospace; }
  .card-val { font-size: 22px; font-weight: normal; color: var(--text); }
  .table-wrap { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); overflow: hidden; }
  table { width: 100%; border-collapse: collapse; font-size: 13px; }
  th { text-align: left; padding: 9px 12px; background: var(--surface2); border-bottom: 1px solid var(--border2); color: var(--text2); font-weight: normal; cursor: pointer; white-space: nowrap; font-family: 'Courier New', monospace; font-size: 12px; text-transform: uppercase; letter-spacing: 0.4px; }
  th:hover { color: var(--text); }
  td { padding: 8px 12px; border-bottom: 1px solid var(--border); vertical-align: middle; }
  tr:last-child td { border-bottom: none; }
  tr:hover td { background: var(--surface2); }
  .rank { color: var(--text3); width: 40px; font-family: 'Courier New', monospace; }
  .rank-top { color: var(--gold); font-weight: bold; }
  .score { font-family: 'Courier New', monospace; text-align: right; }
  .zero { color: var(--text3); }
  .bar-cell { padding-left: 16px; width: 140px; }
  .bar { height: 6px; border-radius: 3px; background: var(--surface2); }
  .bar-fill { height: 6px; border-radius: 3px; background: var(--accent); transition: width 0.3s; }
  .medal { margin-right: 4px; }
  @media (max-width: 560px) { .bar-cell { display: none; } }
</style>
</head>
<body>
<h1>Rankings</h1>
<p class="subtitle">Competition standings — all participants</p>

<div class="controls">
  <input type="text" id="search" placeholder="Search name..." oninput="render()" />
  <select id="filter" onchange="render()">
    <option value="all">All participants</option>
    <option value="active">With scores only</option>
    <option value="zero">Zero scores only</option>
  </select>
</div>
<div class="summary" id="summary"></div>
<div class="table-wrap">
<table>
  <thead>
    <tr>
      <th onclick="sortBy('rank')"># Rank</th>
      <th onclick="sortBy('name')">Name</th>
      <th onclick="sortBy('score')" style="text-align:right">Score</th>
      <th class="bar-cell">Distribution</th>
    </tr>
  </thead>
  <tbody id="tbody"></tbody>
</table>
</div>

<script>
const raw = [
["Chuquichanca, David",101.25],["Lopez, Roberto",0],["Peña, Ruth",71],["Purizaca, Edson",217],["Aguilar, Ximena",68],["Aspajo, Alvin",0],["Falcon, Mario",51],["Marquez, Lia",237.75],["Sabitu, Hafsa",91.8],["Sanchez, Xiomara",0],["Sacbaja, Ajbe",54.5],["Corpuz, Lory",55.25],["Diergaardt, Claudio",8.66],["Hernadez, Alexa",54],["Torres, Laura",105],["Camacho, Mariela",32],["Marin, Claudia",142.75],["Corpuz, Riz",134],["Southall, Jamaica",27],["Lariba, Esperanza",94.5],["Hussain, Aliyu",52],["Ilaida, Cherry",43.5],["Adam, Daylen",0],["Castro, Sara",0],["Martinez, Carolina",67.08],["Eke, Martha",12],["Osorio, Grecia",0],["Correa, Wendy",80],["Sierra, Sara",212],["Loo, Jose",0],["Hernandez, Andres",173.25],["Rozo, Eduardo",39.5],["Shode, Dolapo",94.3],["Gavilanga, Marta",61],["Dubon, Karina",0],["Arguelles, Miranda",48.5],["Ramon, Bryan",141],["Alvarado, Erika",0],["Portales, Maria",114.25],["Carrillo, Astrid",90.25],["Nunez, Paulina",19],["Farías, Sofia",28.5],["Lopez, Fernanda",29.55],["Juarez, Lautaro",178],["Rodriguez, Sebastian",189.2],["Marin, Jashira",53.3],["Osuna, Andrea",54],["Delgado, Jose",109.5],["Criollo, Luis",71],["Blessing, Terngu",0],["Garcia, Mayra",74.6],["Gonzalez, Luz",0],["Salomon, Emanuel",43.58],["Dimaano, Leihf",114.97],["Fortes, Gia",0],["Amaya, Pamela",0],["Hernandez, Jennifer",278.5],["Gabriel, Fernanda",2.5],["Hernandez, Iris",0],["Sanchez, Luis",0],["Saavedra, Roniel",0],["Aguilar, Charisma",64.95],["Galvan, Jorge",1.5],["Castaño, Irish Gealic",56.67],["VanDort, Simonna",76],["Peralta, Jorge",83],["Cecilia, Alva",0],["Lopez, Ferliza",74.71],["Pedotti, Alan",56.6],["Techon, Monica",125.75],["Corrales, Jessie",10],["Villalona, Carlo",0],["Castañeda, Leticia",15],["Pica, Rafael",0],["Alombro, Albert",22],["Sompaya Nyame, Innocent",32],["Bantayan, Rachell",0],["Garcia, Nathaly",1],["Bargo, John Oscar Keith",22],["Rangel, Alessandra",31.5],["Carriaga, Angelica",70],["Cuarto, Ken Andrei",58],["Hayag, Ma. Aurora",111.1],["Appendino, Maximo",15],["Argüelles, Rodrigo",42.1],["Bagatello, Giuliana",1.5],["Kaye, Zyra",69.5],["Ismaila Zakari, Muhammad",72.25],["Rosales, Kenneth Jairo",75],["Benyin, Prince",17],["Onumonu, Wali Gesiere",21],["Habib, Aisha",29.5],["Imaz, Gabriel Agustin",214],["Romagnoli Ochoa, Monica",123.75],["Genson, Jeanny",42.13],["Lucas, Princess Hanna",79.92],["Padin, Orlando Jr.",0],["Jabo, Umar",75.9],["Castellanos, Maricarmen",0],["Sabitu, Asmau",87],["Ofalsa, Dianna",80],["Merculesio, Macy",0],["Joy, Michelle",30],["Miuweme, Theresa",33],["Diaz, Jairi",1.5],["Gonzalez, Ariana",1.5],["De Marte, Jagaira",1.5],["Purizaca, Brandi",0],["Garcia, Vivian",1.5],["Nwankpa, Esther",0],["Marrufo, Andrea",19.5],["Ugwuanyi, Collins",0],["Rallos, Cylyn Jay D.",17.5],["Yaro, Mufida",73],["Rosas, Dulce",45],["S. Lariba, Juna Mae",53.7],["Palado, Kayla",58.5],["Arrellano, Floyd",54.67],["Victoria, John Sherwin",54.5],["Francess, Mary",16.98],["Asencio, Pamela",40.9],["Joel, Ajibola",6],["Okedi, Mary",3],["Espeche, Florencia",7],["Caraig, Kyle",10.5],["Talavera, Jenny",98.25],["Rei, Kerby",79.4],["Prasenan, Imee Diane",7],["Cebreros, Angel",7],["Bie, Athasia Angel",0],["Sanusi, Ahmad",4],["Bastias, Sofia",6]
].filter(d => d[0].trim() !== "");

const maxScore = Math.max(...raw.map(d=>d[1]));
let sortField = 'score', sortDir = -1;

function sortBy(f) {
  if (sortField === f) sortDir *= -1;
  else { sortField = f; sortDir = f === 'name' ? 1 : -1; }
  render();
}

function render() {
  const q = document.getElementById('search').value.toLowerCase();
  const filt = document.getElementById('filter').value;
  let data = raw.filter(d => {
    if (q && !d[0].toLowerCase().includes(q)) return false;
    if (filt === 'active' && d[1] === 0) return false;
    if (filt === 'zero' && d[1] > 0) return false;
    return true;
  });
  const sorted = [...raw].sort((a,b) => b[1]-a[1]);
  data.sort((a,b) => {
    if (sortField === 'name') return sortDir * a[0].localeCompare(b[0]);
    if (sortField === 'rank') return sortDir * (sorted.findIndex(x=>x[0]===a[0]) - sorted.findIndex(x=>x[0]===b[0]));
    return sortDir * (a[1]-b[1]);
  });

  const active = raw.filter(d=>d[1]>0).length;
  const avg = raw.reduce((s,d)=>s+d[1],0) / active;
  document.getElementById('summary').innerHTML = `
    <div class="card"><div class="card-label">Participants</div><div class="card-val">${raw.length}</div></div>
    <div class="card"><div class="card-label">With scores</div><div class="card-val">${active}</div></div>
    <div class="card"><div class="card-label">Top score</div><div class="card-val">${maxScore.toFixed(2)}</div></div>
    <div class="card"><div class="card-label">Avg (active)</div><div class="card-val">${avg.toFixed(1)}</div></div>
  `;

  const medals = ['🥇','🥈','🥉'];
  document.getElementById('tbody').innerHTML = data.map(d => {
    const globalRank = sorted.findIndex(x=>x[0]===d[0]) + 1;
    const pct = maxScore > 0 ? (d[1]/maxScore*100) : 0;
    const isTop = globalRank <= 3 && d[1] > 0;
    const medal = isTop ? `<span class="medal">${medals[globalRank-1]}</span>` : '';
    return `<tr>
      <td class="rank ${isTop ? 'rank-top' : ''}">${medal}${d[1]>0 ? globalRank : '—'}</td>
      <td>${d[0]}</td>
      <td class="score ${d[1]===0 ? 'zero' : ''}">${d[1].toFixed(2)}</td>
      <td class="bar-cell"><div class="bar"><div class="bar-fill" style="width:${pct.toFixed(1)}%"></div></div></td>
    </tr>`;
  }).join('');
}
render();
</script>
</body>
</html>
