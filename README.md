<!DOCTYPE html><html lang="ar">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>تطبيق الأعداد الأولية التفاعلي</title>
  <style>
    body { font-family: Arial, sans-serif; direction: rtl; background: #f8f8f8; margin: 0; padding: 20px; }
    h1 { text-align: center; }
    section { background: #fff; padding: 15px; margin: 15px 0; border-radius: 10px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
    .grid { display: grid; grid-template-columns: repeat(auto-fill, 50px); gap: 5px; }
    .cell { text-align: center; padding: 5px; border-radius: 6px; border: 1px solid #ccc; font-size: 14px; }
    .prime { background: #c6f6d5; border-color: #48bb78; }
    .composite { background: #fed7d7; border-color: #f56565; }
    .untouched { background: #fff; }
    button { margin: 2px; padding: 5px 10px; border: none; border-radius: 6px; cursor: pointer; }
    button:disabled { opacity: 0.5; cursor: not-allowed; }
    .btn-primary { background: #3182ce; color: #fff; }
    .btn-success { background: #38a169; color: #fff; }
    .btn-danger { background: #e53e3e; color: #fff; }
  </style>
</head>
<body>
  <h1>تطبيق تفاعلي: الأعداد الأولية</h1>  <section>
    <h2>المصفاة (Sieve of Eratosthenes)</h2>
    <label>أعلى قيمة (N): <input type="number" id="limit" value="50"></label>
    <button class="btn-primary" onclick="resetGrid()">إعادة ضبط</button>
    <button class="btn-success" onclick="runSieve()">تشغيل</button>
    <button class="btn-danger" onclick="stopSieve()">إيقاف</button>
    <div id="grid" class="grid"></div>
  </section>  <section>
    <h2>اختبار رقم واحد</h2>
    <input type="number" id="testNum" placeholder="أدخل رقمًا">
    <button class="btn-primary" onclick="testNumber()">اختبر</button>
    <div id="testResult"></div>
  </section>  <section>
    <h2>اختبار قصير (Quiz)</h2>
    <button class="btn-success" onclick="startQuiz()">ابدأ اختبار جديد</button>
    <div id="quiz"></div>
  </section><script>
  let numbers = [];
  let limit = 50;
  let running = false;
  let timer = null;

  function resetGrid() {
    limit = parseInt(document.getElementById('limit').value);
    numbers = [];
    for (let i = 2; i <= limit; i++) {
      numbers.push({n: i, state: 'untouched'});
    }
    renderGrid();
    stopSieve();
  }

  function renderGrid() {
    const grid = document.getElementById('grid');
    grid.innerHTML = '';
    numbers.forEach(num => {
      const div = document.createElement('div');
      div.className = 'cell ' + num.state;
      div.textContent = num.n;
      grid.appendChild(div);
    });
  }

  function runSieve() {
    running = true;
    let p = 2;

    function step() {
      if (!running) return;
      let idx = numbers.findIndex(x => x.n === p);
      if (idx === -1) { stopSieve(); return; }
      if (numbers[idx].state === 'untouched') {
        numbers[idx].state = 'prime';
        for (let m = p*2; m <= limit; m += p) {
          let j = numbers.findIndex(x => x.n === m);
          if (j !== -1 && numbers[j].state === 'untouched') numbers[j].state = 'composite';
        }
      }
      renderGrid();
      p++;
      if (p <= limit) timer = setTimeout(step, 500);
      else stopSieve();
    }

    step();
  }

  function stopSieve() {
    running = false;
    if (timer) clearTimeout(timer);
  }

  function isPrime(n) {
    if (n < 2) return false;
    for (let i = 2; i <= Math.sqrt(n); i++) if (n % i === 0) return false;
    return true;
  }

  function testNumber() {
    const n = parseInt(document.getElementById('testNum').value);
    const result = document.getElementById('testResult');
    if (isNaN(n)) { result.textContent = 'الرجاء إدخال عدد صحيح.'; return; }
    if (isPrime(n)) result.textContent = `${n} عدد أولي.`;
    else result.textContent = `${n} ليس عددًا أوليًا.`;
  }

  function startQuiz() {
    const quizDiv = document.getElementById('quiz');
    quizDiv.innerHTML = '';
    for (let i = 0; i < 5; i++) {
      const n = Math.floor(Math.random() * 100) + 2;
      const q = document.createElement('div');
      q.innerHTML = `العدد: <b>${n}</b><br>`;
      const btn1 = document.createElement('button');
      btn1.textContent = 'أولي';
      btn1.onclick = () => alert(isPrime(n) ? 'صحيح ✅' : 'خطأ ❌');
      const btn2 = document.createElement('button');
      btn2.textContent = 'غير أولي';
      btn2.onclick = () => alert(!isPrime(n) ? 'صحيح ✅' : 'خطأ ❌');
      q.appendChild(btn1);
      q.appendChild(btn2);
      quizDiv.appendChild(q);
    }
  }

  resetGrid();
</script></body>
</html>
