<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>AutoTrade — Учёт авто и товаров</title>
<style>
  :root {
    --bg: #f1f5f9;
    --card: #ffffff;
    --text: #0f172a;
    --muted: #64748b;
    --primary: #1e3a8a;
    --primary-light: #eff6ff;
    --accent: #2563eb;
    --success: #059669;
    --success-bg: #ecfdf5;
    --danger: #dc2626;
    --danger-bg: #fef2f2;
    --warning: #d97706;
    --warning-bg: #fffbeb;
    --border: #e2e8f0;
    --radius: 18px;
  }
  * { margin:0; padding:0; box-sizing:border-box; -webkit-tap-highlight-color: transparent; }
  body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; background: var(--bg); color: var(--text); padding-bottom: 90px; }
  
  header { background: #0f172a; color: #fff; padding: 18px 20px; display: flex; justify-content: space-between; align-items: center; position: sticky; top:0; z-index: 40; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
  .logo { font-size: 20px; font-weight: 800; letter-spacing: -0.5px; }
  .logo span { color: #38bdf8; }
  .rate-badge { background: #1e293b; padding: 6px 12px; border-radius: 20px; font-size: 13px; font-weight: 600; color: #94a3b8; border: 1px solid #334155; }
  .rate-badge b { color: #38bdf8; }

  .content { padding: 16px; max-width: 600px; margin: 0 auto; }
  .screen { display: none; }
  .screen.active { display: block; }

  .card { background: var(--card); border-radius: var(--radius); padding: 18px; margin-bottom: 14px; border: 1px solid var(--border); box-shadow: 0 2px 6px rgba(0,0,0,0.02); }
  .card-title { font-size: 14px; font-weight: 700; color: var(--muted); text-transform: uppercase; margin-bottom: 8px; letter-spacing: 0.5px; }
  
  .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
  .stat-box { background: var(--bg); padding: 12px; border-radius: 12px; }
  .stat-val { font-size: 19px; font-weight: 800; color: var(--text); margin-top: 2px; }
  .stat-sub { font-size: 12px; color: var(--muted); margin-top: 2px; }
  
  .alert-card { border-left: 5px solid var(--danger); background: var(--danger-bg); }
  .alert-card .stat-val { color: var(--danger); }
  
  .pill { display: inline-block; padding: 4px 10px; border-radius: 20px; font-size: 11px; font-weight: 700; text-transform: uppercase; }
  .pill-road { background: var(--warning-bg); color: var(--warning); border: 1px solid #fde68a; }
  .pill-stock { background: var(--success-bg); color: var(--success); border: 1px solid #a7f3d0; }
  .pill-sold { background: var(--primary-light); color: var(--accent); border: 1px solid #bfdbfe; }
  
  .btn { display: inline-flex; align-items: center; justify-content: center; gap: 8px; width: 100%; padding: 13px; border-radius: 14px; font-size: 15px; font-weight: 700; cursor: pointer; border: none; text-decoration: none; }
  .btn-primary { background: var(--primary); color: #fff; }
  .btn-accent { background: var(--accent); color: #fff; }
  .btn-wa { background: #22c55e; color: #fff; }
  .btn-outline { background: transparent; border: 1.5px solid var(--border); color: var(--text); }
  .btn-danger { background: var(--danger-bg); color: var(--danger); }
  .btn-sm { padding: 8px 12px; font-size: 13px; border-radius: 10px; width: auto; }

  .filter-row { display: flex; gap: 8px; overflow-x: auto; padding-bottom: 8px; margin-bottom: 12px; }
  .filter-chip { padding: 7px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; background: var(--card); border: 1px solid var(--border); color: var(--muted); white-space: nowrap; cursor: pointer; }
  .filter-chip.active { background: #0f172a; color: #fff; border-color: #0f172a; }

  .item-card { background: var(--card); border-radius: var(--radius); padding: 16px; margin-bottom: 12px; border: 1px solid var(--border); }
  .item-head { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 8px; }
  .item-title { font-size: 17px; font-weight: 800; }
  .item-sub { font-size: 13px; color: var(--muted); margin-top: 2px; }
  .item-details { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin: 12px 0; font-size: 13px; background: var(--bg); padding: 10px; border-radius: 10px; }
  .item-actions { display: flex; gap: 8px; margin-top: 10px; }

  /* Bottom Nav */
  nav { position: fixed; bottom: 0; left: 0; right: 0; background: var(--card); border-top: 1px solid var(--border); display: flex; justify-content: space-around; padding: 10px 6px; z-index: 50; }
  .nav-btn { display: flex; flex-direction: column; align-items: center; gap: 4px; font-size: 11px; font-weight: 600; color: var(--muted); cursor: pointer; border: none; background: none; }
  .nav-btn.active { color: var(--accent); }
  .nav-btn span.icon { font-size: 20px; }

  /* Modal */
  .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.6); z-index: 100; align-items: flex-end; justify-content: center; backdrop-filter: blur(3px); }
  .modal.active { display: flex; }
  .modal-box { background: var(--card); width: 100%; max-width: 550px; border-radius: 24px 24px 0 0; padding: 24px 20px; max-height: 90vh; overflow-y: auto; }
  .modal-head { display: flex; justify-content: space-between; align-items: center; margin-bottom: 18px; }
  .modal-title { font-size: 18px; font-weight: 800; }
  .close-btn { background: none; border: none; font-size: 24px; color: var(--muted); cursor: pointer; }

  .form-group { margin-bottom: 14px; }
  .form-group label { display: block; font-size: 13px; font-weight: 700; color: var(--text); margin-bottom: 6px; }
  .form-control { width: 100%; padding: 12px 14px; border: 1.5px solid var(--border); border-radius: 12px; font-size: 15px; font-weight: 600; outline: none; }
  .form-control:focus { border-color: var(--accent); }
  .flex-2 { display: flex; gap: 10px; }
  .flex-2 > * { flex: 1; }
</style>
</head>
<body>

<header>
  <div class="logo">Auto<span>Trade</span></div>
  <div class="rate-badge" onclick="editRate()">1 $ = <b id="headRate">90.0</b> ₽ ✏️</div>
</header>

<div class="content">

  <!-- ================= СВОДКА ================= -->
  <div id="screen-dashboard" class="screen active">
    <div class="card" style="background: linear-gradient(135deg, #0f172a, #1e3a8a); color: #fff;">
      <div class="card-title" style="color: #94a3b8;">Чистый капитал (Активы − Долги)</div>
      <div style="font-size: 28px; font-weight: 900;" id="dashNetRub">0 ₽</div>
      <div style="color: #38bdf8; font-size: 16px; font-weight: 700; margin-top: 2px;" id="dashNetUsd">$ 0</div>
    </div>

    <div class="grid-2">
      <div class="card">
        <div class="card-title">В машинах и товарах</div>
        <div class="stat-val" id="dashInGoodsRub">0 ₽</div>
        <div class="stat-sub" id="dashInGoodsUsd">$ 0</div>
      </div>
      <div class="card">
        <div class="card-title">Чистая прибыль</div>
        <div class="stat-val" style="color: var(--success);" id="dashProfitRub">+0 ₽</div>
        <div class="stat-sub" id="dashProfitUsd">+$ 0</div>
      </div>
    </div>

    <div class="card alert-card" id="debtAlertBox" style="display:none;">
      <div class="card-title" style="color: var(--danger);">Мои долги к возврату</div>
      <div class="stat-val" id="dashDebtRub">0 ₽</div>
      <div class="stat-sub" style="color: #991b1b;" id="dashDebtUsd">$ 0</div>
    </div>

    <div class="card">
      <div class="card-title">Статус авто в работе</div>
      <div class="grid-2" style="margin-top: 10px;">
        <div class="stat-box">🚚 В пути: <b id="countRoad">0</b></div>
        <div class="stat-box">📍 В наличии: <b id="countStock">0</b></div>
      </div>
      <button class="btn btn-primary" style="margin-top: 14px;" onclick="openAddCarModal()">+ Добавить автомобиль</button>
    </div>
  </div>

  <!-- ================= МАШИНЫ ================= -->
  <div id="screen-cars" class="screen">
    <button class="btn btn-primary" style="margin-bottom: 14px;" onclick="openAddCarModal()">+ Добавить машину из Киргизии</button>
    
    <div class="filter-row">
      <div class="filter-chip active" onclick="filterCars('all', this)">Все (<span id="fcAll">0</span>)</div>
      <div class="filter-chip" onclick="filterCars('road', this)">В пути (<span id="fcRoad">0</span>)</div>
      <div class="filter-chip" onclick="filterCars('stock', this)">В наличии (<span id="fcStock">0</span>)</div>
      <div class="filter-chip" onclick="filterCars('sold', this)">Проданы (<span id="fcSold">0</span>)</div>
    </div>

    <div id="carsList"></div>
  </div>

  <!-- ================= ТОВАРЫ ================= -->
  <div id="screen-goods" class="screen">
    <button class="btn btn-primary" style="margin-bottom: 14px;" onclick="openAddGoodModal()">+ Добавить телефон / товар</button>
    <div id="goodsList"></div>
  </div>

  <!-- ================= ДЕНЬГИ И ДОЛГИ ================= -->
  <div id="screen-money" class="screen">
    <div class="card">
      <div class="card-title">Мои заёмные средства (Кому должен я)</div>
      <div id="debtsList" style="margin-top: 10px;"></div>
      <button class="btn btn-outline" style="margin-top: 10px;" onclick="openAddDebtModal()">+ Записать новый долг</button>
    </div>
  </div>

  <!-- ================= ЕЩЁ ================= -->
  <div id="screen-more" class="screen">
    <div class="card">
      <div class="card-title">Справочник поставщиков (Киргизия)</div>
      <div id="suppliersList"></div>
      <button class="btn btn-outline" style="margin-top: 10px;" onclick="openAddSupplierModal()">+ Добавить поставщика</button>
    </div>

    <div class="card">
      <div class="card-title">Данные и резервные копии</div>
      <button class="btn btn-outline" style="margin-bottom: 8px;" onclick="exportData()">💾 Скачать резервную копию (JSON)</button>
      <button class="btn btn-danger btn-sm" onclick="resetDemo()">🔄 Сбросить и загрузить демо-данные</button>
    </div>
  </div>

</div>

<!-- Нижняя навигация -->
<nav>
  <button class="nav-btn active" onclick="switchTab('dashboard', this)"><span class="icon">📊</span>Сводка</button>
  <button class="nav-btn" onclick="switchTab('cars', this)"><span class="icon">🚗</span>Машины</button>
  <button class="nav-btn" onclick="switchTab('goods', this)"><span class="icon">📱</span>Товары</button>
  <button class="nav-btn" onclick="switchTab('money', this)"><span class="icon">💵</span>Долги</button>
  <button class="nav-btn" onclick="switchTab('more', this)"><span class="icon">⚙️</span>Ещё</button>
</nav>

<!-- МОДАЛКА: Добавить машину -->
<div id="modalAddCar" class="modal">
  <div class="modal-box">
    <div class="modal-head">
      <div class="modal-title">Новый автомобиль</div>
      <button class="close-btn" onclick="closeModals()">✕</button>
    </div>
    <form onsubmit="saveCar(event)">
      <div class="form-group">
        <label>Марка и модель</label>
        <input class="form-control" id="carTitle" placeholder="Toyota Camry 70" required>
      </div>
      <div class="flex-2">
        <div class="form-group">
          <label>Год</label>
          <input class="form-control" id="carYear" type="number" value="2021">
        </div>
        <div class="form-group">
          <label>VIN-код</label>
          <input class="form-control" id="carVin" placeholder="17 знаков">
        </div>
      </div>
      <div class="form-group">
        <label>Поставщик в Киргизии</label>
        <select class="form-control" id="carSupplier"></select>
      </div>
      <div class="flex-2">
        <div class="form-group">
          <label>Цена закупки в $</label>
          <input class="form-control" id="carBuyUsd" type="number" placeholder="15000" oninput="syncCarRub()" required>
        </div>
        <div class="form-group">
          <label>Курс ($/₽)</label>
          <input class="form-control" id="carRate" type="number" step="0.1" value="90" oninput="syncCarRub()">
        </div>
      </div>
      <div class="form-group">
        <label>Цена закупки в ₽</label>
        <input class="form-control" id="carBuyRub" type="number" readonly style="background:var(--bg)">
      </div>
      <div class="form-group">
        <label>Статус</label>
        <select class="form-control" id="carStatus">
          <option value="road">В пути из Киргизии</option>
          <option value="stock">В наличии (в РФ)</option>
        </select>
      </div>
      <button class="btn btn-primary" type="submit">Сохранить машину</button>
    </form>
  </div>
</div>

<!-- МОДАЛКА: Добавить расход по машине -->
<div id="modalAddExpense" class="modal">
  <div class="modal-box">
    <div class="modal-head">
      <div class="modal-title">Добавить расход по авто</div>
      <button class="close-btn" onclick="closeModals()">✕</button>
    </div>
    <form onsubmit="saveExpense(event)">
      <input type="hidden" id="expCarId">
      <div class="form-group">
        <label>Тип расхода</label>
        <input class="form-control" id="expTitle" placeholder="Таможня / Перегон / СБКТС / Ремонт" required>
      </div>
      <div class="flex-2">
        <div class="form-group">
          <label>Сумма в ₽</label>
          <input class="form-control" id="expRub" type="number" placeholder="50000" required>
        </div>
      </div>
      <button class="btn btn-primary" type="submit">Добавить к себестоимости</button>
    </form>
  </div>
</div>

<!-- МОДАЛКА: Продажа авто -->
<div id="modalSellCar" class="modal">
  <div class="modal-box">
    <div class="modal-head">
      <div class="modal-title">Оформление продажи</div>
      <button class="close-btn" onclick="closeModals()">✕</button>
    </div>
    <form onsubmit="saveSale(event)">
      <input type="hidden" id="sellCarId">
      <div class="form-group">
        <label>ФИО Покупателя и Телефон</label>
        <input class="form-control" id="sellBuyer" placeholder="Иванов Иван +7999..." required>
      </div>
      <div class="flex-2">
        <div class="form-group">
          <label>Цена продажи в ₽</label>
          <input class="form-control" id="sellPriceRub" type="number" placeholder="2200000" required>
        </div>
        <div class="form-group">
          <label>Курс на день продажи</label>
          <input class="form-control" id="sellRate" type="number" step="0.1" value="90">
        </div>
      </div>
      <div class="form-group">
        <label>Тип оплаты</label>
        <select class="form-control" id="sellType">
          <option value="cash">100% Оплата (Наличные/Безнал)</option>
          <option value="credit">Исламская рассрочка</option>
        </select>
      </div>
      <button class="btn btn-accent" type="submit">Зафиксировать продажу</button>
    </form>
  </div>
</div>

<!-- МОДАЛКА: Добавить долг -->
<div id="modalAddDebt" class="modal">
  <div class="modal-box">
    <div class="modal-head">
      <div class="modal-title">Записать взятый долг</div>
      <button class="close-btn" onclick="closeModals()">✕</button>
    </div>
    <form onsubmit="saveDebt(event)">
      <div class="form-group">
        <label>Кому должен (ФИО / Займодатель)</label>
        <input class="form-control" id="debtCreditor" placeholder="Ахмад" required>
      </div>
      <div class="flex-2">
        <div class="form-group">
          <label>Сумма</label>
          <input class="form-control" id="debtAmount" type="number" required>
        </div>
        <div class="form-group">
          <label>Валюта долга</label>
          <select class="form-control" id="debtCurrency">
            <option value="USD">Доллары ($)</option>
            <option value="RUB">Рубли (₽)</option>
          </select>
        </div>
      </div>
      <div class="form-group">
        <label>Срок возврата</label>
        <input class="form-control" id="debtDue" type="date">
      </div>
      <button class="btn btn-primary" type="submit">Записать долг</button>
    </form>
  </div>
</div>

<!-- МОДАЛКА: Добавить товар -->
<div id="modalAddGood" class="modal">
  <div class="modal-box">
    <div class="modal-head">
      <div class="modal-title">Новый товар / Телефон</div>
      <button class="close-btn" onclick="closeModals()">✕</button>
    </div>
    <form onsubmit="saveGood(event)">
      <div class="form-group">
        <label>Наименование и модель</label>
        <input class="form-control" id="goodTitle" placeholder="iPhone 15 Pro Max 256GB" required>
      </div>
      <div class="form-group">
        <label>Серийный номер / IMEI</label>
        <input class="form-control" id="goodImei" placeholder="352...">
      </div>
      <div class="flex-2">
        <div class="form-group">
          <label>Цена закупки в ₽</label>
          <input class="form-control" id="goodBuy" type="number" required>
        </div>
        <div class="form-group">
          <label>Цена продажи в ₽</label>
          <input class="form-control" id="goodSell" type="number" required>
        </div>
      </div>
      <button class="btn btn-primary" type="submit">Сохранить товар</button>
    </form>
  </div>
</div>

<script>
// --- ХРАНИЛИЩЕ ДАННЫХ ---
const DEFAULT_STATE = {
  rate: 90,
  cars: [
    { id: 1, title: 'Hyundai Sonata', year: 2020, vin: 'KMHEC41...', supplier: 'Азамат (Бишкек)', buyUsd: 12000, rate: 89.5, status: 'stock', expenses: [{title: 'Перегон', rub: 45000}, {title: 'СБКТС+ЭПТС', rub: 35000}], sold: false },
    { id: 2, title: 'Kia K5 Luxury', year: 2021, vin: 'KNAGE41...', supplier: 'Улан (Ош)', buyUsd: 14500, rate: 90.0, status: 'road', expenses: [{title: 'Залог перегонщику', rub: 30000}], sold: false },
    { id: 3, title: 'Toyota Camry 70', year: 2019, vin: 'JTNB11...', supplier: 'Азамат (Бишкек)', buyUsd: 16000, rate: 88.0, status: 'sold', expenses: [{title: 'Таможня+Утиль', rub: 280000}], sold: true, salePriceRub: 2150000, saleRate: 90, buyer: 'Мухаммад +7988...' }
  ],
  goods: [
    { id: 1, title: 'iPhone 15 Pro 128GB', imei: '35891...', buyRub: 85000, sellRub: 98000, sold: false }
  ],
  debts: [
    { id: 1, creditor: 'Руслан инвестор', amount: 10000, currency: 'USD', due: '2025-12-01' }
  ],
  suppliers: ['Азамат (Бишкек)', 'Улан (Ош)', 'Бектур (Дордой)']
};

let DB = JSON.parse(localStorage.getItem('AUTOTRADE_DB')) || DEFAULT_STATE;

function saveDB() {
  localStorage.setItem('AUTOTRADE_DB', JSON.stringify(DB));
  renderAll();
}

function fmt(n) { return (n || 0).toLocaleString('ru-RU') + ' ₽'; }
function fmtUsd(n) { return '$ ' + Math.round(n || 0).toLocaleString('en-US'); }

function editRate() {
  const r = prompt('Введите текущий курс $ (рублей за 1 доллар):', DB.rate);
  if (r && !isNaN(r)) { DB.rate = parseFloat(r); saveDB(); }
}

function switchTab(tab, el) {
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
  document.getElementById('screen-' + tab).classList.add('active');
  el.classList.add('active');
}

function closeModals() {
  document.querySelectorAll('.modal').forEach(m => m.classList.remove('active'));
}

// --- СВОДКА ---
function renderDashboard() {
  document.getElementById('headRate').innerText = DB.rate.toFixed(1);

  let inGoodsRub = 0;
  let profitRub = 0;
  let countRoad = 0;
  let countStock = 0;

  DB.cars.forEach(c => {
    const buyRub = c.buyUsd * c.rate;
    const expRub = (c.expenses || []).reduce((sum, e) => sum + e.rub, 0);
    const costRub = buyRub + expRub;

    if (!c.sold) {
      inGoodsRub += costRub;
      if (c.status === 'road') countRoad++;
      if (c.status === 'stock') countStock++;
    } else {
      profitRub += (c.salePriceRub - costRub);
    }
  });

  DB.goods.forEach(g => {
    if (!g.sold) inGoodsRub += g.buyRub;
    else profitRub += (g.sellRub - g.buyRub);
  });

  let debtRub = 0;
  let debtUsd = 0;
  DB.debts.forEach(d => {
    if (d.currency === 'USD') { debtUsd += d.amount; debtRub += d.amount * DB.rate; }
    else { debtRub += d.amount; debtUsd += d.amount / DB.rate; }
  });

  const netRub = inGoodsRub + profitRub - debtRub;

  document.getElementById('dashNetRub').innerText = fmt(netRub);
  document.getElementById('dashNetUsd').innerText = fmtUsd(netRub / DB.rate);
  document.getElementById('dashInGoodsRub').innerText = fmt(inGoodsRub);
  document.getElementById('dashInGoodsUsd').innerText = fmtUsd(inGoodsRub / DB.rate);
  document.getElementById('dashProfitRub').innerText = (profitRub >= 0 ? '+' : '') + fmt(profitRub);
  document.getElementById('dashProfitUsd').innerText = (profitRub >= 0 ? '+' : '') + fmtUsd(profitRub / DB.rate);
  
  document.getElementById('countRoad').innerText = countRoad;
  document.getElementById('countStock').innerText = countStock;

  const debtBox = document.getElementById('debtAlertBox');
  if (debtRub > 0) {
    debtBox.style.display = 'block';
    document.getElementById('dashDebtRub').innerText = fmt(debtRub);
    document.getElementById('dashDebtUsd').innerText = fmtUsd(debtUsd);
  } else {
    debtBox.style.display = 'none';
  }
}

// --- МАШИНЫ ---
let currentCarFilter = 'all';
function filterCars(f, el) {
  currentCarFilter = f;
  document.querySelectorAll('.filter-chip').forEach(c => c.classList.remove('active'));
  el.classList.add('active');
  renderCars();
}

function renderCars() {
  const list = document.getElementById('carsList');
  list.innerHTML = '';

  let cAll = 0, cRoad = 0, cStock = 0, cSold = 0;

  DB.cars.forEach(c => {
    cAll++;
    if (c.sold) cSold++;
    else if (c.status === 'road') cRoad++;
    else if (c.status === 'stock') cStock++;

    if (currentCarFilter === 'road' && (c.status !== 'road' || c.sold)) return;
    if (currentCarFilter === 'stock' && (c.status !== 'stock' || c.sold)) return;
    if (currentCarFilter === 'sold' && !c.sold) return;

    const buyRub = c.buyUsd * c.rate;
    const expRub = (c.expenses || []).reduce((sum, e) => sum + e.rub, 0);
    const costRub = buyRub + expRub;

    let pill = `<span class="pill pill-stock">В наличии</span>`;
    if (c.sold) pill = `<span class="pill pill-sold">Продано</span>`;
    else if (c.status === 'road') pill = `<span class="pill pill-road">В пути</span>`;

    const div = document.createElement('div');
    div.className = 'item-card';
    div.innerHTML = `
      <div class="item-head">
        <div>
          <div class="item-title">${c.title} (${c.year})</div>
          <div class="item-sub">VIN: ${c.vin || '—'} • ${c.supplier || 'Поставщик не указан'}</div>
        </div>
        ${pill}
      </div>
      
      <div class="item-details">
        <div>Закупка: <b>${fmtUsd(c.buyUsd)}</b> (${fmt(buyRub)})</div>
        <div>Расходы: <b>${fmt(expRub)}</b></div>
        <div>Себестоимость: <b style="color:var(--primary)">${fmt(costRub)}</b></div>
        <div>Курс закупки: <b>${c.rate} ₽</b></div>
      </div>

      ${c.sold ? `
        <div style="background:var(--success-bg); padding:10px; border-radius:10px; font-size:13px; color:var(--success); margin-bottom:8px;">
          Продано за: <b>${fmt(c.salePriceRub)}</b> • Прибыль: <b>+${fmt(c.salePriceRub - costRub)}</b>
        </div>
      ` : `
        <div class="item-actions">
          <button class="btn btn-outline btn-sm" onclick="openExpenseModal(${c.id})">+ Расход</button>
          ${c.status === 'road' ? `<button class="btn btn-outline btn-sm" onclick="markArrived(${c.id})">📍 Прибыла в РФ</button>` : ''}
          <button class="btn btn-accent btn-sm" onclick="openSellModal(${c.id})">💰 Продать</button>
        </div>
      `}
    `;
    list.appendChild(div);
  });

  document.getElementById('fcAll').innerText = cAll;
  document.getElementById('fcRoad').innerText = cRoad;
  document.getElementById('fcStock').innerText = cStock;
  document.getElementById('fcSold').innerText = cSold;
}

function openAddCarModal() {
  const sel = document.getElementById('carSupplier');
  sel.innerHTML = DB.suppliers.map(s => `<option value="${s}">${s}</option>`).join('');
  document.getElementById('carRate').value = DB.rate;
  syncCarRub();
  document.getElementById('modalAddCar').classList.add('active');
}

function syncCarRub() {
  const usd = parseFloat(document.getElementById('carBuyUsd').value) || 0;
  const rate = parseFloat(document.getElementById('carRate').value) || DB.rate;
  document.getElementById('carBuyRub').value = Math.round(usd * rate);
}

function saveCar(e) {
  e.preventDefault();
  const car = {
    id: Date.now(),
    title: document.getElementById('carTitle').value,
    year: parseInt(document.getElementById('carYear').value),
    vin: document.getElementById('carVin').value,
    supplier: document.getElementById('carSupplier').value,
    buyUsd: parseFloat(document.getElementById('carBuyUsd').value),
    rate: parseFloat(document.getElementById('carRate').value),
    status: document.getElementById('carStatus').value,
    expenses: [],
    sold: false
  };
  DB.cars.unshift(car);
  saveDB();
  closeModals();
}

function openExpenseModal(carId) {
  document.getElementById('expCarId').value = carId;
  document.getElementById('modalAddExpense').classList.add('active');
}

function saveExpense(e) {
  e.preventDefault();
  const carId = parseInt(document.getElementById('expCarId').value);
  const car = DB.cars.find(c => c.id === carId);
  if (car) {
    car.expenses.push({
      title: document.getElementById('expTitle').value,
      rub: parseFloat(document.getElementById('expRub').value)
    });
    saveDB();
  }
  closeModals();
}

function markArrived(carId) {
  const car = DB.cars.find(c => c.id === carId);
  if (car) { car.status = 'stock'; saveDB(); }
}

function openSellModal(carId) {
  document.getElementById('sellCarId').value = carId;
  document.getElementById('sellRate').value = DB.rate;
  document.getElementById('modalSellCar').classList.add('active');
}

function saveSale(e) {
  e.preventDefault();
  const carId = parseInt(document.getElementById('sellCarId').value);
  const car = DB.cars.find(c => c.id === carId);
  if (car) {
    car.sold = true;
    car.status = 'sold';
    car.buyer = document.getElementById('sellBuyer').value;
    car.salePriceRub = parseFloat(document.getElementById('sellPriceRub').value);
    car.saleRate = parseFloat(document.getElementById('sellRate').value);
    saveDB();
  }
  closeModals();
}

// --- ТОВАРЫ ---
function renderGoods() {
  const list = document.getElementById('goodsList');
  list.innerHTML = DB.goods.map(g => `
    <div class="item-card">
      <div class="item-head">
        <div>
          <div class="item-title">${g.title}</div>
          <div class="item-sub">IMEI: ${g.imei || '—'}</div>
        </div>
        <span class="pill ${g.sold ? 'pill-sold' : 'pill-stock'}">${g.sold ? 'Продано' : 'В наличии'}</span>
      </div>
      <div class="item-details">
        <div>Закупка: <b>${fmt(g.buyRub)}</b></div>
        <div>Продажа: <b>${fmt(g.sellRub)}</b></div>
        <div>Прибыль: <b style="color:var(--success)">+${fmt(g.sellRub - g.buyRub)}</b></div>
      </div>
    </div>
  `).join('');
}

function openAddGoodModal() {
  document.getElementById('modalAddGood').classList.add('active');
}

function saveGood(e) {
  e.preventDefault();
  DB.goods.unshift({
    id: Date.now(),
    title: document.getElementById('goodTitle').value,
    imei: document.getElementById('goodImei').value,
    buyRub: parseFloat(document.getElementById('goodBuy').value),
    sellRub: parseFloat(document.getElementById('goodSell').value),
    sold: false
  });
  saveDB();
  closeModals();
}

// --- ДОЛГИ ---
function renderDebts() {
  const list = document.getElementById('debtsList');
  list.innerHTML = DB.debts.map(d => `
    <div class="item-card" style="border-left: 4px solid var(--danger);">
      <div class="item-head">
        <div>
          <div class="item-title">${d.creditor}</div>
          <div class="item-sub">Срок возврата: ${d.due || 'Не указан'}</div>
        </div>
        <div style="font-size:16px; font-weight:800; color:var(--danger)">
          ${d.currency === 'USD' ? fmtUsd(d.amount) + ` (${fmt(d.amount * DB.rate)})` : fmt(d.amount)}
        </div>
      </div>
    </div>
  `).join('') || '<div style="color:var(--muted); font-size:14px;">Долгов нет 🎉</div>';
}

function openAddDebtModal() {
  document.getElementById('modalAddDebt').classList.add('active');
}

function saveDebt(e) {
  e.preventDefault();
  DB.debts.push({
    id: Date.now(),
    creditor: document.getElementById('debtCreditor').value,
    amount: parseFloat(document.getElementById('debtAmount').value),
    currency: document.getElementById('debtCurrency').value,
    due: document.getElementById('debtDue').value
  });
  saveDB();
  closeModals();
}

// --- ПОСТАВЩИКИ ---
function renderSuppliers() {
  document.getElementById('suppliersList').innerHTML = DB.suppliers.map(s => `
    <div style="padding: 10px 0; border-bottom: 1px solid var(--border); font-weight: 600;">🇰🇬 ${s}</div>
  `).join('');
}

function openAddSupplierModal() {
  const name = prompt('Имя и город поставщика:');
  if (name) { DB.suppliers.push(name); saveDB(); }
}

function exportData() {
  const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(DB));
  const a = document.createElement('a');
  a.href = dataStr;
  a.download = `autotrade_backup_${new Date().toISOString().slice(0,10)}.json`;
  a.click();
}

function resetDemo() {
  if (confirm('Сбросить все данные к исходным демо?')) {
    DB = JSON.parse(JSON.stringify(DEFAULT_STATE));
    saveDB();
  }
}

function renderAll() {
  renderDashboard();
  renderCars();
  renderGoods();
  renderDebts();
  renderSuppliers();
}

renderAll();
</script>
</body>
</html>
