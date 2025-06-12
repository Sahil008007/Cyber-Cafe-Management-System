# Cyber-Cafe-Management-System

You have provided a detailed description of the Cyber Cafe Management System's purpose and functionality. It should manage and streamline cyber cafe operations including user registration, session tracking, billing, and resource allocation, automating time logging and payment processing. Previously, I was creating a responsive, modern single-page app with these features.

I will ensure the implementation covers all these aspects:
- User registration (add/edit/delete customers)
- Session tracking (start/stop usage sessions on computers by customers)
- Billing and payment processing (calculate session duration and billing)
- Resource allocation (assign available computers to customers)
- Automation of time logging and fees
- A dashboard for overview of system status
- Responsive UI with clean and modern design
- Local data persistence using localStorage

I will finalize and improve the existing implementation accordingly, making sure all features are present, intuitive, and the app is visually impressive, accessible, and fully responsive. All code will remain in a single HTML file with inline CSS and JS, using Google fonts and icons as required.

Now proceeding to update the complete project based on these requirements.


`cyber-cafe-management.html`
```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Cyber Cafe Management System</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet" />
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet" />
<style>
  /* Reset and base styles */
  *, *::before, *::after {
    box-sizing: border-box;
  }
  body {
    margin: 0; padding: 0;
    font-family: 'Inter', sans-serif;
    background: #f4f6f8;
    color: #222;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
  }

  /* Layout */
  #app {
    display: grid;
    grid-template-columns: 260px 1fr;
    grid-template-rows: 64px 1fr;
    height: 100vh;
    overflow: hidden;
  }

  /* Header */
  header {
    grid-column: 1 / -1;
    background: linear-gradient(135deg, #4a90e2 0%, #145ea8 100%);
    color: white;
    display: flex;
    align-items: center;
    padding: 0 24px;
    font-weight: 700;
    letter-spacing: 1.1px;
    font-size: 1.25rem;
    user-select: none;
    box-shadow: 0 2px 8px rgb(0 0 0 / 0.15);
    position: relative;
    z-index: 2;
  }

  /* Sidebar */
  nav.sidebar {
    background: white;
    border-right: 1px solid #ddd;
    box-shadow: inset -2px 0 6px rgb(0 0 0 / 0.05);
    display: flex;
    flex-direction: column;
    padding: 16px 0;
  }
  nav.sidebar a {
    display: flex;
    align-items: center;
    gap: 16px;
    padding: 14px 24px;
    color: #444;
    text-decoration: none;
    font-weight: 600;
    font-size: 0.95rem;
    transition: background-color 0.25s ease, color 0.25s ease;
  }
  nav.sidebar a.active,
  nav.sidebar a:hover {
    background: #4a90e2;
    color: white;
  }
  nav.sidebar a .material-icons {
    font-size: 20px;
  }

  /* Main content */
  main.content {
    background: #f9fafb;
    padding: 24px 32px;
    overflow-y: auto;
    position: relative;
  }

  /* Section titles */
  h2.section-title {
    font-size: 1.5rem;
    font-weight: 700;
    margin-bottom: 24px;
    color: #314659;
  }
  h3.sub-title {
    font-size: 1.2rem;
    font-weight: 600;
    margin-bottom: 18px;
    color: #4169e1;
  }

  /* Form elements */
  form {
    background: white;
    padding: 24px;
    border-radius: 12px;
    box-shadow: 0 6px 18px rgb(0 0 0 / 0.05);
    max-width: 640px;
  }
  label {
    display: block;
    font-weight: 600;
    margin-bottom: 6px;
    color: #555;
  }
  input[type="text"], input[type="number"], select {
    width: 100%;
    padding: 10px 14px;
    border-radius: 10px;
    border: 1.5px solid #ccc;
    font-size: 1rem;
    margin-bottom: 18px;
    transition: border-color 0.3s ease;
  }
  input[type="text"]:focus,
  input[type="number"]:focus,
  select:focus {
    outline: none;
    border-color: #4a90e2;
    box-shadow: 0 0 6px #4a90e2aa;
  }

  button {
    background: #4a90e2;
    border: none;
    color: white;
    padding: 12px 32px;
    border-radius: 12px;
    font-weight: 700;
    font-size: 1rem;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  button:hover:not(:disabled) {
    background: #3a74bf;
  }
  button:disabled {
    background: #a4c0e3;
    cursor: not-allowed;
  }

  /* Table styling */
  table {
    width: 100%;
    border-collapse: collapse;
    background: white;
    border-radius: 12px;
    overflow: hidden;
    box-shadow: 0 8px 24px rgb(0 0 0 / 0.08);
  }
  th, td {
    text-align: left;
    padding: 16px 20px;
    border-bottom: 1px solid #e0e0e0;
    vertical-align: middle;
  }
  th {
    background: #e4ebf6;
    font-weight: 600;
    color: #2c3c58;
  }
  tr:last-child td {
    border-bottom: none;
  }
  tr:hover {
    background: #f7fbff;
  }

  /* Action buttons inside tables */
  .table-btn {
    background: transparent;
    border: none;
    cursor: pointer;
    padding: 6px;
    border-radius: 50%;
    transition: background-color 0.25s ease;
    color: #4a90e2;
  }
  .table-btn:hover {
    background: #e0ecff;
  }
  .table-btn .material-icons {
    font-size: 20px;
    vertical-align: middle;
  }

  /* Session timer text */
  .session-timer {
    font-family: monospace;
    font-weight: 600;
    font-size: 1rem;
    color: #204d74;
  }

  /* Billing summary style */
  .billing-summary {
    margin-top: 12px;
    font-weight: 600;
    color: #2c3c58;
  }

  /* Responsive */
  @media (max-width: 960px) {
    #app {
      grid-template-columns: 1fr;
      grid-template-rows: 64px 1fr 60px;
    }
    nav.sidebar {
      display: flex;
      flex-direction: row;
      border-right: none;
      border-top: 1px solid #ddd;
      padding: 0;
      overflow-x: auto;
    }
    nav.sidebar a {
      flex: 1 0 auto;
      justify-content: center;
      padding: 12px 10px;
      font-size: 0.85rem;
      border-right: 1px solid #ddd;
    }
    nav.sidebar a:last-child {
      border-right: none;
    }
    main.content {
      padding: 16px 16px 80px;
    }
  }

  /* Scrollbar for main content */
  main.content::-webkit-scrollbar {
    width: 10px;
  }
  main.content::-webkit-scrollbar-track {
    background: #f4f6f8;
  }
  main.content::-webkit-scrollbar-thumb {
    background-color: #4a90e2;
    border-radius: 6px;
  }
</style>
</head>
<body>
<div id="app" role="application" aria-label="Cyber Cafe Management System">
  <header role="banner">
    Cyber Cafe Management System
  </header>
  <nav class="sidebar" aria-label="Primary">
    <a href="#" class="active" data-section="dashboard" role="tab" aria-selected="true" tabindex="0" id="tab-dashboard"><span class="material-icons" aria-hidden="true">dashboard</span> Dashboard</a>
    <a href="#" data-section="customers" role="tab" aria-selected="false" tabindex="-1" id="tab-customers"><span class="material-icons" aria-hidden="true">people</span> Customers</a>
    <a href="#" data-section="computers" role="tab" aria-selected="false" tabindex="-1" id="tab-computers"><span class="material-icons" aria-hidden="true">computer</span> Computers</a>
    <a href="#" data-section="sessions" role="tab" aria-selected="false" tabindex="-1" id="tab-sessions"><span class="material-icons" aria-hidden="true">timer</span> Sessions</a>
    <a href="#" data-section="transactions" role="tab" aria-selected="false" tabindex="-1" id="tab-transactions"><span class="material-icons" aria-hidden="true">receipt_long</span> Transactions</a>
  </nav>
  <main class="content" role="main" tabindex="0">
    <!-- Dynamic content area -->
  </main>
</div>

<script>
  (() => {
    // Data storage keys in localStorage
    const STORAGE_KEYS = {
      customers: 'ccms_customers',
      computers: 'ccms_computers',
      sessions: 'ccms_sessions',
      transactions: 'ccms_transactions'
    };

    // Load or initialize data
    let customers = JSON.parse(localStorage.getItem(STORAGE_KEYS.customers)) || [];
    let computers = JSON.parse(localStorage.getItem(STORAGE_KEYS.computers)) || [
      { id: 1, name: 'PC-1', status: 'available' },
      { id: 2, name: 'PC-2', status: 'available' },
      { id: 3, name: 'PC-3', status: 'available' },
      { id: 4, name: 'PC-4', status: 'available' },
      { id: 5, name: 'PC-5', status: 'available' }
    ];
    let sessions = JSON.parse(localStorage.getItem(STORAGE_KEYS.sessions)) || [];
    let transactions = JSON.parse(localStorage.getItem(STORAGE_KEYS.transactions)) || [];

    // Save data function
    function saveData() {
      localStorage.setItem(STORAGE_KEYS.customers, JSON.stringify(customers));
      localStorage.setItem(STORAGE_KEYS.computers, JSON.stringify(computers));
      localStorage.setItem(STORAGE_KEYS.sessions, JSON.stringify(sessions));
      localStorage.setItem(STORAGE_KEYS.transactions, JSON.stringify(transactions));
    }

    // DOM elements
    const navLinks = document.querySelectorAll('nav.sidebar a');
    const mainContent = document.querySelector('main.content');

    // Accessibility: keyboard nav for tabs
    navLinks.forEach(link => {
      link.addEventListener('keydown', e => {
        let index = Array.from(navLinks).indexOf(document.activeElement);
        if (e.key === 'ArrowRight' || e.key === 'ArrowDown') {
          e.preventDefault();
          let nextIndex = (index + 1) % navLinks.length;
          navLinks[nextIndex].focus();
        } else if (e.key === 'ArrowLeft' || e.key === 'ArrowUp') {
          e.preventDefault();
          let prevIndex = (index - 1 + navLinks.length) % navLinks.length;
          navLinks[prevIndex].focus();
        }
      });
    });

    // Nav click handler
    navLinks.forEach(link => {
      link.addEventListener('click', e => {
        e.preventDefault();
        setActiveSection(link.dataset.section);
      });
    });

    // Set active tab + load content
    function setActiveSection(section) {
      navLinks.forEach(link => {
        const isActive = link.dataset.section === section;
        link.classList.toggle('active', isActive);
        link.setAttribute('aria-selected', isActive ? 'true' : 'false');
        link.tabIndex = isActive ? 0 : -1;
      });
      loadSection(section);
    }

    // Format duration ms to hh:mm:ss
    function formatDuration(ms) {
      let totalSeconds = Math.floor(ms / 1000);
      const hours = Math.floor(totalSeconds / 3600);
      totalSeconds %= 3600;
      const minutes = Math.floor(totalSeconds / 60);
      const seconds = totalSeconds % 60;
      return [
        hours.toString().padStart(2, '0'), 
        minutes.toString().padStart(2, '0'), 
        seconds.toString().padStart(2, '0')
      ].join(':');
    }

    // Format date to readable string
    function formatDate(ts) {
      const d = new Date(ts);
      return d.toLocaleString();
    }

    // Load content by section
    function loadSection(section) {
      mainContent.innerHTML = '';
      switch(section){
        case 'dashboard': loadDashboard(); break;
        case 'customers': loadCustomers(); break;
        case 'computers': loadComputers(); break;
        case 'sessions': loadSessions(); break;
        case 'transactions': loadTransactions(); break;
        default: loadDashboard();
      }
    }

    /* DASHBOARD */
    function loadDashboard() {
      const container = document.createElement('section');
      container.setAttribute('aria-label', 'Dashboard');

      const title = document.createElement('h2');
      title.className = 'section-title';
      title.textContent = 'Dashboard Overview';

      const statsGrid = document.createElement('div');
      statsGrid.style.display = 'grid';
      statsGrid.style.gridTemplateColumns = 'repeat(auto-fit,minmax(180px,1fr))';
      statsGrid.style.gap = '24px';

      function createStatCard(label, value, icon){
        const card = document.createElement('div');
        card.style.background = 'white';
        card.style.borderRadius = '16px';
        card.style.padding = '20px';
        card.style.boxShadow = '0 6px 18px rgb(0 0 0 / 0.05)';
        card.style.display = 'flex';
        card.style.alignItems = 'center';
        card.style.gap = '16px';

        const iconSpan = document.createElement('span');
        iconSpan.className = 'material-icons';
        iconSpan.textContent = icon;
        iconSpan.style.fontSize = '32px';
        iconSpan.style.color = '#4a90e2';

        const texts = document.createElement('div');
        texts.style.display = 'flex';
        texts.style.flexDirection = 'column';
        texts.style.justifyContent = 'center';

        const valEl = document.createElement('div');
        valEl.style.fontWeight = '700';
        valEl.style.fontSize = '1.8rem';
        valEl.style.color = '#222';
        valEl.textContent = value;

        const labelEl = document.createElement('div');
        labelEl.style.fontSize = '0.875rem';
        labelEl.style.color = '#555';
        labelEl.textContent = label;

        texts.appendChild(valEl);
        texts.appendChild(labelEl);

        card.appendChild(iconSpan);
        card.appendChild(texts);

        return card;
      }

      const activeSessionsCount = sessions.length;
      const totalCustomers = customers.length;
      const totalComputers = computers.length;
      const totalTransactions = transactions.length;
      const computersInUse = computers.filter(c => c.status === 'in use').length;
      const computersAvailable = computers.filter(c => c.status === 'available').length;

      statsGrid.appendChild(createStatCard('Active Sessions', activeSessionsCount, 'timer'));
      statsGrid.appendChild(createStatCard('Total Customers', totalCustomers, 'people'));
      statsGrid.appendChild(createStatCard('Computers In Use', computersInUse, 'computer'));
      statsGrid.appendChild(createStatCard('Computers Available', computersAvailable, 'computer'));
      statsGrid.appendChild(createStatCard('Total Computers', totalComputers, 'devices'));
      statsGrid.appendChild(createStatCard('Total Transactions', totalTransactions, 'receipt_long'));

      container.appendChild(title);
      container.appendChild(statsGrid);
      mainContent.appendChild(container);
    }

    /* CUSTOMERS - Add/Edit/Delete */
    function loadCustomers() {
      const container = document.createElement('section');
      container.setAttribute('aria-label', 'Customers Management');

      const title = document.createElement('h2');
      title.className = 'section-title';
      title.textContent = 'Customers';

      // Add customer form
      const addFormTitle = document.createElement('h3');
      addFormTitle.className = 'sub-title';
      addFormTitle.textContent = 'Register New Customer';

      const form = document.createElement('form');
      form.setAttribute('aria-label', 'Add new customer form');
      form.addEventListener('submit', e => {
        e.preventDefault();
        const name = form.elements['name'].value.trim();
        const phone = form.elements['phone'].value.trim();
        if(name){
          customers.push({ id: Date.now(), name, phone });
          saveData();
          loadCustomers();
        }
      });

      const labelName = document.createElement('label');
      labelName.setAttribute('for', 'customer-name');
      labelName.textContent = 'Name *';
      const inputName = document.createElement('input');
      inputName.type = 'text';
      inputName.id = 'customer-name';
      inputName.name = 'name';
      inputName.required = true;
      inputName.autocomplete = 'off';

      const labelPhone = document.createElement('label');
      labelPhone.setAttribute('for', 'customer-phone');
      labelPhone.textContent = 'Phone (optional)';
      const inputPhone = document.createElement('input');
      inputPhone.type = 'text';
      inputPhone.id = 'customer-phone';
      inputPhone.name = 'phone';
      inputPhone.autocomplete = 'off';

      const submitBtn = document.createElement('button');
      submitBtn.type = 'submit';
      submitBtn.textContent = 'Add Customer';

      form.append(labelName, inputName, labelPhone, inputPhone, submitBtn);

      // Customers list table
      const table = document.createElement('table');
      table.setAttribute('aria-label', 'Registered customers list');

      const thead = document.createElement('thead');
      thead.innerHTML = `
        <tr>
          <th>Name</th>
          <th>Phone</th>
          <th aria-label="Edit customer"></th>
          <th aria-label="Delete customer"></th>
        </tr>
      `;
      const tbody = document.createElement('tbody');

      customers.forEach(cust => {
        const tr = document.createElement('tr');

        const tdName = document.createElement('td');
        tdName.textContent = cust.name;
        tdName.style.fontWeight = '600';

        const tdPhone = document.createElement('td');
        tdPhone.textContent = cust.phone || '---';

        const tdEdit = document.createElement('td');
        const btnEdit = document.createElement('button');
        btnEdit.className = 'table-btn';
        btnEdit.setAttribute('aria-label', `Edit customer ${cust.name}`);
        btnEdit.innerHTML = '<span class="material-icons">edit</span>';
        btnEdit.addEventListener('click', () => openEditCustomerModal(cust.id));
        tdEdit.appendChild(btnEdit);

        const tdDelete = document.createElement('td');
        const btnDelete = document.createElement('button');
        btnDelete.className = 'table-btn';
        btnDelete.setAttribute('aria-label', `Delete customer ${cust.name}`);
        btnDelete.innerHTML = '<span class="material-icons">delete</span>';
        btnDelete.addEventListener('click', () => {
          if(confirm(`Delete customer "${cust.name}"? This will cancel any active sessions.`)){
            // Cancel sessions linked to this customer
            sessions = sessions.filter(sess => sess.customerId !== cust.id);
            customers = customers.filter(c => c.id !== cust.id);
            saveData();
            loadCustomers();
          }
        });
        tdDelete.appendChild(btnDelete);

        tr.append(tdName, tdPhone, tdEdit, tdDelete);
        tbody.appendChild(tr);
      });

      table.append(thead, tbody);

      container.append(title, addFormTitle, form, table);
      mainContent.appendChild(container);
    }

    /* EDIT CUSTOMER MODAL */
    function openEditCustomerModal(customerId){
      const cust = customers.find(c => c.id === customerId);
      if(!cust) return alert('Customer not found.');

      // Create modal backdrop
      const backdrop = document.createElement('div');
      backdrop.style = `
        position: fixed; inset: 0; background: rgba(0,0,0,0.5);
        display: flex; align-items: center; justify-content: center;
        z-index: 10000;
      `;

      // Modal dialog
      const modal = document.createElement('div');
      modal.style = `
        background: white; border-radius: 12px; padding: 24px; max-width: 400px;
        width: 90vw; box-shadow: 0 6px 18px rgb(0 0 0 / 0.25);
        position: relative;
      `;
      modal.setAttribute('role', 'dialog');
      modal.setAttribute('aria-modal', 'true');
      modal.setAttribute('aria-labelledby', 'modal-title');

      const title = document.createElement('h3');
      title.id = 'modal-title';
      title.textContent = `Edit Customer: ${cust.name}`;
      title.style.marginBottom = '18px';

      const form = document.createElement('form');
      form.setAttribute('aria-label', 'Edit customer form');
      form.addEventListener('submit', e => {
        e.preventDefault();
        const newName = form.elements['name'].value.trim();
        const newPhone = form.elements['phone'].value.trim();
        if(newName){
          cust.name = newName;
          cust.phone = newPhone;
          saveData();
          document.body.removeChild(backdrop);
          loadCustomers();
        }
      });

      const labelName = document.createElement('label');
      labelName.setAttribute('for', 'edit-customer-name');
      labelName.textContent = 'Name *';
      const inputName = document.createElement('input');
      inputName.type = 'text';
      inputName.id = 'edit-customer-name';
      inputName.name = 'name';
      inputName.required = true;
      inputName.value = cust.name;

      const labelPhone = document.createElement('label');
      labelPhone.setAttribute('for', 'edit-customer-phone');
      labelPhone.textContent = 'Phone (optional)';
      const inputPhone = document.createElement('input');
      inputPhone.type = 'text';
      inputPhone.id = 'edit-customer-phone';
      inputPhone.name = 'phone';
      inputPhone.value = cust.phone || '';

      const btnSave = document.createElement('button');
      btnSave.type = 'submit';
      btnSave.textContent = 'Save Changes';

      const btnCancel = document.createElement('button');
      btnCancel.type = 'button';
      btnCancel.textContent = 'Cancel';
      btnCancel.style.marginLeft = '12px';
      btnCancel.addEventListener('click', () => {
        document.body.removeChild(backdrop);
      });

      form.append(labelName, inputName, labelPhone, inputPhone, btnSave, btnCancel);
      modal.append(title, form);
      backdrop.appendChild(modal);

      document.body.appendChild(backdrop);

      inputName.focus();
    }

    /* COMPUTERS */
    function loadComputers() {
      const container = document.createElement('section');
      container.setAttribute('aria-label', 'Computers Management');

      const title = document.createElement('h2');
      title.className = 'section-title';
      title.textContent = 'Computers';

      // Add computer form
      const addFormTitle = document.createElement('h3');
      addFormTitle.className = 'sub-title';
      addFormTitle.textContent = 'Add New Computer';

      const form = document.createElement('form');
      form.setAttribute('aria-label', 'Add new computer form');
      form.addEventListener('submit', e => {
        e.preventDefault();
        const name = form.elements['name'].value.trim();
        if(name){
          const exists = computers.some(c => c.name.toLowerCase() === name.toLowerCase());
          if(exists){
            alert('Computer with this name already exists.');
            return;
          }
          computers.push({ id: Date.now(), name, status: 'available' });
          saveData();
          loadComputers();
        }
      });

      const labelName = document.createElement('label');
      labelName.setAttribute('for', 'computer-name');
      labelName.textContent = 'Computer Name *';
      const inputName = document.createElement('input');
      inputName.type = 'text';
      inputName.id = 'computer-name';
      inputName.name = 'name';
      inputName.required = true;

      const submitBtn = document.createElement('button');
      submitBtn.type = 'submit';
      submitBtn.textContent = 'Add Computer';

      form.append(labelName, inputName, submitBtn);

      // Computers list table
      const table = document.createElement('table');
      table.setAttribute('aria-label', 'Configured computers list');

      const thead = document.createElement('thead');
      thead.innerHTML = `
        <tr>
          <th>Name</th>
          <th>Status</th>
          <th aria-label="Edit computer"></th>
          <th aria-label="Delete computer"></th>
        </tr>
      `;
      const tbody = document.createElement('tbody');

      computers.forEach(comp => {
        const tr = document.createElement('tr');

        const tdName = document.createElement('td');
        tdName.textContent = comp.name;
        tdName.style.fontWeight = '600';

        const tdStatus = document.createElement('td');
        tdStatus.textContent = comp.status === 'available' ? 'Available' : 'In Use';
        tdStatus.style.color = comp.status === 'available' ? '#2a953e' : '#d9534f';
        tdStatus.style.fontWeight = '700';

        const tdEdit = document.createElement('td');
        const btnEdit = document.createElement('button');
        btnEdit.className = 'table-btn';
        btnEdit.setAttribute('aria-label', `Edit computer ${comp.name}`);
        btnEdit.innerHTML = '<span class="material-icons">edit</span>';
        btnEdit.addEventListener('click', () => openEditComputerModal(comp.id));
        tdEdit.appendChild(btnEdit);

        const tdDelete = document.createElement('td');
        const btnDelete = document.createElement('button');
        btnDelete.className = 'table-btn';
        btnDelete.setAttribute('aria-label', `Delete computer ${comp.name}`);
        btnDelete.innerHTML = '<span class="material-icons">delete</span>';
        btnDelete.addEventListener('click', () => {
          // Ensure not deleting computer in active session
          if (comp.status === 'in use'){
            alert('Cannot delete a computer currently in use.');
            return;
          }
          if(confirm(`Delete computer "${comp.name}"?`)){
            computers = computers.filter(c => c.id !== comp.id);
            saveData();
            loadComputers();
          }
        });
        tdDelete.appendChild(btnDelete);

        tr.append(tdName, tdStatus, tdEdit, tdDelete);
        tbody.appendChild(tr);
      });

      table.append(thead, tbody);

      container.append(title, addFormTitle, form, table);
      mainContent.appendChild(container);
    }

    /* EDIT COMPUTER MODAL */
    function openEditComputerModal(computerId){
      const comp = computers.find(c => c.id === computerId);
      if(!comp) return alert('Computer not found.');

      const backdrop = document.createElement('div');
      backdrop.style = 'position: fixed; inset: 0; background: rgba(0,0,0,0.5); display: flex; align-items: center; justify-content: center; z-index: 10000;';

      const modal = document.createElement('div');
      modal.style = 'background: white; border-radius: 12px; padding: 24px; max-width: 400px; width: 90vw; box-shadow: 0 6px 18px rgb(0 0 0 / 0.25); position: relative;';
      modal.setAttribute('role', 'dialog');
      modal.setAttribute('aria-modal', 'true');
      modal.setAttribute('aria-labelledby', 'modal-title');

      const title = document.createElement('h3');
      title.id = 'modal-title';
      title.textContent = `Edit Computer: ${comp.name}`;
      title.style.marginBottom = '18px';

      const form = document.createElement('form');
      form.setAttribute('aria-label', 'Edit computer form');
      form.addEventListener('submit', e => {
        e.preventDefault();

        const newName = form.elements['name'].value.trim();
        if (!newName) return;

        if (newName.toLowerCase() !== comp.name.toLowerCase()){
          const exists = computers.some(c => c.name.toLowerCase() === newName.toLowerCase());
          if (exists){
            alert('Another computer with this name already exists.');
            return;
          }
        }

        comp.name = newName;
        saveData();
        document.body.removeChild(backdrop);
        loadComputers();
      });

      const labelName = document.createElement('label');
      labelName.setAttribute('for', 'edit-computer-name');
      labelName.textContent = 'Name *';
      const inputName = document.createElement('input');
      inputName.type = 'text';
      inputName.id = 'edit-computer-name';
      inputName.name = 'name';
      inputName.required = true;
      inputName.value = comp.name;

      const btnSave = document.createElement('button');
      btnSave.type = 'submit';
      btnSave.textContent = 'Save Changes';

      const btnCancel = document.createElement('button');
      btnCancel.type = 'button';
      btnCancel.textContent = 'Cancel';
      btnCancel.style.marginLeft = '12px';
      btnCancel.addEventListener('click', () => {
        document.body.removeChild(backdrop);
      });

      form.append(labelName, inputName, btnSave, btnCancel);
      modal.append(title, form);
      backdrop.appendChild(modal);

      document.body.appendChild(backdrop);
      inputName.focus();
    }

    /* SESSIONS - Start/Stop sessions with time logging and billing */
    function loadSessions() {
      const container = document.createElement('section');
      container.setAttribute('aria-label', 'Session management');

      const title = document.createElement('h2');
      title.className = 'section-title';
      title.textContent = 'Sessions';

      // Session form: start new session
      const startFormTitle = document.createElement('h3');
      startFormTitle.className = 'sub-title';
      startFormTitle.textContent = 'Start New Session';

      const form = document.createElement('form');
      form.setAttribute('aria-label', 'Start new session form');
      
      // Customer select
      const labelCustomer = document.createElement('label');
      labelCustomer.setAttribute('for', 'session-customer');
      labelCustomer.textContent = 'Customer *';

      const selectCustomer = document.createElement('select');
      selectCustomer.id = 'session-customer';
      selectCustomer.name = 'customer';
      selectCustomer.required = true;
      selectCustomer.style.marginBottom = '18px';

      if(customers.length === 0){
        const optionNone = document.createElement('option');
        optionNone.textContent = 'No customers registered';
        optionNone.disabled = true;
        selectCustomer.appendChild(optionNone);
        selectCustomer.disabled = true;
      } else {
        const blankOption = document.createElement('option');
        blankOption.value = '';
        blankOption.textContent = 'Select Customer';
        blankOption.hidden = true;
        blankOption.selected = true;
        selectCustomer.appendChild(blankOption);

        customers.forEach(cust => {
          const opt = document.createElement('option');
          opt.value = cust.id;
          opt.textContent = cust.name;
          selectCustomer.appendChild(opt);
        });
      }

      // Computer select
      const labelComputer = document.createElement('label');
      labelComputer.setAttribute('for', 'session-computer');
      labelComputer.textContent = 'Computer *';

      const selectComputer = document.createElement('select');
      selectComputer.id = 'session-computer';
      selectComputer.name = 'computer';
      selectComputer.required = true;
      selectComputer.style.marginBottom = '18px';

      const availableComputers = computers.filter(comp => comp.status === 'available');

      if(availableComputers.length === 0){
        const optionNone = document.createElement('option');
        optionNone.textContent = 'No available computers';
        optionNone.disabled = true;
        selectComputer.appendChild(optionNone);
        selectComputer.disabled = true;
      } else {
        const blankOption = document.createElement('option');
        blankOption.value = '';
        blankOption.textContent = 'Select Computer';
        blankOption.hidden = true;
        blankOption.selected = true;
        selectComputer.appendChild(blankOption);

        availableComputers.forEach(comp => {
          const opt = document.createElement('option');
          opt.value = comp.id;
          opt.textContent = comp.name;
          selectComputer.appendChild(opt);
        });
      }

      const btnStart = document.createElement('button');
      btnStart.type = 'submit';
      btnStart.textContent = 'Start Session';

      form.append(labelCustomer, selectCustomer, labelComputer, selectComputer, btnStart);

      form.addEventListener('submit', e => {
        e.preventDefault();

        const custId = parseInt(selectCustomer.value, 10);
        const compId = parseInt(selectComputer.value, 10);

        if(isNaN(custId) || isNaN(compId)){
          alert('Please select a customer and a computer.');
          return;
        }

        // Start session object
        sessions.push({
          id: Date.now(),
          customerId: custId,
          computerId: compId,
          startTime: Date.now(),
          endTime: null,
          billed: false
        });

        // Mark computer as in use
        const compIndex = computers.findIndex(c => c.id === compId);
        if(compIndex >= 0){
          computers[compIndex].status = 'in use';
        }
        saveData();
        loadSessions();
        alert('Session started.');
      });

      // Active sessions list
      const activeTitle = document.createElement('h3');
      activeTitle.className = 'sub-title';
      activeTitle.textContent = 'Active Sessions';

      const table = document.createElement('table');
      table.setAttribute('aria-label', 'Active sessions list');
      
      const thead = document.createElement('thead');
      thead.innerHTML = `
        <tr>
          <th>Customer</th>
          <th>Computer</th>
          <th>Start Time</th>
          <th>Duration</th>
          <th>Action</th>
        </tr>
      `;
      const tbody = document.createElement('tbody');

      // Only active sessions (not ended)
      const activeSessions = sessions.filter(s => !s.endTime);

      activeSessions.forEach(sess => {
        const tr = document.createElement('tr');

        const tdCustomer = document.createElement('td');
        const cust = customers.find(c => c.id === sess.customerId);
        tdCustomer.textContent = cust ? cust.name : 'Unknown';

        const tdComputer = document.createElement('td');
        const comp = computers.find(c => c.id === sess.computerId);
        tdComputer.textContent = comp ? comp.name : 'Unknown';

        const tdStart = document.createElement('td');
        tdStart.textContent = formatDate(sess.startTime);

        const tdDuration = document.createElement('td');
        tdDuration.className = 'session-timer';
        tdDuration.textContent = formatDuration(Date.now() - sess.startTime);
        // Animate the timer every second
        function updateTimer(){
          tdDuration.textContent = formatDuration(Date.now() - sess.startTime);
        }
        const intervalId = setInterval(() => {
          // If session ended meanwhile, clear interval
          if(sessions.find(s=>s.id===sess.id)?.endTime){
            clearInterval(intervalId);
            return;
          }
          updateTimer();
        }, 1000);

        const tdAction = document.createElement('td');
        const btnStop = document.createElement('button');
        btnStop.className = 'table-btn';
        btnStop.setAttribute('aria-label', `Stop session of ${cust ? cust.name : 'customer'}`);
        btnStop.innerHTML = '<span class="material-icons">stop</span>';
        btnStop.title = 'Stop Session';
        btnStop.addEventListener('click', () => {
          stopSession(sess.id);
        });
        tdAction.appendChild(btnStop);

        tr.append(tdCustomer, tdComputer, tdStart, tdDuration, tdAction);
        tbody.appendChild(tr);
      });

      table.append(thead, tbody);

      container.append(title, startFormTitle, form, activeTitle, table);
      mainContent.appendChild(container);
    }

    // Stop a session, calculate duration and add transaction
    function stopSession(sessionId) {
      const sessIndex = sessions.findIndex(s => s.id === sessionId);
      if (sessIndex < 0) return alert('Session not found.');
      const sess = sessions[sessIndex];
      if (sess.endTime) return alert('Session already stopped.');

      sess.endTime = Date.now();

      // Pricing: e.g. $2 per hour, billed per minute
      const ratePerHour = 2.00;
      const durationMs = sess.endTime - sess.startTime;
      const durationHours = durationMs / (1000 * 3600);
      const durationMinutesRounded = Math.ceil(durationMs / (1000 * 60));
      const cost = +(ratePerHour * (durationMinutesRounded / 60)).toFixed(2);

      // Insert transaction record
      transactions.push({
        id: Date.now(),
        customerId: sess.customerId,
        computerId: sess.computerId,
        startTime: sess.startTime,
        endTime: sess.endTime,
        cost: cost,
        durationMs: durationMs
      });

      // Mark computer available again
      const compIndex = computers.findIndex(c => c.id === sess.computerId);
      if(compIndex >= 0) {
        computers[compIndex].status = 'available';
      }

      saveData();
      alert(`Session stopped.\nDuration: ${formatDuration(durationMs)}\nCost: $${cost.toFixed(2)}`);
      loadSessions();
    }

    /* TRANSACTIONS - History with billing info */
    function loadTransactions() {
      const container = document.createElement('section');
      container.setAttribute('aria-label', 'Transactions history');

      const title = document.createElement('h2');
      title.className = 'section-title';
      title.textContent = 'Transactions';

      if(transactions.length === 0){
        const p = document.createElement('p');
        p.textContent = 'No transactions recorded yet.';
        container.append(title, p);
        mainContent.appendChild(container);
        return;
      }

      const table = document.createElement('table');
      table.setAttribute('aria-label', 'Transactions list');

      const thead = document.createElement('thead');
      thead.innerHTML = `
        <tr>
          <th>Customer</th>
          <th>Computer</th>
          <th>Start Time</th>
          <th>End Time</th>
          <th>Duration</th>
          <th>Cost ($)</th>
        </tr>
      `;

      const tbody = document.createElement('tbody');

      // Sort transactions by newest first
      const sortedTransactions = [...transactions].sort((a,b) => b.id - a.id);

      sortedTransactions.forEach(tx => {
        const tr = document.createElement('tr');

        const tdCustomer = document.createElement('td');
        const cust = customers.find(c => c.id === tx.customerId);
        tdCustomer.textContent = cust ? cust.name : 'Unknown';

        const tdComputer = document.createElement('td');
        const comp = computers.find(c => c.id === tx.computerId);
        tdComputer.textContent = comp ? comp.name : 'Unknown';

        const tdStart = document.createElement('td');
        tdStart.textContent = formatDate(tx.startTime);

        const tdEnd = document.createElement('td');
        tdEnd.textContent = formatDate(tx.endTime);

        const tdDuration = document.createElement('td');
        tdDuration.textContent = formatDuration(tx.durationMs);

        const tdCost = document.createElement('td');
        tdCost.textContent = tx.cost.toFixed(2);

        tr.append(tdCustomer, tdComputer, tdStart, tdEnd, tdDuration, tdCost);
        tbody.appendChild(tr);
      });

      table.append(thead, tbody);
      container.append(title, table);
      mainContent.appendChild(container);
    }

    // Initial load
    setActiveSection('dashboard');
  })();
</script>
</body>
</html>

```
