<!DOCTYPE html><html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Zav Express — Order Form & Admin</title>
  <style>
    :root{--accent:#0066cc;--muted:#666}
    *{box-sizing:border-box}
    body{font-family:Inter,system-ui,Segoe UI,Roboto,Arial;background:#f4f6f8;margin:0;color:#111}
    header{background:var(--accent);color:#fff;padding:18px 20px;text-align:center}
    .container{max-width:980px;margin:22px auto;padding:0 16px}
    .card{background:#fff;border-radius:10px;padding:18px;box-shadow:0 6px 18px rgba(12,30,60,.08);}
    h1{margin:0;font-size:22px}
    label{display:block;font-weight:600;margin-top:12px}
    input[type=text], input[type=tel], input[type=url], textarea, select, input[type=number] {width:100%;padding:10px;border:1px solid #d8dde6;border-radius:8px;margin-top:6px}
    textarea{resize:vertical}
    .row{display:flex;gap:12px}
    .col{flex:1}
    .actions{display:flex;gap:10px;margin-top:14px}
    button{background:var(--accent);color:#fff;border:0;padding:10px 14px;border-radius:8px;cursor:pointer}
    button.secondary{background:#e6eefb;color:var(--accent);border:1px solid rgba(0,102,204,.08)}
    footer{margin:24px 0;text-align:center;color:var(--muted)}
    .small{font-size:13px;color:var(--muted)}/* Admin table */
table{width:100%;border-collapse:collapse;margin-top:12px}
th,td{padding:10px;border-bottom:1px solid #eef2f6;text-align:left;font-size:14px}
th{background:#fafcff}
.status{padding:6px 8px;border-radius:6px;font-weight:700;font-size:12px}
.status.pending{background:#fff7e6;color:#b46b00}
.status.fulfilled{background:#eaffef;color:#087f3a}
.filter-row{display:flex;gap:8px;align-items:center;margin-top:12px}

@media (max-width:720px){.row{flex-direction:column}}

  </style>
</head>
<body>
  <header>
    <h1>Zav Express</h1>
    <div class="small">Middleman ordering from Lazada & Shopee — submit a product link, specs, qty, and we handle the rest</div>
  </header>  <div class="container">
    <div id="mainView" class="card">
      <h2>Place an Order</h2>
      <form id="orderForm">
        <div class="row">
          <div class="col">
            <label for="fullName">Full name</label>
            <input id="fullName" type="text" required placeholder="Juan dela Cruz" />
          </div>
          <div class="col">
            <label for="phone">Contact number</label>
            <input id="phone" type="tel" required placeholder="09xxxxxxxxx" />
          </div>
        </div><label for="address">Complete address</label>
    <textarea id="address" rows="2" placeholder="Barangay, Municipality, Province"></textarea>

    <div class="row">
      <div class="col">
        <label for="platform">Platform</label>
        <select id="platform">
          <option value="Shopee">Shopee</option>
          <option value="Lazada">Lazada</option>
          <option value="Other">Other</option>
        </select>
      </div>
      <div class="col">
        <label for="productLink">Product link (paste full URL)</label>
        <input id="productLink" type="url" placeholder="https://shopee.ph/.. or https://www.lazada.ph/..." required />
      </div>
    </div>

    <div class="row">
      <div class="col">
        <label for="quantity">Quantity</label>
        <input id="quantity" type="number" min="1" value="1" required />
      </div>
      <div class="col">
        <label for="variation">Variation / Specifications</label>
        <input id="variation" type="text" placeholder="Color: Black, Size: M" />
      </div>
    </div>

    <label for="notes">Additional notes / delivery instructions</label>
    <textarea id="notes" rows="2" placeholder="Any special instructions or voucher codes"></textarea>

    <label for="payment">Preferred payment method</label>
    <select id="payment">
      <option>Cash on Delivery</option>
      <option>GCash</option>
      <option>Bank Transfer</option>
      <option>PayPal</option>
    </select>

    <div class="actions">
      <button type="submit">Submit order</button>
      <button type="button" class="secondary" id="viewAdminBtn">Admin login</button>
      <button type="button" class="secondary" id="clearDemo">Clear demo orders</button>
    </div>

    <div id="msg" class="small" style="margin-top:12px"></div>
  </form>

  <hr style="margin:18px 0" />
  <div class="small">Demo notes: this page uses your browser storage (localStorage) to save orders. For a real site with multiple admins and persistent storage you should connect to a server and database — I can help set that up.</div>
</div>

<!-- Admin panel (hidden by default) -->
<div id="adminView" class="card" style="display:none;margin-top:18px">
  <h2>Admin — Orders</h2>
  <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
    <div class="small">Logged in as <strong>Zav Admin</strong></div>
    <div style="flex:1"></div>
    <button id="exportCsv" class="secondary">Export CSV</button>
    <button id="logoutBtn" class="secondary">Logout</button>
  </div>

  <div class="filter-row">
    <label>Search:</label>
    <input id="searchInput" placeholder="name, phone, product link..." />
    <label>Status:</label>
    <select id="filterStatus">
      <option value="all">All</option>
      <option value="pending">Pending</option>
      <option value="fulfilled">Fulfilled</option>
    </select>
  </div>

  <div id="ordersTableWrap">
    <table id="ordersTable">
      <thead>
        <tr>
          <th>#</th>
          <th>Name</th>
          <th>Phone</th>
          <th>Platform / Link</th>
          <th>Qty / Var</th>
          <th>Payment</th>
          <th>Notes</th>
          <th>When</th>
          <th>Status</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>
</div>

<footer class="small">Built for Zav Express — change the admin password inside the code before publishing. For hosting use Replit, Netlify, or Vercel.</footer>

  </div>  <!-- Admin Login Modal (simple) -->  <div id="loginModal" style="position:fixed;inset:0;display:none;align-items:center;justify-content:center;background:rgba(6,12,28,.45)">
    <div style="width:360px;background:#fff;padding:18px;border-radius:10px;box-shadow:0 8px 30px rgba(2,10,40,.4)">
      <h3 style="margin-top:0">Admin Login</h3>
      <label for="adminPass">Password</label>
      <input id="adminPass" type="password" />
      <div style="display:flex;gap:8px;margin-top:12px;justify-content:flex-end">
        <button id="loginSubmit">Login</button>
        <button id="loginCancel" class="secondary">Cancel</button>
      </div>
      <div id="loginMsg" class="small" style="margin-top:8px;color:#b00020"></div>
    </div>
  </div>  <script>
    /*******************************
     * Simple client-side order system
     * - Saves orders to localStorage
     * - Admin can login (client-side password) to view/manage
     * NOTE: This is for demo / small-business use only. For production use a backend.
     *******************************/

    const STORAGE_KEY = 'zav_orders_v1';
    const ADMIN_PASSWORD = 'zavadmin123'; // change this before publishing

    // helpers
    const $ = (id) => document.getElementById(id);
    const readOrders = () => JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
    const writeOrders = (arr) => localStorage.setItem(STORAGE_KEY, JSON.stringify(arr));

    // submit order
    document.getElementById('orderForm').addEventListener('submit', function(e){
      e.preventDefault();
      const order = {
        id: 'ZAV' + Date.now(),
        name: $('fullName').value.trim(),
        phone: $('phone').value.trim(),
        address: $('address').value.trim(),
        platform: $('platform').value,
        productLink: $('productLink').value.trim(),
        quantity: parseInt($('quantity').value) || 1,
        variation: $('variation').value.trim(),
        notes: $('notes').value.trim(),
        payment: $('payment').value,
        status: 'pending',
        createdAt: new Date().toISOString()
      };

      // basic validation
      if(!order.name || !order.phone || !order.productLink) {
        $('msg').textContent = 'Please fill in required fields.';
        return;
      }

      const arr = readOrders();
      arr.unshift(order); // newest first
      writeOrders(arr);

      $('msg').textContent = 'Thank you! Your order has been received. We will contact you shortly.';
      this.reset();

      // small UX: clear message after 5s
      setTimeout(()=> $('msg').textContent = '', 5000);
    });

    // admin login
    document.getElementById('viewAdminBtn').addEventListener('click', ()=>{
      $('loginModal').style.display = 'flex';
      $('adminPass').value = '';
      $('loginMsg').textContent = '';
    });
    document.getElementById('loginCancel').addEventListener('click', ()=> $('loginModal').style.display = 'none');
    document.getElementById('loginSubmit').addEventListener('click', adminLogin);
    document.getElementById('adminPass').addEventListener('keydown', (e)=>{ if(e.key==='Enter') adminLogin(); });

    function adminLogin(){
      const pass = $('adminPass').value;
      if(pass === ADMIN_PASSWORD){
        $('loginModal').style.display = 'none';
        showAdminView();
      } else {
        $('loginMsg').textContent = 'Incorrect password.';
      }
    }

    function showAdminView(){
      $('mainView').style.display = 'none';
      $('adminView').style.display = 'block';
      renderOrdersTable();
    }

    document.getElementById('logoutBtn').addEventListener('click', ()=>{
      $('adminView').style.display = 'none';
      $('mainView').style.display = 'block';
    });

    // render table
    function renderOrdersTable(){
      const tbody = document.querySelector('#ordersTable tbody');
      tbody.innerHTML = '';
      const filter = $('filterStatus').value;
      const q = ($('searchInput').value || '').toLowerCase().trim();
      const arr = readOrders().filter(o => {
        if(filter !== 'all' && o.status !== filter) return false;
        if(!q) return true;
        return (o.name+ ' ' + o.phone + ' ' + o.productLink + ' ' + o.variation + ' ' + (o.notes||'')).toLowerCase().includes(q);
      });

      if(arr.length===0){
        tbody.innerHTML = '<tr><td colspan="10" class="small">No orders found.</td></tr>';
        return;
      }

      arr.forEach((o, idx)=>{
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td>${idx+1}</td>
          <td>${escapeHtml(o.name)}<div class="small">${escapeHtml(o.address)}</div></td>
          <td>${escapeHtml(o.phone)}</td>
          <td><div class="small">${escapeHtml(o.platform)}</div><a href="${escapeAttr(o.productLink)}" target="_blank">Open link</a></td>
          <td>${o.quantity} <div class="small">${escapeHtml(o.variation)}</div></td>
          <td>${escapeHtml(o.payment)}</td>
          <td class="small">${escapeHtml(o.notes || '')}</td>
          <td class="small">${new Date(o.createdAt).toLocaleString()}</td>
          <td><span class="status ${o.status}">${o.status}</span></td>
          <td>
            <button data-id="${o.id}" class="markBtn">Mark Fulfilled</button>
            <button data-id="${o.id}" class="deleteBtn secondary">Delete</button>
          </td>
        `;
        tbody.appendChild(tr);
      });

      // attach actions
      document.querySelectorAll('.markBtn').forEach(b=> b.addEventListener('click', (e)=>{
        const id = e.currentTarget.dataset.id; updateOrderStatus(id, 'fulfilled');
      }));
      document.querySelectorAll('.deleteBtn').forEach(b=> b.addEventListener('click', (e)=>{
        const id = e.currentTarget.dataset.id; deleteOrder(id);
      }));
    }

    function updateOrderStatus(id, status){
      const arr = readOrders();
      const idx = arr.findIndex(a=>a.id===id);
      if(idx>=0){ arr[idx].status = status; writeOrders(arr); renderOrdersTable(); }
    }

    function deleteOrder(id){
      if(!confirm('Delete this order? This action cannot be undone.')) return;
      let arr = readOrders();
      arr = arr.filter(a=>a.id!==id);
      writeOrders(arr); renderOrdersTable();
    }

    // export CSV
    document.getElementById('exportCsv').addEventListener('click', ()=>{
      const arr = readOrders();
      if(arr.length===0){ alert('No orders to export'); return; }
      const cols = ['id','name','phone','address','platform','productLink','quantity','variation','notes','payment','status','createdAt'];
      const csv = [cols.join(',')].concat(arr.map(o=>cols.map(c=>`"${String(o[c]||'').replace(/"/g,'""')}"`).join(','))).join('
');
      const blob = new Blob([csv], {type: 'text/csv'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a'); a.href = url; a.download = 'zav_orders.csv'; document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
    });

    // search & filter
    $('searchInput').addEventListener('input', renderOrdersTable);
    $('filterStatus').addEventListener('change', renderOrdersTable);

    // clear demo
    $('clearDemo').addEventListener('click', ()=>{
      if(confirm('Clear all demo orders from this browser?')){ localStorage.removeItem(STORAGE_KEY); $('msg').textContent='Demo orders cleared.'; setTimeout(()=>$('msg').textContent='',3000); }
    });

    // small escape functions for safety
    function escapeHtml(s){ if(!s) return ''; return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }
    function escapeAttr(s){ return String(s).replace(/"/g,'%22'); }

    // quick load: if user navigates to #admin show admin (for convenience)
    if(location.hash === '#admin'){
      $('mainView').style.display='none'; $('adminView').style.display='block'; renderOrdersTable();
    }

  </script></body>
</html>
