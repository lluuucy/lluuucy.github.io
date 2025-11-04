<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Take the Green Purity Test! — Amherst College</title>
  <meta name="description" content="The Green Purity Test serves to track how sustainable your lifestyle is. Click on every item you do." />
  <style>
    :root{--bg:#f6f8f9;--card:#ffffff;--accent:#2b8a3e;--muted:#6b7280;--glass:rgba(255,255,255,0.6)}
    *{box-sizing:border-box}
    body{font-family:Inter,ui-sans-serif,system-ui,-apple-system,'Segoe UI',Roboto,'Helvetica Neue',Arial;line-height:1.4;margin:0;background:linear-gradient(180deg,#eaf3ea 0%,var(--bg) 100%);color:#0b1220;padding:24px}
    .container{max-width:900px;margin:0 auto}
    header{display:flex;align-items:center;gap:16px;margin-bottom:18px}
    h1{margin:0;font-size:2rem;color:var(--accent)}
    p.lead{margin:4px 0 0;color:var(--muted);font-size:1rem}
    .card{background:var(--card);border-radius:12px;padding:20px;box-shadow:0 6px 18px rgba(16,24,40,0.06);margin-bottom:16px}

    form{display:grid;gap:14px}
    .questions{display:grid;gap:8px}
    .q{display:flex;align-items:center;gap:12px;padding:10px;border-radius:8px;border:1px solid #eef2f6}
    .q input[type=checkbox]{width:18px;height:18px}
    .q label{flex:1}

    .actions{display:flex;gap:8px;flex-wrap:wrap}
    button{background:var(--accent);color:white;border:0;padding:10px 14px;border-radius:8px;font-weight:600;cursor:pointer}
    button.secondary{background:transparent;color:var(--accent);border:1px solid rgba(43,138,62,0.18)}
    .result{display:flex;align-items:center;justify-content:space-between;gap:12px}
    .score{font-size:2.2rem;font-weight:700;color:var(--accent)}

    .progress{height:12px;background:#e6f1e6;border-radius:10px;overflow:hidden}
    .progress > i{display:block;height:100%;background:linear-gradient(90deg,#2b8a3e,#6cc06b)}

    .raffle{background:#f6fff6;border:2px solid #b5e7b5;border-radius:14px;padding:20px;display:grid;gap:12px}
    .raffle strong{font-size:1.2rem;color:#14532d}
    .inline{display:flex;gap:8px}
    input[type=text],input[type=email]{padding:10px;border-radius:8px;border:1px solid #dbe7dc;flex:1}
    small.privacy{color:var(--muted)}
    footer{margin-top:8px;color:var(--muted);font-size:0.9rem;text-align:center}

    @media (max-width:520px){header{flex-direction:column;align-items:flex-start} .result{flex-direction:column;align-items:flex-start}}
  </style>
</head>
<body>
  <div class="container">
    <header>
      <div style="width:56px;height:56px;border-radius:10px;background:var(--glass);display:flex;align-items:center;justify-content:center;font-weight:700;color:var(--accent);">GP</div>
      <div>
        <h1>Take the Green Purity Test!</h1>
        <p class="lead">The Green Purity Test serves to track how sustainable your lifestyle is. Click on every item you do.</p>
      </div>
    </header>

    <main>
      <section class="card">
        <form id="testForm" onsubmit="return false;">
          <div class="questions" id="questionsList"></div>

          <div class="card" style="background:#f8fff8;border:1px solid #e6f7e6">
            <div class="result">
              <div>
                <div style="color:var(--muted);font-size:0.9rem">Your Green Score</div>
                <div style="display:flex;align-items:center;gap:12px">
                  <div class="score" id="score">—</div>
                  <div style="min-width:160px">
                    <div class="progress"><i id="progressBar" style="width:0%"></i></div>
                    <div style="font-size:0.85rem;color:var(--muted);margin-top:6px" id="interpret">Answer the questions to reveal your score.</div>
                  </div>
                </div>
              </div>

              <div style="text-align:right">
                <div style="color:var(--muted);font-size:0.85rem">Completed: <span id="completedCount">0</span>/<span id="totalCount">0</span></div>
                <div style="margin-top:8px" class="actions">
                  <button type="button" onclick="calculateScore()">Calculate</button>
                  <button type="button" class="secondary" onclick="resetForm()">Reset</button>
                </div>
              </div>
            </div>
          </div>

          <div class="raffle">
            <strong>🎁 Enter to win a $100 gift card to an Amherst restaurant of your choice!</strong>
            <label style="font-size:1rem;color:#166534"><input id="enterRaffle" type="checkbox" style="margin-right:8px"> Yes, I’d like to enter the raffle</label>

            <div id="raffleFields" style="display:none;margin-top:8px">
              <div class="inline">
                <input id="firstName" type="text" placeholder="First name">
                <input id="lastName" type="text" placeholder="Last name">
              </div>
              <input id="amherstEmail" type="email" placeholder="Amherst email (required to enter raffle)">
              <small class="privacy">Your information will only be used for raffle purposes and not shared externally.</small>
            </div>

            <div style="display:flex;gap:8px;margin-top:10px">
              <button id="submitBtn" type="button" onclick="finalize()">Submit (Calculate &amp; Send)</button>
              <button type="button" class="secondary" onclick="downloadResults()">Download my results (CSV)</button>
              <button type="button" class="secondary" onclick="copyResult()">Copy result</button>
            </div>
          </div>

          <div id="outcome" style="display:none;margin-top:12px"></div>
        </form>
      </section>

      <footer>Created for Amherst College • Built for campus sustainability awareness.</footer>
    </main>
  </div>

  <script>
    const QUESTIONS = [
      "Turn off the lights when leaving the room",
      "Use natural light from the window during the day",
      "Always unplug appliances/electronics when not in use",
      "Wash on cold?",
      "Wait to do laundry until the basket is full?",
      "Keep your windows closed when the heat is on?",
      "Take sub 10 minute showers?",
      "Take sub 5 minute showers?",
      "Use a reusable water bottle?",
      "Use reusable shopping bags?",
      "Mostly get your clothes and items second-hand?",
      "Attend the Free Clothing Events?",
      "Attend the Free Book Fair?",
      "Empty your room trash no more than once a week?",
      "Put red solo cups in the trash?",
      "Compost your grab n go plastics?",
      "Put batteries, old electronics, and vapes in the universal waste disposals (located in the laundry rooms)?",
      "Put unwanted clothes in the clothing donation bins (located in the laundry rooms)?",
      "Donate your old books in common room free little libraries?",
      "Limit online shopping?",
      "Try the vegetarian options?",
      "Eat mainly the vegetarian meals?",
      "Eat entirely the vegetarian meals?",
      "Eat an entirely vegan diet?",
      "Minimize having non-compostable packaged foods (ex. Grab n go chips, late night ice cream, etc.)?",
      "Leave Val with a mostly clean plate?",
      "Take the PVTA?",
      "Ride a bike?",
      "Use the Tread Shed?",
      "Avoid driving as much as possible?",
      "In a club you are passionate about?",
      "Vote?",
      "Tell other people to take the Eco Purity Test?",
      "Win the kill-A-watt challenge?",
      "Know the name of your first-year EcoRep?"
    ];

    const questionsDiv = document.getElementById('questionsList');
    const totalCountSpan = document.getElementById('totalCount');
    const completedCountSpan = document.getElementById('completedCount');

    function renderQuestions(){
      QUESTIONS.forEach((q, idx) => {
        const id = 'q' + idx;
        const wrap = document.createElement('div'); wrap.className='q';
        const checkbox = document.createElement('input'); checkbox.type='checkbox'; checkbox.id=id; checkbox.name='q'; checkbox.dataset.index=idx;
        const label = document.createElement('label'); label.htmlFor=id; label.textContent=q;
        checkbox.addEventListener('change', updateCounts);
        wrap.appendChild(checkbox); wrap.appendChild(label); questionsDiv.appendChild(wrap);
      });
      totalCountSpan.textContent = QUESTIONS.length;
    }

    function updateCounts(){
      const checked = document.querySelectorAll('input[name=q]:checked').length;
      completedCountSpan.textContent = checked;
    }

    renderQuestions();

    const enterRaffle = document.getElementById('enterRaffle');
    const raffleFields = document.getElementById('raffleFields');
    enterRaffle.addEventListener('change', (e) => {
      raffleFields.style.display = e.target.checked ? 'block' : 'none';
    });

    function calculateScore(){
      const checked = document.querySelectorAll('input[name=q]:checked').length;
      const total = QUESTIONS.length;
      const percent = Math.round((checked / total) * 100);
      document.getElementById('score').textContent = percent + '%';
      document.getElementById('progressBar').style.width = percent + '%';
      const interp = document.getElementById('interpret');
      let msg = '';
      if(percent >= 85) msg = 'Green champion — keep inspiring others!';
      else if(percent >= 65) msg = 'Very green — great habits.';
      else if(percent >= 40) msg = 'On the right track — room to improve.';
      else msg = 'Opportunity to adopt more sustainable habits.';
      interp.textContent = msg;
      document.getElementById('outcome').style.display='block';
      document.getElementById('outcome').innerHTML = `<div class="card"><strong>Summary</strong><p>You answered <strong>${checked}</strong> of <strong>${total}</strong> actions positively. Your Green Score is <strong>${percent}%</strong>. ${msg}</p></div>`;
      updateCounts();
      return {checked, total, percent, msg};
    }

    async function finalize(){
      const result = calculateScore();
      if(enterRaffle.checked){
        const f = document.getElementById('firstName').value.trim();
        const l = document.getElementById('lastName').value.trim();
        const email = document.getElementById('amherstEmail').value.trim();
        if(!f || !l || !email){alert('Please fill in all raffle fields.');return;}
        if(!/^[^@\s]+@amherst\.edu$/i.test(email)){alert('Use a valid @amherst.edu email.');return;}

        const data = {firstName:f,lastName:l,email:email,scorePercent:result.percent,timestamp:new Date().toISOString()};
        const SCRIPT_URL = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';

        try {
          const res = await fetch(SCRIPT_URL, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(data)
          });
          if(res.ok){
            alert('🎉 Submitted! You are entered in the raffle for a $100 Amherst restaurant gift card!');
          } else {
            alert('Submission failed. Please try again later.');
          }
        } catch(err){
          alert('Error sending data: ' + err);
        }
      } else {
        alert('Result calculated locally.');
      }
    }

    function resetForm(){
      document.querySelectorAll('input[name=q]').forEach(i => i.checked=false);
      document.getElementById('score').textContent = '—';
      document.getElementById('progressBar').style.width = '0%';
      document.getElementById('interpret').textContent = 'Answer the questions to reveal your score.';
      document.getElementById('outcome').style.display='none';
      updateCounts();
    }

    function downloadResults(){
      const checkedBoxes = Array.from(document.querySelectorAll('input[name=q]'));
      const answers = checkedBoxes.map((cb, idx) => ({question: QUESTIONS[idx], yes: cb.checked ? 'yes' : 'no'}));
      const result = calculateScore();
      const payload = {
        timestamp: new Date().toISOString(),
        scorePercent: result.percent,
        positiveCount: result.checked,
        totalQuestions: result.total,
        answers
      };
      const csv = toCSV(payloadToCSVRows(payload));
      downloadFile(csv, 'green-purity-result.csv', 'text/csv');
    }

    function copyResult(){
      const result = calculateScore();
      const text = `Green Purity Test — Score: ${result.percent}% (${result.checked}/${result.total})\\n${result.msg}`;
      navigator.clipboard.writeText(text).then(()=>alert('Result copied to clipboard.'));
    }

    function toCSV(objects){
      const keys = Object.keys(objects[0]);
      const lines = [keys.join(',')];
      for(const obj of objects){
        const row = keys.map(k => '"'+String(obj[k] ?? '').replace(/"/g,'""')+'"');
        lines.push(row.join(','));
      }
      return lines.join('\n');
    }

    function payloadToCSVRows(payload){
      const rows = [];
      rows.push({field:'timestamp',value:payload.timestamp});
      rows.push({field:'scorePercent',value:payload.scorePercent});
      rows.push({field:'positiveCount',value:payload.positiveCount});
      rows.push({field:'totalQuestions',value:payload.totalQuestions});
      payload.answers.forEach((a,i)=>rows.push({field:`q${i+1} - ${a.question}`,value:a.yes}));
      return rows;
    }

    function downloadFile(content, filename, mime){
      const blob = new Blob([content], {type: mime});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a'); a.href = url; a.download = filename; document.body.appendChild(a); a.click(); a.remove(); setTimeout(()=>URL.revokeObjectURL(url), 3000);
    }

    updateCounts();
  </script>
</body>
</html>
