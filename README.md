<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>NORTHEAST PRO SERIES – OVERALL RANKING</title>

<style>
body {
    margin: 0;
    background: #000;
    color: #fff;
    font-family: Segoe UI, sans-serif;
}

h1 {
    text-align: center;
    font-size: 60px;
    color: #f5d47a;
    margin: 30px 0 20px;
    text-shadow: 0 0 20px rgba(255,215,120,0.6);
}

.wrapper {
    width: 1800px;
    margin: auto;
    display: flex;
    gap: 40px;
    opacity: 0.9;
}

table {
    width: 100%;
    border-collapse: collapse;
    font-size: 20px;
}

th {
    background: #c89b3c;
    color: #111;
    padding: 10px;
}

td {
    padding: 8px;
    border-bottom: 1px solid rgba(255,255,255,0.1);
}

tr:nth-child(even) {
    background: rgba(255,255,255,0.05);
}

.rank { width: 60px; text-align: center; font-weight: 700; }
.fin, .pos, .cd, .tot { width: 100px; text-align: center; }
.team {
    padding-left: 10px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

/* Animations */
@keyframes slideLeft {
  from { transform: translateX(-60px); opacity: 0; }
  to   { transform: translateX(0); opacity: 1; }
}

@keyframes slideRight {
  from { transform: translateX(60px); opacity: 0; }
  to   { transform: translateX(0); opacity: 1; }
}

.left-anim { animation: slideLeft 0.6s ease forwards; }
.right-anim { animation: slideRight 0.6s ease forwards; }
</style>
</head>

<body>

<h1>OVERALL RANKING</h1>

<div class="wrapper">

  <table>
    <thead>
      <tr>
        <th>#</th><th>TEAM</th><th>FIN</th><th>POS</th><th>CD</th><th>TOTAL</th>
      </tr>
    </thead>
    <tbody id="left"></tbody>
  </table>

  <table>
    <thead>
      <tr>
        <th>#</th><th>TEAM</th><th>FIN</th><th>POS</th><th>CD</th><th>TOTAL</th>
      </tr>
    </thead>
    <tbody id="right"></tbody>
  </table>

</div>

<script>
// 🔴 MAKE SURE YOUR SHEET IS PUBLIC
const SHEET_JSON =
  "https://docs.google.com/spreadsheets/d/12TfyyP1dxImsqo0dYXVPMIHlao9VmExUL7WHonLQ1Os/gviz/tq?tqx=out:json&gid=181535";

let firstLoad = true;

async function loadData() {
  try {
    const res = await fetch(SHEET_JSON);
    const text = await res.text();
    const json = JSON.parse(text.substring(47, text.length - 2));

    const rows = json.table.rows;

    const data = rows.map(r => {
      const c = r.c || [];
      return {
        team: c[0]?.v || "",
        cd:   c[2]?.v || 0,
        pos:  c[3]?.v || 0,
        fin:  c[4]?.v || 0,
        tot:  c[5]?.v || 0
      };
    }).filter(r => r.team);

    data.sort((a, b) => b.tot - a.tot);

    render("left", data.slice(0, 10), 1, "left");
    render("right", data.slice(10, 20), 11, "right");

    firstLoad = false;

  } catch (err) {
    console.error("Load error:", err);
  }
}

function render(id, list, startRank, side) {
  const el = document.getElementById(id);
  let html = "";

  list.forEach((r, i) => {
    const anim = firstLoad ? (side === "left" ? "left-anim" : "right-anim") : "";
    const delay = firstLoad ? `style="animation-delay:${i * 0.08}s"` : "";

    html += `
      <tr class="${anim}" ${delay}>
        <td class="rank">${startRank + i}</td>
        <td class="team">${r.team}</td>
        <td class="fin">${r.fin}</td>
        <td class="pos">${r.pos}</td>
        <td class="cd">${r.cd}</td>
        <td class="tot">${r.tot}</td>
      </tr>
    `;
  });

  el.innerHTML = html;
}

loadData();
setInterval(loadData, 3000); // refresh every 3 sec
</script>

</body>
</html>
