<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Davenport Transport Solutions Shift Rota Planner</title>
<style>
  :root{ --bg:#f4f5f7; --ink:#1f2937; --grid:#d9dde3; --muted:#6b7280; --card:#ffffff; --accent:#1f6feb; --chip-text:#111827; --weekend:#fafafa; }
  *{box-sizing:border-box}
  body{margin:0; padding:18px; background:var(--bg); color:var(--ink); font:15px/1.4 ui-sans-serif,system-ui,-apple-system,Segoe UI,Roboto,Inter,Arial;}
  h1{margin:0 0 10px; font-size:20px}
  .wrap{max-width:1200px; margin:0 auto}
  .panel{background:var(--card); border:1px solid var(--grid); border-radius:10px; padding:14px; margin:0 0 14px;}
  label{display:block; font-size:12px; color:var(--muted); margin:6px 0 4px}
  input, select, textarea, button{ width:100%; padding:8px 10px; border:1px solid var(--grid); border-radius:8px; background:#fff; color:var(--ink); outline:none;}
  input[type="date"]{font-family:inherit;}
  input[readonly]{background:#f8fafc}
  textarea{min-height:74px; resize:vertical}
  .row{display:grid; gap:8px}
  @media(min-width:1120px){ .row{grid-template-columns: 560px 1fr} }
  .inline{display:flex; gap:8px}
  .inline > div{flex:1}
  .btn{cursor:pointer; width:auto; border:1px solid var(--accent); color:#fff; background:var(--accent); font-weight:600}
  .btn.secondary{background:#fff;color:var(--accent)}
  .muted{color:var(--muted)}
  .grid{overflow:auto; border:1px solid var(--grid); border-radius:10px; background:#fff}
  table{border-collapse:separate; border-spacing:0; width:100%}
  th, td{border-right:1px solid var(--grid); border-bottom:1px solid var(--grid); padding:6px 8px; text-align:left; white-space:nowrap; font-variant-numeric:tabular-nums;}
  thead th{position:sticky; top:0; background:#f7f8fa; z-index:1}
  .chip{ display:inline-block; border-radius:6px; padding:3px 8px; border:1px solid rgba(0,0,0,.07); font-weight:700; letter-spacing:.2px; box-shadow:inset 0 1px 0 rgba(255,255,255,.6); }
  .small{font-size:12px}
  .section-title{font-weight:700;margin:10px 0 6px}
  tr.weekend td{background:var(--weekend)}
  .chip:hover{opacity:0.9}
  .error{color:#ef4444; font-size:12px; margin:4px 0}
</style>
</head>
<body>
<div class="wrap">
  <h1>Davenport Transport Solutions Shift Rota Planner</h1>
 
  <div class="row">
    <div class="panel">
      <div class="inline">
        <div>
          <label>Start date mode</label>
          <select id="startMode">
            <option value="nextMonday" selected>Next Monday (default)</option>
            <option value="today">Today</option>
            <option value="firstNextMonth">First of next month</option>
            <option value="custom">Custom</option>
          </select>
        </div>
        <div>
          <label>Start date</label>
          <input type="date" id="startDateDisplay">
        </div>
        <div>
          <label>Weeks to generate</label>
          <input type="number" id="weeks" value="4" min="1" max="104">
        </div>
      </div>
 
      <div class="inline">
        <div>
          <label>Pattern preset</label>
          <select id="patternPreset">
            <option value="p42">4 on / 2 off (all drivers)</option>
            <option value="p44">4 on / 4 off (all drivers)</option>
            <option value="p42_r44">Blend: 4/2 primary + 4/4 cover</option>
            <option value="p44_r42">Blend: 4/4 primary + 4/2 cover</option>
          </select>
        </div>
        <div>
          <label>Runs per day (routes)</label>
          <input type="number" id="runs" value="3" min="1" max="10">
        </div>
      </div>
 
      <div class="inline">
        <div>
          <label>Primary / relief split (names)</label>
          <select id="nameSplit">
            <option value="auto" selected>Automatic (choose primaries to fit R)</option>
            <option value="firstHalf">First half primary, second half relief</option>
            <option value="firstOne">First name primary, others relief</option>
          </select>
        </div>
        <div>
          <label>Use all listed drivers?</label>
          <select id="useAll">
            <option value="yes" selected>Yes (assign everyone)</option>
            <option value="need">Only as needed to meet target</option>
          </select>
        </div>
      </div>
 
      <div class="inline">
        <div>
          <label>Primary route assignment</label>
          <select id="primaryRouteMode">
            <option value="rotate" selected>Rotate across routes</option>
            <option value="fixed">Fixed to a route</option>
          </select>
        </div>
        <div>
          <label>Primary days before route switch</label>
          <input type="number" id="primarySwitchDays" value="2" min="1" max="7">
        </div>
      </div>
 
      <div class="inline">
        <div>
          <label>Relief route assignment</label>
          <select id="reliefRouteMode">
            <option value="rotate" selected>Rotate across routes</option>
            <option value="fixed">Fixed to a route (spill to free)</option>
          </select>
        </div>
        <div></div> <!-- Empty div to maintain layout -->
      </div>
 
      <label>Driver names (one per line)</label>
      <textarea id="names">Mark
Yousef
Maruanne
Darek
Rob</textarea>
 
      <div class="inline" style="margin-top:10px">
        <button class="btn" id="build">Build rota</button>
        <button class="btn secondary" id="csv">Export CSV</button>
      </div>
      <p class="error" id="error"></p>
      <p class="small muted" id="note"></p>
      <p class="small" id="blendSummary"></p>
      <p class="small" id="routeSummary"></p>
    </div>
 
    <div class="panel">
      <div class="section-title">Rota (visual)</div>
      <div class="grid" id="rotaGrid"></div>
    </div>
  </div>
 
  <div class="row">
    <div class="panel">
      <div class="section-title">Drivers (assignments)</div>
      <div class="grid" id="driverGrid"></div>
    </div>
 
    <div class="panel">
      <div class="section-title">Summary (Run days per driver)</div>
      <div class="grid" id="summaryGrid"></div>
    </div>
  </div>
</div>
 
<script>
(function(){
  const $ = id => document.getElementById(id);
 
  // Utility functions
  function clamp(v, min, max) { return Math.max(min, Math.min(max, v)); }
  function int(v, d = 0) { return parseInt(v, 10) || d; }
  function cleanNames(text) { return text.split('\n').map(l => l.trim()).filter(Boolean); }
  function hash(s) { let h = 0; for (let i = 0; i < s.length; i++) h = h * 31 + s.charCodeAt(i); return Math.abs(h); }
  function addDays(date, days) { const result = new Date(date); result.setDate(result.getDate() + days); return result; }
  function stripTime(d) { return new Date(d.getFullYear(), d.getMonth(), d.getDate()); }
  function toISO(d) { return d.toISOString().slice(0, 10); }

  // Automatic start date
  function computeStartDate(mode) {
    const d = new Date();
    if (mode === 'today') return stripTime(d);
    if (mode === 'firstNextMonth') { const x = new Date(d); x.setMonth(x.getMonth() + 1, 1); return stripTime(x); }
    const x = new Date(d), wd = x.getDay(), diff = (1 - wd + 7) % 7; x.setDate(x.getDate() + diff); return stripTime(x); // next Monday
  }
  function updateStartDateDisplay() {
    try {
      const mode = $('startMode').value;
      if (mode !== 'custom') {
        $('startDateDisplay').value = toISO(computeStartDate(mode));
        $('startDateDisplay').setAttribute('readonly', '');
      } else {
        $('startDateDisplay').removeAttribute('readonly');
        if (!$('startDateDisplay').value) $('startDateDisplay').value = toISO(stripTime(new Date()));
      }
    } catch (e) {
      console.error('Error in updateStartDateDisplay:', e);
      $('error').textContent = 'Error setting start date. Check console for details.';
    }
  }

  // Color palette for drivers
  const colorPalette = [
    '#ffe082', '#a5d6a7', '#90caf9', '#f48fb1', '#ce93d8',
    '#ffccbc', '#b2ebf2', '#ffab91', '#c5e1a5', '#b39ddb',
    '#fff59d', '#81d4fa', '#f06292', '#e6ee9c', '#80deea'
  ];

  function getDriverClass(name) {
    const index = hash(name) % colorPalette.length;
    return `driver-${index}`;
  }

  // Dynamically generate CSS for driver colors
  function generateDriverStyles(names) {
    try {
      let style = '';
      names.forEach(name => {
        const index = hash(name) % colorPalette.length;
        style += `
          .chip.driver-${index} { 
            background: ${colorPalette[index]}; 
            color: #111827; 
          }
        `;
      });
      const existingStyles = document.head.querySelectorAll('style[data-driver-styles]');
      existingStyles.forEach(s => s.remove());
      const styleElement = document.createElement('style');
      styleElement.setAttribute('data-driver-styles', '');
      styleElement.textContent = style;
      document.head.appendChild(styleElement);
    } catch (e) {
      console.error('Error in generateDriverStyles:', e);
      $('error').textContent = 'Error generating driver styles. Check console for details.';
    }
  }

  // Event listeners
  try {
    $('startMode').addEventListener('change', updateStartDateDisplay);
    $('build').addEventListener('click', () => {
      console.log('Build rota button clicked');
      buildRota();
    });
    updateStartDateDisplay();
  } catch (e) {
    console.error('Error setting up event listeners:', e);
    $('error').textContent = 'Error initializing event listeners. Check console for details.';
  }

  let rota = []; // global for export

  function buildRota() {
    try {
      const error = $('error');
      error.textContent = '';
      console.log('Starting buildRota');
      const names = cleanNames($('names').value);
      if (names.length < 1) {
        error.textContent = 'Please provide at least one driver name.';
        console.error('No driver names provided');
        return;
      }
      console.log('Driver names:', names);
      generateDriverStyles(names);
      const runs = clamp(int($('runs').value, 3), 1, 10);
      const weeks = clamp(int($('weeks').value, 4), 1, 104);
      const days = weeks * 7;
      const startDate = stripTime(new Date($('startDateDisplay').value));
      if (isNaN(startDate.getTime())) {
        error.textContent = 'Invalid start date.';
        console.error('Invalid start date:', $('startDateDisplay').value);
        return;
      }
      console.log('Parameters - runs:', runs, 'weeks:', weeks, 'startDate:', startDate);
      const pattern = $('patternPreset').value;
      let primOn, primOff, relOn, relOff;
      let isBlend = pattern.includes('_');
      if (pattern === 'p42') {
        primOn = 4; primOff = 2;
      } else if (pattern === 'p44') {
        primOn = 4; primOff = 4;
      } else if (pattern === 'p42_r44') {
        primOn = 4; primOff = 2; relOn = 4; relOff = 4;
      } else if (pattern === 'p44_r42') {
        primOn = 4; primOff = 4; relOn = 4; relOff = 2;
      }
      console.log('Pattern:', pattern, 'isBlend:', isBlend);
      const nameSplit = $('nameSplit').value;
      const useAll = $('useAll').value === 'yes';
      const primaryRouteMode = $('primaryRouteMode').value;
      const reliefRouteMode = $('reliefRouteMode').value;
      const primarySwitchDays = clamp(int($('primarySwitchDays').value, 2), 1, 7);
      console.log('Settings - nameSplit:', nameSplit, 'useAll:', useAll, 'primaryRouteMode:', primaryRouteMode, 'reliefRouteMode:', reliefRouteMode, 'primarySwitchDays:', primarySwitchDays);
      let numPrim;
      if (!isBlend) {
        numPrim = names.length;
      } else {
        if (nameSplit === 'auto') {
          numPrim = runs;
        } else if (nameSplit === 'firstHalf') {
          numPrim = Math.floor(names.length / 2);
        } else if (nameSplit === 'firstOne') {
          numPrim = 1;
        }
      }
      if (numPrim > names.length) {
        error.textContent = 'Not enough drivers for the selected number of primary drivers.';
        console.error('Not enough drivers: numPrim=', numPrim, 'names.length=', names.length);
        return;
      }
      if (primaryRouteMode === 'fixed' && numPrim !== runs) {
        error.textContent = 'For fixed primary routes, number of primaries must equal number of routes.';
        console.error('Fixed primary routes require numPrim === runs:', numPrim, runs);
        return;
      }
      let primaries = names.slice(0, numPrim);
      let reliefs = names.slice(numPrim);
      if (!useAll && isBlend) {
        const offFraction = primOff / (primOn + primOff);
        const offSlotsPerDay = numPrim * offFraction;
        const relFraction = relOn / (relOn + relOff);
        const relNeeded = Math.ceil(offSlotsPerDay / relFraction + 0.5);
        reliefs = reliefs.slice(0, relNeeded);
      }
      const allDrivers = primaries.concat(reliefs);
      console.log('Primaries:', primaries, 'Reliefs:', reliefs);
      const drivers = [];
      primaries.forEach(name => drivers.push({name, isPrimary: true, on: primOn, off: primOff, cycle: primOn + primOff}));
      reliefs.forEach(name => drivers.push({name, isPrimary: false, on: relOn, off: relOff, cycle: relOn + relOff}));
      // Check feasibility
      const primWorkFraction = primOn / (primOn + primOff);
      const relWorkFraction = isBlend ? relOn / (relOn + relOff) : primWorkFraction;
      const avgWorkingDrivers = (primaries.length * primWorkFraction) + (reliefs.length * relWorkFraction);
      if (avgWorkingDrivers < runs) {
        const minDriversNeeded = Math.ceil(runs / Math.max(primWorkFraction, relWorkFraction));
        error.textContent = `Infeasible schedule: only ${avgWorkingDrivers.toFixed(2)} drivers available on average, need at least ${runs}. Try adding ${minDriversNeeded - names.length} more drivers or reducing routes.`;
        console.error('Infeasible schedule: avgWorkingDrivers=', avgWorkingDrivers, 'runs=', runs, 'minDriversNeeded=', minDriversNeeded);
        return;
      }
      console.log('Feasibility check passed: avgWorkingDrivers=', avgWorkingDrivers, 'runs=', runs);
      let chosenOffsets = [];
      let found = false;
      const maxTries = 200000;
      for (let t = 0; t < maxTries; t++) {
        const offsets = drivers.map(d => Math.floor(Math.random() * d.cycle));
        let good = true;
        for (let d = 0; d < days; d++) {
          const working = drivers.filter((dr, i) => ((d + offsets[i]) % dr.cycle) < dr.on);
          if (working.length < runs) {
            good = false;
            break;
          }
        }
        if (good) {
          chosenOffsets = offsets;
          found = true;
          break;
        }
      }
      if (!found) {
        const minDriversNeeded = Math.ceil(runs / Math.max(primWorkFraction, relWorkFraction));
        error.textContent = `Cannot find a valid schedule after ${maxTries} tries. Need at least ${minDriversNeeded} drivers for ${runs} routes with current shift patterns. Try adding ${minDriversNeeded - names.length} drivers, reducing routes, or adjusting shift patterns.`;
        console.error('Failed to find valid schedule after', maxTries, 'tries. minDriversNeeded=', minDriversNeeded);
        return;
      }
      console.log('Schedule found with offsets:', chosenOffsets);
      rota = [];
      const primaryAssignments = new Map(primaries.map(p => [p, []]));
      for (let d = 0; d < days; d++) {
        const date = addDays(startDate, d);
        const dayName = date.toLocaleString('en-us', { weekday: 'short' });
        const isWeekend = date.getDay() === 0 || date.getDay() === 6;
        const working = drivers.filter((dr, i) => ((d + chosenOffsets[i]) % dr.cycle) < dr.on).map(dr => dr.name);
        const primaryWorking = working.filter(name => primaries.includes(name));
        const reliefWorking = working.filter(name => reliefs.includes(name));
        let routeAssignments = new Array(runs).fill(null);
        let freeRoutes = Array.from({length: runs}, (_, i) => i);
        if (primaryRouteMode === 'fixed') {
          for (let p = 0; p < primaries.length; p++) {
            const name = primaries[p];
            if (working.includes(name)) {
              routeAssignments[p] = name;
              primaryAssignments.get(name).push(p);
              freeRoutes = freeRoutes.filter(r => r !== p);
            } else {
              primaryAssignments.get(name).push(null);
            }
          }
        } else {
          let sortedPrim = [...primaryWorking].sort((a, b) => primaries.indexOf(a) - primaries.indexOf(b));
          let availableRoutes = [...freeRoutes];
          for (let p = 0; p < sortedPrim.length && availableRoutes.length > 0; p++) {
            const name = sortedPrim[p];
            const prevAssignments = primaryAssignments.get(name);
            let i = prevAssignments.length - 1;
            let consecutive = 0;
            let lastRoute = null;
            if (i >= 0) {
              while (i >= 0 && prevAssignments[i] === null) i--;
              if (i >= 0) {
                lastRoute = prevAssignments[i];
                consecutive = 1;
                i--;
                while (i >= 0 && prevAssignments[i] !== null && prevAssignments[i] === lastRoute) {
                  consecutive++;
                  i--;
                }
              }
            }
            let route;
            if (consecutive > 0 && consecutive < primarySwitchDays && lastRoute !== null && availableRoutes.includes(lastRoute)) {
              route = lastRoute;
            } else {
              route = getNewRoute(availableRoutes, lastRoute, date, p, runs);
            }
            routeAssignments[route] = name;
            primaryAssignments.get(name).push(route);
            availableRoutes = availableRoutes.filter(r => r !== route);
          }
          freeRoutes = availableRoutes;
          primaries.forEach(name => {
            if (!primaryWorking.includes(name)) {
              primaryAssignments.get(name).push(null);
            }
          });
        }
        if (freeRoutes.length > 0) {
          let sortedRel = [...reliefWorking].sort((a, b) => reliefs.indexOf(a) - reliefs.indexOf(b));
          let availableRoutes = [...freeRoutes];
          for (let p = 0; p < sortedRel.length && availableRoutes.length > 0; p++) {
            let route;
            if (reliefRouteMode === 'fixed') {
              const preferred = p % runs;
              route = availableRoutes.includes(preferred) ? preferred : availableRoutes[0];
            } else {
              const startR = hash(toISO(date)) % runs;
              route = availableRoutes[(startR + p) % availableRoutes.length];
            }
            routeAssignments[route] = sortedRel[p];
            availableRoutes = availableRoutes.filter(r => r !== route);
          }
          freeRoutes = availableRoutes;
        }
        const spares = working.filter(name => !routeAssignments.includes(name)).join(', ');
        rota.push({dateStr: toISO(date), dayName, isWeekend, assigned: routeAssignments, spares});
      }
      let hasSpare = rota.some(item => item.spares);
      let html = '<table><thead><tr><th>Date</th><th>Day</th>';
      for (let r = 1; r <= runs; r++) html += '<th>Run ' + r + '</th>';
      if (hasSpare) html += '<th>Spare</th>';
      html += '</tr></thead><tbody>';
      rota.forEach(item => {
        html += '<tr' + (item.isWeekend ? ' class="weekend"' : '') + '><td>' + item.dateStr + '</td><td>' + item.dayName + '</td>';
        item.assigned.forEach(a => html += '<td>' + (a ? '<span class="chip ' + getDriverClass(a) + '">' + a + '</span>' : '') + '</td>');
        if (hasSpare) html += '<td>' + item.spares.split(', ').map(s => s ? `<span class="chip ${getDriverClass(s)}">${s}</span>` : '').join(', ') + '</td>';
        html += '</tr>';
      });
      html += '</tbody></table>';
      $('rotaGrid').innerHTML = html;
      let runCounts = new Map(allDrivers.map(n => [n, 0]));
      let spareCounts = new Map(allDrivers.map(n => [n, 0]));
      rota.forEach(item => {
        item.assigned.filter(a => a).forEach(a => runCounts.set(a, runCounts.get(a) + 1));
        item.spares.split(', ').filter(s => s).forEach(s => spareCounts.set(s, spareCounts.get(s) + 1));
      });
      let sumHtml = '<table><thead><tr><th>Driver</th>';
      if (hasSpare) sumHtml += '<th>Run days</th><th>Spare days</th><th>Total On-days</th>';
      else sumHtml += '<th>Runs Covered (days)</th>';
      sumHtml += '</tr></thead><tbody>';
      allDrivers.forEach(n => {
        const runsC = runCounts.get(n);
        const sparesC = spareCounts.get(n);
        sumHtml += '<tr><td><span class="chip ' + getDriverClass(n) + '">' + n + '</span></td>';
        if (hasSpare) sumHtml += '<td>' + runsC + '</td><td>' + sparesC + '</td><td>' + (runsC + sparesC) + '</td>';
        else sumHtml += '<td>' + runsC + '</td>';
        sumHtml += '</tr>';
      });
      sumHtml += '</tbody></table>';
      $('summaryGrid').innerHTML = sumHtml;
      let driverHtml = '<table><thead><tr><th>Driver</th>';
      for (let d = 0; d < days; d++) {
        const date = addDays(startDate, d);
        const dayShort = date.toLocaleString('en-us', { weekday: 'short' });
        const dateShort = date.toLocaleString('en-us', { day: '2-digit', month: '2-digit' });
        driverHtml += '<th>' + dayShort + '<br>' + dateShort + '</th>';
      }
      driverHtml += '</tr></thead><tbody>';
      allDrivers.forEach(n => {
        driverHtml += '<tr><td><span class="chip ' + getDriverClass(n) + '">' + n + '</span></td>';
        rota.forEach(item => {
          const route = item.assigned.findIndex(a => a === n) + 1;
          if (route > 0) driverHtml += '<td>Run ' + route + '</td>';
          else if (item.spares.split(', ').includes(n)) driverHtml += '<td>Spare</td>';
          else driverHtml += '<td>Off</td>';
        });
        driverHtml += '</tr>';
      });
      driverHtml += '</tbody></table>';
      $('driverGrid').innerHTML = driverHtml;
      if (isBlend) {
        $('blendSummary').innerHTML = 'Primary drivers (' + primOn + ' on / ' + primOff + ' off): ' + primaries.map(n => '<span class="chip ' + getDriverClass(n) + '">' + n + '</span>').join(', ') + '<br>Relief drivers (' + relOn + ' on / ' + relOff + ' off): ' + reliefs.map(n => '<span class="chip ' + getDriverClass(n) + '">' + n + '</span>').join(', ');
      } else {
        $('blendSummary').innerHTML = 'All drivers (' + primOn + ' on / ' + primOff + ' off): ' + allDrivers.map(n => '<span class="chip ' + getDriverClass(n) + '">' + n + '</span>').join(', ');
      }
      $('routeSummary').innerHTML = 'Primary route assignment: ' + (primaryRouteMode === 'rotate' ? `Rotating (every ${primarySwitchDays} days)` : 'Fixed') + '<br>Relief route assignment: ' + (reliefRouteMode === 'rotate' ? 'Rotating (as needed)' : 'Fixed (spill to free)');
      $('note').innerHTML = 'Generated for ' + weeks + ' weeks starting ' + startDate.toLocaleDateString();
      console.log('Rota generation completed');
    } catch (e) {
      console.error('Error in buildRota:', e);
      $('error').textContent = 'Error generating rota. Check console for details.';
    }
  }

  function getNewRoute(availableRoutes, avoid, date, p, runs) {
    try {
      let newAvail = avoid !== null ? availableRoutes.filter(r => r !== avoid) : availableRoutes;
      if (newAvail.length === 0) newAvail = availableRoutes;
      const startR = hash(toISO(date)) % newAvail.length;
      return newAvail[(startR + p) % newAvail.length];
    } catch (e) {
      console.error('Error in getNewRoute:', e);
      throw e;
    }
  }

  $('csv').addEventListener('click', () => {
    try {
      if (rota.length === 0) {
        $('error').textContent = 'Build the rota first.';
        console.error('No rota data for CSV export');
        return;
      }
      const runs = int($('runs').value, 3);
      const hasSpare = rota.some(item => item.spares);
      let csv = 'Date,Day';
      for (let r = 1; r <= runs; r++) csv += ',Run ' + r;
      if (hasSpare) csv += ',Spare';
      csv += '\n';
      rota.forEach(item => {
        csv += item.dateStr + ',' + item.dayName;
        item.assigned.forEach(a => csv += ',' + (a || ''));
        if (hasSpare) csv += ',' + item.spares;
        csv += '\n';
      });
      csv += '\nSummary\nDriver';
      if (hasSpare) csv += ',Run days,Spare days,Total On-days\n';
      else csv += ',Runs Covered (days)\n';
      const allDrivers = [...new Set([...rota[0].assigned.filter(a => a), ...rota[0].spares.split(', ').filter(s => s)])];
      allDrivers.forEach(n => {
        let runsC = 0, sparesC = 0;
        rota.forEach(item => {
          if (item.assigned.includes(n)) runsC++;
          if (item.spares.split(', ').includes(n)) sparesC++;
        });
        csv += n;
        if (hasSpare) csv += ',' + runsC + ',' + sparesC + ',' + (runsC + sparesC);
        else csv += ',' + runsC;
        csv += '\n';
      });
      const blob = new Blob([csv], {type: 'text/csv;charset=utf-8;'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = 'rota_' + toISO(new Date()) + '.csv';
      document.body.appendChild(a);
      a.click();
      document.body.removeChild(a);
      console.log('CSV exported successfully');
    } catch (e) {
      console.error('Error in CSV export:', e);
      $('error').textContent = 'Error exporting CSV. Check console for details.';
    }
  });
})();
</script>
</body>
</html>
