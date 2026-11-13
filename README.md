<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>KostKu Pro — Manajemen Kamar, Bukti Transfer, Alarm &amp; Keuangan</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet" />
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.8.2/jspdf.plugin.autotable.min.js"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: { sans: ['Inter', 'system-ui', 'sans-serif'] },
          colors: {
            brand: {
              50: '#eff6ff',
              100: '#dbeafe',
              500: '#3b82f6',
              600: '#2563eb',
              700: '#1d4ed8',
              900: '#1e3a8a'
            }
          }
        }
      }
    };
  </script>
  <style>
    :root {
      --theme-from: #0ea5e9;
      --theme-via: #2563eb;
      --theme-to: #10b981;
      --theme-accent: #2563eb;
      --theme-soft: #eff6ff;
    }
    [data-theme="emerald"] {
      --theme-from: #059669;
      --theme-via: #0d9488;
      --theme-to: #2563eb;
      --theme-accent: #059669;
      --theme-soft: #ecfdf5;
    }
    [data-theme="indigo"] {
      --theme-from: #4f46e5;
      --theme-via: #7c3aed;
      --theme-to: #ec4899;
      --theme-accent: #4f46e5;
      --theme-soft: #eef2ff;
    }
    [data-theme="sunset"] {
      --theme-from: #ea580c;
      --theme-via: #e11d48;
      --theme-to: #7c3aed;
      --theme-accent: #e11d48;
      --theme-soft: #fff1f2;
    }
    body {
      font-family: 'Inter', system-ui, sans-serif;
      -webkit-tap-highlight-color: transparent;
      background: linear-gradient(135deg, #0f172a 0%, #1e293b 50%, #0f172a 100%);
    }
    .theme-gradient {
      background: linear-gradient(135deg, var(--theme-from) 0%, var(--theme-via) 55%, var(--theme-to) 100%);
    }
    .theme-btn {
      background: linear-gradient(135deg, var(--theme-from) 0%, var(--theme-via) 100%);
    }
    .theme-text { color: var(--theme-accent); }
    .theme-bg-soft { background-color: var(--theme-soft); }

    /* Phone Shell Mode vs Fullscreen Desktop Mode */
    body.mode-hp #appShell {
      max-width: 440px;
      margin: 0 auto;
      min-height: 100vh;
      background: #f8fafc;
      box-shadow: 0 25px 60px -15px rgba(0, 0, 0, 0.65), 0 0 0 8px rgba(255, 255, 255, 0.08);
    }
    @media (min-width: 640px) {
      body.mode-hp {
        padding: 1.25rem 0;
      }
      body.mode-hp #appShell {
        min-height: calc(100vh - 2.5rem);
        border-radius: 2.25rem;
        overflow-x: hidden;
      }
    }
    body.mode-wide {
      background: #f1f5f9;
    }
    body.mode-wide #appShell {
      max-width: 1200px;
      margin: 0 auto;
      min-height: 100vh;
      background: #f8fafc;
    }

    @keyframes pulseRing {
      0%, 100% { transform: scale(1); box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.45); }
      50% { transform: scale(1.015); box-shadow: 0 0 0 8px rgba(239, 68, 68, 0); }
    }
    .alarm-pulse { animation: pulseRing 1.8s infinite; }
    .no-scrollbar::-webkit-scrollbar { display: none; }
    .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
    @media print {
      body { background: #ffffff !important; padding: 0 !important; }
      #appShell, #modalTenant, #modalPaymentProof, #modalViewReceipt, #modalCheckout, #modalSettings, #toastBox { display: none !important; }
      body.print-finance-mode #modalFinanceReport {
        display: block !important;
        position: static !important;
        background: #ffffff !important;
        padding: 0 !important;
      }
      body.print-finance-mode #modalFinanceReport > div {
        max-width: 100% !important;
        max-height: none !important;
        box-shadow: none !important;
        border-radius: 0 !important;
      }
      body.print-stmt-mode #modalRoomStatement {
        display: block !important;
        position: static !important;
        background: #ffffff !important;
        padding: 0 !important;
      }
      body.print-stmt-mode #modalRoomStatement > div {
        max-width: 100% !important;
        max-height: none !important;
        box-shadow: none !important;
        border-radius: 0 !important;
      }
      .no-print { display: none !important; }
    }
  </style>
</head>
<body class="mode-hp text-slate-800" data-theme="ocean">

  <!-- Main Mobile/App Container -->
  <div id="appShell" class="relative flex flex-col justify-between pb-24">

    <!-- Phone Status Bar Decoration (Visible in HP Mode) -->
    <div id="phoneTopNotch" class="theme-gradient text-white/90 px-5 pt-2 pb-1 flex items-center justify-between text-[11px] font-semibold select-none">
      <span id="liveClockNotch">09:25</span>
      <div class="flex items-center gap-2">
        <button id="btnToggleViewMode" type="button" class="px-2 py-0.5 rounded-full bg-white/20 hover:bg-white/30 text-[10px] font-bold text-white transition" title="Ubah Tampilan HP / Layar Lebar">
          📱 Mode HP
        </button>
        <button id="btnCloudIndicator" type="button" class="px-2 py-0.5 rounded-full bg-white/15 hover:bg-white/25 text-[10px] font-bold text-white flex items-center gap-1 transition" title="Status Sinkronisasi Google Sheets Cloud">
          <span id="cloudDot" class="w-1.5 h-1.5 rounded-full bg-amber-300"></span>
          <span id="cloudStatusText">Lokal</span>
        </button>
      </div>
    </div>

    <!-- Top App Header with Editable Logo & Kost Name -->
    <header class="sticky top-0 z-30 theme-gradient text-white shadow-md">
      <div class="px-4 py-3.5 flex items-center justify-between gap-2">
        <div class="flex items-center gap-3 min-w-0">
          <!-- Clickable Logo -->
          <button id="btnEditBranding" type="button" title="Klik untuk ganti Logo & Nama Kost" class="relative group w-12 h-12 rounded-2xl bg-white/20 backdrop-blur border border-white/30 flex items-center justify-center text-white shadow-inner shrink-0 overflow-hidden">
            <img id="kostLogoImg" src="" alt="Logo Kost" class="hidden w-full h-full object-cover" />
            <div id="kostLogoDefault" class="flex items-center justify-center">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" d="M3 10.5L12 3l9 7.5V20a1 1 0 01-1 1h-5v-6H9v6H4a1 1 0 01-1-1v-9.5z" />
              </svg>
            </div>
            <span class="absolute inset-0 bg-black/40 opacity-0 group-hover:opacity-100 flex items-center justify-center text-[9px] font-bold transition">EDIT</span>
          </button>

          <div class="min-w-0">
            <div class="flex items-center gap-1.5">
              <h1 id="kostTitleDisplay" class="text-base font-extrabold tracking-tight truncate">KostKu Pro</h1>
              <button id="btnQuickEditBrand" type="button" title="Ganti Nama Kost, Logo & Warna" class="p-1 rounded-lg bg-white/15 hover:bg-white/25 text-white shrink-0">
                <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="2.2" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M15.232 5.232l3.536 3.536m-2.036-5.036a2.5 2.5 0 113.536 3.536L6.5 21.036H3v-3.572L16.732 3.732z" />
                </svg>
              </button>
            </div>
            <p id="kostSubtitleDisplay" class="text-[11px] text-white/85 truncate">Manajemen Kamar, Bukti Transfer &amp; Keuangan</p>
          </div>
        </div>

        <!-- Quick Header Actions -->
        <div class="flex items-center gap-1.5 shrink-0">
          <button id="btnAlarmSoundHeader" type="button" title="Tes / Status Alarm" class="relative p-2.5 rounded-xl bg-white/15 hover:bg-white/25 text-white transition min-h-[40px] min-w-[40px] flex items-center justify-center">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9" />
            </svg>
            <span id="headerBellDot" class="hidden absolute top-1.5 right-1.5 w-2.5 h-2.5 rounded-full bg-rose-400 ring-2 ring-white"></span>
          </button>
          <button id="btnSettingsModal" type="button" title="Pengaturan, Tema & Backup" class="px-2.5 py-2 rounded-xl bg-white/15 hover:bg-white/25 text-white text-[11px] font-bold transition min-h-[40px] flex items-center gap-1"><span>Data &amp; HP</span>
            <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" />
              <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
            </svg>
          </button>
        </div>
      </div>

      <!-- Desktop/Tablet Pill Navigation (kept for compatibility & quick access) -->
      <nav class="hidden px-4 pb-2.5 items-center gap-1 overflow-x-auto no-scrollbar" id="desktopNav">
        <button data-tab="kamar" class="nav-btn px-3 py-1.5 rounded-lg text-xs font-semibold transition bg-white text-blue-600 shadow-sm">Kamar &amp; Penghuni</button>
        <button data-tab="alarm" class="nav-btn px-3 py-1.5 rounded-lg text-xs font-semibold transition text-white/90 hover:bg-white/10 flex items-center gap-1">
          Alarm Tempo
          <span id="alarmBadgeDesktop" class="hidden px-1.5 py-0.2 text-[10px] font-bold rounded-full bg-rose-500 text-white">0</span>
        </button>
        <button data-tab="keuangan" class="nav-btn px-3 py-1.5 rounded-lg text-xs font-semibold transition text-white/90 hover:bg-white/10">Keuangan</button>
        <button data-tab="riwayat" class="nav-btn px-3 py-1.5 rounded-lg text-xs font-semibold transition text-white/90 hover:bg-white/10">Riwayat Masuk/Keluar</button>
      </nav>
    </header>

    <!-- Active Alarm Banner -->
    <div id="urgentAlarmBanner" class="hidden bg-gradient-to-r from-rose-600 to-orange-500 text-white shadow-inner">
      <div class="px-4 py-2.5 flex items-center justify-between gap-2">
        <div class="flex items-center gap-2.5 min-w-0">
          <span class="p-1.5 rounded-lg bg-white/20 shrink-0">
            <svg class="w-4 h-4 animate-bounce" fill="none" stroke="currentColor" stroke-width="2.2" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
          </span>
          <div class="min-w-0">
            <p class="text-xs font-bold truncate" id="urgentBannerTitle">Ada kamar jatuh tempo!</p>
            <p class="text-[11px] text-rose-100 truncate" id="urgentBannerSub">Segera cek alarm kamar.</p>
          </div>
        </div>
        <div class="flex items-center gap-1.5 shrink-0">
          <button id="btnBannerPlayAlarm" type="button" class="px-2.5 py-1.5 rounded-lg bg-white text-rose-700 text-[11px] font-bold hover:bg-rose-50 transition">Bunyikan Alarm</button>
          <button id="btnBannerViewAlarms" type="button" class="px-2.5 py-1.5 rounded-lg bg-rose-900/60 text-white text-[11px] font-semibold hover:bg-rose-900 transition">Lihat Alarm</button>
        </div>
      </div>
    </div>

    <!-- Main Content Area -->
    <main class="flex-1 px-3.5 py-4 space-y-4">

      <!-- Period Selector & Quick Add Room Card -->
      <section class="bg-white p-3.5 rounded-2xl border border-slate-200/80 shadow-sm flex items-center justify-between gap-2">
        <div class="min-w-0">
          <span class="text-[10px] font-bold uppercase tracking-wider text-slate-400 block">Periode Aktif</span>
          <h2 class="text-sm font-extrabold text-slate-900 truncate" id="activePeriodLabel">November 2026</h2>
        </div>
        <div class="flex items-center gap-1.5 shrink-0">
          <input id="monthPicker" type="month" aria-label="Pilih Bulan" class="px-2.5 py-1.5 rounded-xl border border-slate-200 text-xs font-semibold text-slate-800 bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500 min-h-[38px]" />
          <button id="btnAddRoomTop" type="button" class="px-3 py-1.5 rounded-xl theme-btn text-white text-xs font-bold shadow-sm transition min-h-[38px] flex items-center gap-1">
            <span>+ Tambah Kamar</span>
          </button>
        </div>
      </section>

      <!-- TAB 1: KAMAR & PENGHUNI -->
      <section id="tab-kamar" class="tab-panel space-y-4">
        <!-- Colorful KPI Grid -->
        <div class="grid grid-cols-2 gap-2.5" id="roomKpiGrid"></div>

        <!-- Search & Filter Pills -->
        <div class="space-y-2.5">
          <div class="relative">
            <input id="roomSearchInput" type="text" placeholder="Cari nomor kamar / nama penghuni..." class="w-full pl-9 pr-3 py-2.5 rounded-2xl border border-slate-200 bg-white text-xs font-medium shadow-2xs focus:outline-none focus:ring-2 focus:ring-blue-500 min-h-[40px]" />
            <svg class="w-4 h-4 text-slate-400 absolute left-3 top-3" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-4.35-4.35m0 0A7.5 7.5 0 1010.5 18a7.5 7.5 0 006.15-3.35z" />
            </svg>
          </div>
          <div class="flex items-center gap-1.5 overflow-x-auto no-scrollbar pb-0.5">
            <button data-room-filter="all" class="room-filter-btn px-3 py-1.5 rounded-xl text-[11px] font-bold bg-slate-900 text-white whitespace-nowrap">Semua Kamar</button>
            <button data-room-filter="terisi" class="room-filter-btn px-3 py-1.5 rounded-xl text-[11px] font-semibold bg-white border border-slate-200 text-slate-700 whitespace-nowrap">Terisi</button>
            <button data-room-filter="kosong" class="room-filter-btn px-3 py-1.5 rounded-xl text-[11px] font-semibold bg-white border border-slate-200 text-slate-700 whitespace-nowrap">Kosong</button>
            <button data-room-filter="belum_bayar" class="room-filter-btn px-3 py-1.5 rounded-xl text-[11px] font-semibold bg-white border border-rose-200 text-rose-600 whitespace-nowrap">Belum Bayar Bulan Ini</button>
          </div>
        </div>

        <!-- Room Cards Container -->
        <div id="roomsGrid" class="grid grid-cols-1 gap-3.5"></div>
      </section>

      <!-- TAB 2: ALARM JATUH TEMPO -->
      <section id="tab-alarm" class="tab-panel hidden space-y-4">
        <div class="bg-gradient-to-br from-rose-500 to-orange-500 text-white p-4 rounded-2xl shadow-md space-y-3">
          <div>
            <h3 class="text-sm font-extrabold">Alarm Jatuh Tempo Per Kamar</h3>
            <p class="text-xs text-white/90 mt-0.5">Mendeteksi otomatis kamar terlambat, jatuh tempo hari ini, atau H-3.</p>
          </div>
          <div class="flex flex-wrap items-center gap-2">
            <button id="btnRequestNotif" type="button" class="px-3 py-2 rounded-xl bg-white/20 hover:bg-white/30 backdrop-blur text-white text-xs font-bold transition min-h-[38px]">Aktifkan Notifikasi HP</button>
            <button id="btnTestAlarm" type="button" class="px-3 py-2 rounded-xl bg-white text-rose-600 text-xs font-extrabold hover:bg-rose-50 transition min-h-[38px]">Bunyikan Tes Alarm</button>
            <button id="btnStopAlarm" type="button" class="hidden px-3 py-2 rounded-xl bg-slate-900 text-white text-xs font-bold transition min-h-[38px]">Matikan Suara Alarm</button>
          </div>
        </div>

        <div id="alarmListContainer" class="space-y-3"></div>
      </section>

      <!-- TAB 3: KEUANGAN BULANAN -->
      <section id="tab-keuangan" class="tab-panel hidden space-y-4">
        <div class="grid grid-cols-1 gap-2.5" id="financeKpiGrid"></div>
        <div class="bg-gradient-to-r from-slate-900 to-slate-800 text-white p-3.5 rounded-2xl shadow-sm flex items-center justify-between gap-2">
          <div>
            <p class="text-xs font-extrabold">📄 Laporan Rincian Keuangan (Siap Print / PDF)</p>
            <p class="text-[10px] text-slate-300">Rekap pembayaran per kamar (lengkap No. HP) &amp; rincian kas bulanan</p>
          </div>
          <button id="btnOpenFinancePdfTop" type="button" class="px-3 py-2 rounded-xl bg-rose-500 hover:bg-rose-600 text-white text-xs font-extrabold shrink-0 shadow-xs">🖨️ Prind / PDF</button>
        </div>

        <!-- Chart Card -->
        <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
          <div class="mb-2">
            <h3 class="text-sm font-bold text-slate-900">Grafik Pemasukan vs Pengeluaran</h3>
            <p class="text-[11px] text-slate-500">Tren 6 bulan terakhir</p>
          </div>
          <div class="relative h-56">
            <canvas id="financeChart"></canvas>
          </div>
        </div>

        <!-- Add Transaction Card -->
        <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm space-y-3">
          <div>
            <h3 class="text-sm font-bold text-slate-900">Catat Kas Masuk / Pengeluaran</h3>
            <p class="text-[11px] text-slate-500">Catat biaya listrik, air, WiFi, perbaikan, atau pemasukan lain.</p>
          </div>
          <form id="quickTxForm" class="space-y-2.5">
            <div class="grid grid-cols-2 gap-2">
              <button type="button" data-tx-type="pemasukan" class="tx-type-btn py-2 rounded-xl text-xs font-bold border border-emerald-500 bg-emerald-50 text-emerald-700 min-h-[40px]">+ Pemasukan</button>
              <button type="button" data-tx-type="pengeluaran" class="tx-type-btn py-2 rounded-xl text-xs font-bold border border-slate-200 bg-white text-slate-600 min-h-[40px]">- Pengeluaran</button>
            </div>
            <input type="hidden" id="txTypeInput" value="pemasukan" />
            <div class="grid grid-cols-2 gap-2">
              <div>
                <label class="block text-[11px] font-semibold text-slate-600 mb-1">Tanggal</label>
                <input id="txDateInput" type="date" required class="w-full px-2.5 py-2 rounded-xl border border-slate-300 text-xs min-h-[38px]" />
              </div>
              <div>
                <label class="block text-[11px] font-semibold text-slate-600 mb-1">Kategori</label>
                <select id="txCategoryInput" class="w-full px-2.5 py-2 rounded-xl border border-slate-300 text-xs bg-white min-h-[38px]">
                  <option value="Sewa Kamar">Sewa Kamar</option>
                  <option value="Deposit / Tambahan">Deposit / Tambahan</option>
                  <option value="Listrik & Token">Listrik &amp; Token</option>
                  <option value="Air PDAM">Air PDAM</option>
                  <option value="WiFi / Internet">WiFi / Internet</option>
                  <option value="Kebersihan & Sampah">Kebersihan &amp; Sampah</option>
                  <option value="Perbaikan / Maintenance">Perbaikan / Maintenance</option>
                  <option value="Lainnya">Lainnya</option>
                </select>
              </div>
            </div>
            <div>
              <label class="block text-[11px] font-semibold text-slate-600 mb-1">Nominal (Rp)</label>
              <input id="txAmountInput" type="number" min="1000" step="1000" placeholder="Contoh: 250000" required class="w-full px-3 py-2 rounded-xl border border-slate-300 text-xs min-h-[38px]" />
            </div>
            <div>
              <label class="block text-[11px] font-semibold text-slate-600 mb-1">Keterangan</label>
              <input id="txDescInput" type="text" placeholder="Contoh: Token listrik lantai 1" required class="w-full px-3 py-2 rounded-xl border border-slate-300 text-xs min-h-[38px]" />
            </div>
            <button type="submit" class="w-full py-2.5 rounded-xl theme-btn text-white text-xs font-bold shadow-sm transition min-h-[42px]">Simpan Transaksi</button>
          </form>
        </div>

        <!-- Transactions List -->
        <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
          <div class="p-3.5 border-b border-slate-200 flex items-center justify-between gap-2">
            <h3 class="text-xs font-bold text-slate-900">Rincian Transaksi Bulan Ini</h3>
            <div class="flex items-center gap-1.5">
              <select id="txFilterType" class="px-2.5 py-1.5 rounded-xl border border-slate-200 text-[11px] font-semibold bg-slate-50">
                <option value="all">Semua</option>
                <option value="pemasukan">Masuk</option>
                <option value="pengeluaran">Keluar</option>
              </select>
              <button id="btnOpenFinancePdf" type="button" class="px-2.5 py-1.5 rounded-xl bg-rose-600 hover:bg-rose-700 text-white text-[11px] font-bold shadow-2xs flex items-center gap-1">🖨️ Cetak / PDF</button>
              <button id="btnExportCsv" type="button" class="px-2.5 py-1.5 rounded-xl border border-slate-200 bg-white hover:bg-slate-50 text-[11px] font-bold text-slate-700">Ekspor CSV</button>
            </div>
          </div>
          <div class="overflow-x-auto">
            <table class="w-full text-left border-collapse">
              <thead class="bg-slate-50 text-[10px] font-bold text-slate-500 uppercase">
                <tr>
                  <th class="px-3 py-2.5">Tgl</th>
                  <th class="px-3 py-2.5">Rincian</th>
                  <th class="px-3 py-2.5 text-right">Nominal</th>
                  <th class="px-3 py-2.5 text-center">Aksi</th>
                </tr>
              </thead>
              <tbody id="txTableBody" class="divide-y divide-slate-100 text-xs"></tbody>
            </table>
          </div>
        </div>
      </section>

      <!-- TAB 4: RIWAYAT MASUK & KELUAR -->
      <section id="tab-riwayat" class="tab-panel hidden space-y-4">
        <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm space-y-2.5">
          <div>
            <h3 class="text-sm font-bold text-slate-900">Riwayat Masuk &amp; Keluar Per Kamar</h3>
            <p class="text-[11px] text-slate-500">Klik tombol <strong>Rincian</strong> pada kamar/penghuni untuk melihat histori lengkap dari awal masuk sampai keluar kos beserta bukti transfernya.</p>
          </div>
          <input id="historySearchInput" type="text" placeholder="Cari nama penghuni atau nomor kamar..." class="w-full px-3 py-2 rounded-xl border border-slate-200 text-xs min-h-[38px]" />
        </div>
        <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
          <div class="overflow-x-auto">
            <table class="w-full text-left border-collapse">
              <thead class="bg-slate-50 text-[10px] font-bold text-slate-500 uppercase">
                <tr>
                  <th class="px-3 py-2.5">Kamar &amp; Nama</th>
                  <th class="px-3 py-2.5">Masuk - Keluar</th>
                  <th class="px-3 py-2.5">Status</th>
                  <th class="px-3 py-2.5 text-center">Rincian</th>
                </tr>
              </thead>
              <tbody id="historyTableBody" class="divide-y divide-slate-100 text-xs"></tbody>
            </table>
          </div>
        </div>
      </section>

    </main>

    <!-- Bottom Navigation Bar (Always Visible in HP Shell) -->
    <nav class="fixed bottom-0 left-1/2 -translate-x-1/2 w-full max-w-[440px] z-30 bg-white/95 backdrop-blur border-t border-slate-200 flex items-center justify-around px-1 py-1.5 shadow-lg" id="bottomNavBar">
      <button data-tab="kamar" class="mobile-nav-btn flex-1 py-1.5 flex flex-col items-center gap-0.5 text-blue-600 font-bold text-[11px]">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M3 10.5L12 3l9 7.5V20a1 1 0 01-1 1h-5v-6H9v6H4a1 1 0 01-1-1v-9.5z" /></svg>
        <span>Kamar</span>
      </button>
      <button data-tab="alarm" class="mobile-nav-btn relative flex-1 py-1.5 flex flex-col items-center gap-0.5 text-slate-500 font-medium text-[11px]">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9" /></svg>
        <span>Alarm</span>
        <span id="alarmBadgeMobile" class="hidden absolute top-0.5 right-5 px-1.5 py-0.2 text-[10px] font-bold rounded-full bg-rose-500 text-white">0</span>
      </button>
      <button data-tab="keuangan" class="mobile-nav-btn flex-1 py-1.5 flex flex-col items-center gap-0.5 text-slate-500 font-medium text-[11px]">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>
        <span>Keuangan</span>
      </button>
      <button data-tab="riwayat" class="mobile-nav-btn flex-1 py-1.5 flex flex-col items-center gap-0.5 text-slate-500 font-medium text-[11px]">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" /></svg>
        <span>Riwayat</span>
      </button>
    </nav>
  </div>

  <!-- MODAL 1: CHECK-IN / EDIT PENGHUNI KAMAR -->
  <div id="modalTenant" class="hidden fixed inset-0 z-50 bg-slate-900/60 backdrop-blur-xs flex items-end sm:items-center justify-center p-0 sm:p-4">
    <div class="bg-white w-full sm:max-w-md rounded-t-3xl sm:rounded-2xl shadow-xl overflow-hidden max-h-[92vh] flex flex-col">
      <div class="px-5 py-3.5 theme-gradient text-white flex items-center justify-between">
        <h3 class="text-sm font-bold" id="modalTenantTitle">Input Penghuni Kamar</h3>
        <button type="button" data-close-modal="modalTenant" class="p-1.5 rounded-lg bg-white/15 hover:bg-white/25 text-white">✕</button>
      </div>
      <form id="tenantForm" class="p-4 space-y-3 overflow-y-auto text-xs">
        <input type="hidden" id="tfRoomId" />
        <div class="grid grid-cols-2 gap-2.5">
          <div>
            <label class="block font-semibold text-slate-600 mb-1">Nomor / Nama Kamar</label>
            <input id="tfRoomName" type="text" required class="w-full px-3 py-2 rounded-xl border border-slate-300 min-h-[38px]" />
          </div>
          <div>
            <label class="block font-semibold text-slate-600 mb-1">Harga Sewa / Bulan (Rp)</label>
            <input id="tfRentPrice" type="number" min="0" step="10000" required class="w-full px-3 py-2 rounded-xl border border-slate-300 min-h-[38px]" />
          </div>
        </div>
        <div>
          <label class="block font-semibold text-slate-600 mb-1">Nama Penghuni</label>
          <input id="tfTenantName" type="text" placeholder="Kosongkan jika kamar belum terisi" class="w-full px-3 py-2 rounded-xl border border-slate-300 min-h-[38px]" />
        </div>
        <div class="grid grid-cols-2 gap-2.5">
          <div>
            <label class="block font-semibold text-slate-600 mb-1">No. HP / WhatsApp</label>
            <input id="tfTenantPhone" type="tel" placeholder="0812xxxxxxxx" class="w-full px-3 py-2 rounded-xl border border-slate-300 min-h-[38px]" />
          </div>
          <div>
            <label class="block font-semibold text-slate-600 mb-1">Tgl Jatuh Tempo (1–31)</label>
            <input id="tfDueDay" type="number" min="1" max="31" placeholder="Otomatis" class="w-full px-3 py-2 rounded-xl border border-slate-300 min-h-[38px]" />
          </div>
        </div>
        <div class="grid grid-cols-2 gap-2.5">
          <div>
            <label class="block font-semibold text-slate-600 mb-1">Tanggal Masuk Kos</label>
            <input id="tfCheckIn" type="date" class="w-full px-3 py-2 rounded-xl border border-slate-300 min-h-[38px]" />
          </div>
          <div>
            <label class="block font-semibold text-slate-600 mb-1">Tanggal Keluar (Opsional)</label>
            <input id="tfCheckOut" type="date" class="w-full px-3 py-2 rounded-xl border border-slate-300 min-h-[38px]" />
          </div>
        </div>
        <div class="flex items-center gap-2 pt-1">
          <input id="tfAlarmEnabled" type="checkbox" checked class="w-4 h-4 rounded text-blue-600" />
          <label for="tfAlarmEnabled" class="font-medium text-slate-700">Aktifkan alarm pengingat jatuh tempo kamar ini</label>
        </div>
        <div id="tfPayNowWrap" class="flex items-center gap-2 bg-emerald-50 p-2.5 rounded-xl border border-emerald-200">
          <input id="tfPayFirstMonth" type="checkbox" class="w-4 h-4 rounded text-emerald-600" />
          <label for="tfPayFirstMonth" class="font-semibold text-emerald-800">Sekaligus catat lunas bulan ini ke Keuangan</label>
        </div>
        <div class="flex items-center justify-end gap-2 pt-2">
          <button type="button" data-close-modal="modalTenant" class="px-4 py-2 rounded-xl border border-slate-200 font-semibold text-slate-600 min-h-[40px]">Batal</button>
          <button type="submit" class="px-4 py-2 rounded-xl theme-btn text-white font-bold min-h-[40px]">Simpan Data Kamar</button>
        </div>
      </form>
    </div>
  </div>

  <!-- MODAL 2: BAYAR SEWA & UPLOAD BUKTI TRANSFER -->
  <div id="modalPaymentProof" class="hidden fixed inset-0 z-50 bg-slate-900/60 backdrop-blur-xs flex items-end sm:items-center justify-center p-0 sm:p-4">
    <div class="bg-white w-full sm:max-w-md rounded-t-3xl sm:rounded-2xl shadow-xl overflow-hidden max-h-[92vh] flex flex-col">
      <div class="px-5 py-3.5 bg-gradient-to-r from-emerald-600 to-teal-500 text-white flex items-center justify-between">
        <h3 class="text-sm font-bold" id="modalPayTitle">Pembayaran &amp; Bukti Transfer</h3>
        <button type="button" data-close-modal="modalPaymentProof" class="p-1.5 rounded-lg bg-white/15 hover:bg-white/25 text-white">✕</button>
      </div>
      <form id="paymentProofForm" class="p-4 space-y-3 overflow-y-auto text-xs">
        <input type="hidden" id="ppRoomId" />
        <div class="bg-emerald-50 border border-emerald-200 rounded-xl p-3 flex items-center justify-between">
          <div>
            <p class="font-bold text-emerald-950" id="ppRoomLabel">Kamar 01 — Andi</p>
            <p class="text-[11px] text-emerald-700" id="ppMonthLabel">Periode: November 2026</p>
          </div>
          <p class="text-sm font-extrabold text-emerald-700" id="ppAmountLabel">Rp 1.200.000</p>
        </div>

        <div class="grid grid-cols-2 gap-2.5">
          <div>
            <label class="block font-semibold text-slate-600 mb-1">Tanggal Bayar / Transfer</label>
            <input id="ppDateInput" type="date" required class="w-full px-3 py-2 rounded-xl border border-slate-300 min-h-[38px]" />
          </div>
          <div>
            <label class="block font-semibold text-slate-600 mb-1">Metode Pembayaran</label>
            <select id="ppMethodInput" class="w-full px-3 py-2 rounded-xl border border-slate-300 bg-white min-h-[38px]">
              <option value="Transfer Bank">Transfer Bank</option>
              <option value="QRIS / E-Wallet">QRIS / E-Wallet</option>
              <option value="Tunai / Cash">Tunai / Cash</option>
            </select>
          </div>
        </div>

        <div>
          <label class="block font-semibold text-slate-600 mb-1">Catatan / Nama Pengirim Rekening</label>
          <input id="ppNoteInput" type="text" placeholder="Contoh: Transfer BCA a.n Andi Pratama" class="w-full px-3 py-2 rounded-xl border border-slate-300 min-h-[38px]" />
        </div>

        <div>
          <label class="block font-semibold text-slate-600 mb-1">Upload Foto Bukti Transfer (Opsional)</label>
          <label class="flex flex-col items-center justify-center border-2 border-dashed border-slate-300 hover:border-emerald-500 rounded-2xl p-3 cursor-pointer bg-slate-50 transition">
            <img id="ppPreviewImg" src="" alt="Preview Bukti" class="hidden max-h-40 rounded-xl object-contain mb-2 shadow-xs" />
            <span id="ppUploadPlaceholder" class="text-[11px] font-semibold text-slate-500 text-center">📷 Klik untuk pilih foto struk / screenshot bukti transfer dari HP/Laptop</span>
            <input id="ppFileInput" type="file" accept="image/*" class="hidden" />
          </label>
        </div>

        <div class="flex items-center justify-end gap-2 pt-2">
          <button type="button" data-close-modal="modalPaymentProof" class="px-4 py-2 rounded-xl border border-slate-200 font-semibold text-slate-600 min-h-[40px]">Batal</button>
          <button type="submit" class="px-4 py-2 rounded-xl bg-emerald-600 hover:bg-emerald-700 text-white font-bold min-h-[40px]">Simpan Lunas &amp; Bukti</button>
        </div>
      </form>
    </div>
  </div>

  <!-- MODAL 3: RINCIAN KAMAR DARI AWAL MASUK SAMPAI KELUAR + BUKTI TRANSFER -->
  <div id="modalRoomStatement" class="hidden fixed inset-0 z-50 bg-slate-900/60 backdrop-blur-xs flex items-end sm:items-center justify-center p-0 sm:p-4">
    <div class="bg-white w-full sm:max-w-lg rounded-t-3xl sm:rounded-2xl shadow-xl overflow-hidden max-h-[92vh] flex flex-col">
      <div class="px-5 py-3.5 theme-gradient text-white flex items-center justify-between">
        <div>
          <h3 class="text-sm font-bold" id="stmtTitle">Rincian Lengkap Kamar</h3>
          <p class="text-[11px] text-white/85" id="stmtSubtitle">Histori dari awal masuk kos sampai keluar &amp; bukti transfer</p>
        </div>
        <button type="button" data-close-modal="modalRoomStatement" class="p-1.5 rounded-lg bg-white/15 hover:bg-white/25 text-white">✕</button>
      </div>

      <div id="stmtBody" class="p-4 space-y-4 overflow-y-auto text-xs flex-1"></div>

      <div class="px-4 py-3 border-t border-slate-200 bg-slate-50 flex items-center justify-between gap-2">
        <button id="btnPrintStatement" type="button" class="px-3.5 py-2 rounded-xl bg-slate-900 hover:bg-slate-800 text-white text-xs font-bold min-h-[38px]">🖨️ Cetak / Simpan Kartu Rincian</button>
        <button type="button" data-close-modal="modalRoomStatement" class="px-4 py-2 rounded-xl border border-slate-300 bg-white text-xs font-semibold text-slate-700 min-h-[38px]">Tutup</button>
      </div>
    </div>
  </div>

  <!-- MODAL 4: PREVIEW FOTO BUKTI TRANSFER PENUH -->
  <div id="modalViewReceipt" class="hidden fixed inset-0 z-50 bg-slate-900/80 backdrop-blur-xs flex items-center justify-center p-4">
    <div class="bg-white w-full max-w-sm rounded-2xl shadow-2xl overflow-hidden">
      <div class="px-4 py-3 border-b border-slate-200 flex items-center justify-between">
        <h4 class="text-xs font-bold text-slate-900" id="vrTitle">Bukti Transfer</h4>
        <button type="button" data-close-modal="modalViewReceipt" class="p-1 rounded-lg hover:bg-slate-100 text-slate-500">✕</button>
      </div>
      <div class="p-4 space-y-3 text-xs">
        <img id="vrImage" src="" alt="Bukti Transfer" class="w-full max-h-80 object-contain rounded-xl bg-slate-100 border border-slate-200" />
        <div id="vrMeta" class="bg-slate-50 p-3 rounded-xl border border-slate-200 space-y-1"></div>
      </div>
    </div>
  </div>

  <!-- MODAL 5: CHECK-OUT PENGHUNI -->
  <div id="modalCheckout" class="hidden fixed inset-0 z-50 bg-slate-900/60 backdrop-blur-xs flex items-end sm:items-center justify-center p-0 sm:p-4">
    <div class="bg-white w-full sm:max-w-md rounded-t-3xl sm:rounded-2xl shadow-xl overflow-hidden">
      <div class="px-5 py-3.5 bg-rose-600 text-white flex items-center justify-between">
        <h3 class="text-sm font-bold">Proses Keluar Penghuni (Check-Out)</h3>
        <button type="button" data-close-modal="modalCheckout" class="p-1.5 rounded-lg bg-white/15 text-white">✕</button>
      </div>
      <form id="checkoutForm" class="p-4 space-y-3.5 text-xs">
        <input type="hidden" id="coRoomId" />
        <p class="text-slate-600" id="coSummaryText"></p>
        <div>
          <label class="block font-semibold text-slate-600 mb-1">Tanggal Keluar Aktual</label>
          <input id="coDateInput" type="date" required class="w-full px-3 py-2 rounded-xl border border-slate-300 min-h-[38px]" />
        </div>
        <div class="flex items-center justify-end gap-2 pt-2">
          <button type="button" data-close-modal="modalCheckout" class="px-4 py-2 rounded-xl border border-slate-200 font-semibold text-slate-600 min-h-[40px]">Batal</button>
          <button type="submit" class="px-4 py-2 rounded-xl bg-rose-600 hover:bg-rose-700 text-white font-bold min-h-[40px]">Konfirmasi Keluar</button>
        </div>
      </form>
    </div>
  </div>

  <!-- MODAL 6: BRANDING (GANTI NAMA KOST, LOGO, TEMA WARNA) & BACKUP DATA -->
  <div id="modalSettings" class="hidden fixed inset-0 z-50 bg-slate-900/60 backdrop-blur-xs flex items-end sm:items-center justify-center p-0 sm:p-4">
    <div class="bg-white w-full sm:max-w-md rounded-t-3xl sm:rounded-2xl shadow-xl overflow-hidden max-h-[92vh] flex flex-col">
      <div class="px-5 py-3.5 theme-gradient text-white flex items-center justify-between">
        <h3 class="text-sm font-bold">Identitas Kost, Tema Warna &amp; Backup</h3>
        <button type="button" data-close-modal="modalSettings" class="p-1.5 rounded-lg bg-white/15 hover:bg-white/25 text-white">✕</button>
      </div>
      <div class="p-4 space-y-4 overflow-y-auto text-xs text-slate-700">

        <!-- Custom Kost Name & Logo -->
        <div class="bg-slate-50 border border-slate-200 rounded-2xl p-3.5 space-y-3">
          <p class="font-extrabold text-slate-900">🏠 Ganti Nama Kost &amp; Logo</p>
          <div>
            <label class="block font-semibold text-slate-600 mb-1">Nama Kost</label>
            <input id="brandNameInput" type="text" placeholder="Contoh: Kost Griya Harmoni" class="w-full px-3 py-2 rounded-xl border border-slate-300 bg-white min-h-[38px]" />
          </div>
          <div>
            <label class="block font-semibold text-slate-600 mb-1">Sub-Judul / Alamat Singkat</label>
            <input id="brandSubInput" type="text" placeholder="Contoh: Jl. Sam Ratulangi No. 12, Manado" class="w-full px-3 py-2 rounded-xl border border-slate-300 bg-white min-h-[38px]" />
          </div>
          <div class="flex items-center gap-3">
            <label class="flex-1 px-3 py-2 rounded-xl border border-blue-200 bg-blue-50 text-blue-700 font-bold text-center cursor-pointer hover:bg-blue-100 transition">
              🖼️ Upload Logo Kost
              <input id="brandLogoInput" type="file" accept="image/*" class="hidden" />
            </label>
            <button id="btnRemoveLogo" type="button" class="px-3 py-2 rounded-xl border border-slate-200 bg-white text-slate-600 font-semibold hover:bg-slate-100">Reset Logo</button>
          </div>
          <button id="btnSaveBranding" type="button" class="w-full py-2 rounded-xl theme-btn text-white font-bold shadow-xs min-h-[38px]">Simpan Identitas Kost</button>
        </div>

        <!-- Theme Color Selector -->
        <div class="bg-slate-50 border border-slate-200 rounded-2xl p-3.5 space-y-2.5">
          <p class="font-extrabold text-slate-900">🎨 Pilih Tema Warna Aplikasi</p>
          <div class="grid grid-cols-2 gap-2">
            <button type="button" data-set-theme="ocean" class="p-2.5 rounded-xl border border-slate-200 bg-white flex items-center gap-2 font-bold text-slate-800 hover:border-blue-500">
              <span class="w-5 h-5 rounded-full bg-gradient-to-tr from-sky-500 to-emerald-500 shrink-0"></span>
              <span>Biru-Hijau Segar</span>
            </button>
            <button type="button" data-set-theme="emerald" class="p-2.5 rounded-xl border border-slate-200 bg-white flex items-center gap-2 font-bold text-slate-800 hover:border-emerald-500">
              <span class="w-5 h-5 rounded-full bg-gradient-to-tr from-emerald-600 to-teal-400 shrink-0"></span>
              <span>Zamrud Tropis</span>
            </button>
            <button type="button" data-set-theme="indigo" class="p-2.5 rounded-xl border border-slate-200 bg-white flex items-center gap-2 font-bold text-slate-800 hover:border-indigo-500">
              <span class="w-5 h-5 rounded-full bg-gradient-to-tr from-indigo-600 to-pink-500 shrink-0"></span>
              <span>Royal Ungu-Pink</span>
            </button>
            <button type="button" data-set-theme="sunset" class="p-2.5 rounded-xl border border-slate-200 bg-white flex items-center gap-2 font-bold text-slate-800 hover:border-rose-500">
              <span class="w-5 h-5 rounded-full bg-gradient-to-tr from-orange-500 to-rose-600 shrink-0"></span>
              <span>Sunset Hangat</span>
            </button>
          </div>
        </div>

        <!-- Google Sheets Cloud Auto-Sync Section -->
        <div class="bg-emerald-50/90 border border-emerald-200 rounded-2xl p-3.5 space-y-3">
          <div class="flex items-center justify-between">
            <p class="font-extrabold text-emerald-950 flex items-center gap-1.5">☁️ Sinkronisasi Otomatis Google Sheets (Cloud)</p>
            <span id="cloudBadgeModal" class="px-2 py-0.5 rounded-full text-[10px] font-bold bg-amber-100 text-amber-800">Belum Terhubung</span>
          </div>
          <p class="text-[11px] text-emerald-800 leading-relaxed">
            Hubungkan ke Google Sheets agar data kamar, bukti transfer, dan keuangan otomatis sinkron real-time antara <strong>HP &amp; Laptop</strong>.
          </p>
          <div>
            <label class="block font-semibold text-emerald-900 mb-1">URL Web App Google Apps Script</label>
            <input id="cloudScriptUrlInput" type="url" placeholder="https://script.google.com/macros/s/.../exec" class="w-full px-3 py-2 rounded-xl border border-emerald-300 bg-white text-xs min-h-[38px]" />
          </div>
          <div class="flex items-center justify-between gap-2">
            <label class="flex items-center gap-2 font-semibold text-emerald-900 cursor-pointer">
              <input id="cloudAutoSyncCheck" type="checkbox" checked class="w-4 h-4 rounded text-emerald-600" />
              <span>Auto-Sync Real-Time tiap perubahan</span>
            </label>
            <span id="cloudLastSyncLabel" class="text-[10px] text-emerald-700 font-medium">Belum pernah sync</span>
          </div>
          <div class="grid grid-cols-2 gap-2">
            <button id="btnSaveCloudUrl" type="button" class="col-span-2 py-2 rounded-xl bg-emerald-600 hover:bg-emerald-700 text-white font-bold shadow-xs min-h-[38px]">🔗 Simpan &amp; Hubungkan Cloud</button>
            <button id="btnCloudPushNow" type="button" class="py-2 px-2.5 rounded-xl bg-white border border-emerald-300 text-emerald-800 font-bold hover:bg-emerald-100 min-h-[36px]">⬆️ Kirim ke Cloud</button>
            <button id="btnCloudPullNow" type="button" class="py-2 px-2.5 rounded-xl bg-white border border-emerald-300 text-emerald-800 font-bold hover:bg-emerald-100 min-h-[36px]">⬇️ Ambil dari Cloud</button>
            <button id="btnCopyShareCloudLink" type="button" class="py-2 px-2.5 rounded-xl bg-blue-600 hover:bg-blue-700 text-white font-bold min-h-[36px]">📲 Salin Link Buka di HP</button>
            <button id="btnToggleGasGuide" type="button" class="py-2 px-2.5 rounded-xl bg-slate-800 hover:bg-slate-900 text-white font-bold min-h-[36px]">📜 Kode Apps Script</button>
          </div>

          <!-- Collapsible Google Apps Script Code & Setup Guide -->
          <div id="gasGuideBox" class="hidden pt-2 border-t border-emerald-200 space-y-2 text-[11px] text-slate-700">
            <p class="font-bold text-slate-900">Cara Pasang di Google Sheets (Hanya 1 Menit):</p>
            <ol class="list-decimal pl-4 space-y-1">
              <li>Buka <strong>Google Sheets</strong> baru di laptop, klik menu <strong>Ekstensi &gt; Apps Script</strong>.</li>
              <li>Hapus isi yang ada, lalu <strong>Salin &amp; Tempel</strong> kode di bawah ini dan tekan <strong>Simpan</strong>.</li>
              <li>Klik <strong>Terapkan (Deploy) &gt; Deployment baru</strong>, pilih jenis <strong>Aplikasi Web (Web App)</strong>.</li>
              <li>Pada bagian <em>"Siapa yang memiliki akses"</em> pilih <strong>Siapa saja (Anyone)</strong>, klik <strong>Terapkan</strong>, lalu salin URL Web App (`.../exec`) ke kolom di atas!</li>
            </ol>
            <div class="relative">
              <textarea id="gasCodeTextarea" readonly rows="6" class="w-full p-2.5 rounded-xl bg-slate-900 text-emerald-300 font-mono text-[10px] leading-snug"></textarea>
              <button id="btnCopyGasCode" type="button" class="mt-1.5 w-full py-1.5 rounded-lg bg-slate-700 hover:bg-slate-600 text-white font-bold text-[11px]">📋 Salin Kode Google Apps Script</button>
            </div>
          </div>
        </div>

        <!-- Backup & Restore -->
        <div class="space-y-2">
          <p class="font-extrabold text-slate-900">💾 Backup File Manual (.json)</p>
          <div class="grid grid-cols-2 gap-2">
            <button id="btnBackupJson" type="button" class="px-3 py-2.5 rounded-xl bg-slate-900 text-white font-semibold hover:bg-slate-800 min-h-[40px]">Download Backup Data (.json)</button>
            <label class="px-3 py-2.5 rounded-xl border border-slate-300 bg-white hover:bg-slate-50 font-semibold text-slate-700 text-center cursor-pointer min-h-[40px] flex items-center justify-center">
              Restore Data (.json)
              <input id="inputRestoreJson" type="file" accept=".json" class="hidden" />
            </label>
          </div>
        </div>

        <div class="pt-2 border-t border-slate-200 flex items-center justify-between">
          <span class="text-slate-500">Mulai dari data kosong?</span>
          <button id="btnResetEmpty" type="button" class="px-3 py-1.5 rounded-xl border border-rose-200 text-rose-600 font-semibold hover:bg-rose-50">Kosongkan Semua Penghuni</button>
        </div>
      </div>
    </div>
  </div>

  <!-- MODAL 7: LAPORAN RINCIAN KEUANGAN SIAP PRINT / DOWNLOAD PDF -->
  <div id="modalFinanceReport" class="hidden fixed inset-0 z-50 bg-slate-900/70 backdrop-blur-xs flex items-end sm:items-center justify-center p-0 sm:p-4">
    <div class="bg-white w-full sm:max-w-2xl rounded-t-3xl sm:rounded-2xl shadow-2xl overflow-hidden max-h-[92vh] flex flex-col">
      <div class="no-print px-5 py-3.5 theme-gradient text-white flex items-center justify-between">
        <div>
          <h3 class="text-sm font-bold">Laporan Rincian Keuangan Bulanan (PDF / Print)</h3>
          <p class="text-[11px] text-white/85">Siap dicetak ke printer atau disimpan sebagai file PDF resmi</p>
        </div>
        <button type="button" data-close-modal="modalFinanceReport" class="p-1.5 rounded-lg bg-white/15 hover:bg-white/25 text-white">✕</button>
      </div>

      <div id="financeReportBody" class="p-5 space-y-4 overflow-y-auto text-xs flex-1 bg-white text-slate-800"></div>

      <div class="no-print px-4 py-3 border-t border-slate-200 bg-slate-50 flex flex-wrap items-center justify-between gap-2">
        <div class="flex items-center gap-2">
          <button id="btnDownloadFinancePdf" type="button" class="px-3.5 py-2 rounded-xl bg-rose-600 hover:bg-rose-700 text-white text-xs font-extrabold shadow-xs min-h-[38px] flex items-center gap-1.5">
            <span>📥 Download File PDF</span>
          </button>
          <button id="btnPrintFinanceReport" type="button" class="px-3.5 py-2 rounded-xl bg-slate-900 hover:bg-slate-800 text-white text-xs font-bold min-h-[38px] flex items-center gap-1.5">
            <span>🖨️ Prind Laporan (PDF)</span>
          </button>
        </div>
        <button type="button" data-close-modal="modalFinanceReport" class="px-4 py-2 rounded-xl border border-slate-300 bg-white text-xs font-semibold text-slate-700 min-h-[38px]">Tutup</button>
      </div>
    </div>
  </div>

  <!-- Toast Notification -->
  <div id="toastBox" class="hidden fixed top-16 left-1/2 -translate-x-1/2 z-50 max-w-xs w-11/12 bg-slate-900 text-white text-xs font-semibold px-4 py-3 rounded-2xl shadow-xl text-center"></div>

  <script>
    (function () {
      const STORAGE_KEY = 'kostku_pro_state_v2';
      const MONTH_NAMES = ['Januari', 'Februari', 'Maret', 'April', 'Mei', 'Juni', 'Juli', 'Agustus', 'September', 'Oktober', 'November', 'Desember'];

      function todayISO() {
        const d = new Date();
        const y = d.getFullYear();
        const m = String(d.getMonth() + 1).padStart(2, '0');
        const day = String(d.getDate()).padStart(2, '0');
        return `${y}-${m}-${day}`;
      }

      function currentMonthISO() {
        return todayISO().slice(0, 7);
      }

      function formatRp(num) {
        return 'Rp ' + (Number(num) || 0).toLocaleString('id-ID');
      }

      function formatDateID(iso) {
        if (!iso) return '-';
        const parts = iso.split('-');
        if (parts.length !== 3) return iso;
        const mIdx = parseInt(parts[1], 10) - 1;
        return `${parseInt(parts[2], 10)} ${MONTH_NAMES[mIdx] || parts[1]} ${parts[0]}`;
      }

      function formatMonthID(ym) {
        if (!ym) return '-';
        const [y, m] = ym.split('-').map(Number);
        return `${MONTH_NAMES[(m || 1) - 1]} ${y}`;
      }

      // Generate a clean sample SVG data URI receipt for initial demo
      function createSampleReceiptDataUri(roomName, tenant, month, amount) {
        const svg = `<svg xmlns="http://www.w3.org/2000/svg" width="360" height="220" viewBox="0 0 360 220">
          <rect width="360" height="220" rx="16" fill="#ecfdf5" stroke="#10b981" stroke-width="2"/>
          <text x="20" y="34" font-family="sans-serif" font-size="14" font-weight="bold" fill="#065f46">BUKTI TRANSFER SEWA KOST</text>
          <line x1="20" y1="46" x2="340" y2="46" stroke="#a7f3d0" stroke-width="1.5"/>
          <text x="20" y="74" font-family="sans-serif" font-size="12" fill="#047857">Kamar: ${roomName}</text>
          <text x="20" y="98" font-family="sans-serif" font-size="12" fill="#047857">Penghuni: ${tenant}</text>
          <text x="20" y="122" font-family="sans-serif" font-size="12" fill="#047857">Periode: ${month}</text>
          <text x="20" y="156" font-family="sans-serif" font-size="16" font-weight="bold" fill="#064e3b">Nominal: ${formatRp(amount)}</text>
          <rect x="240" y="165" width="95" height="30" rx="8" fill="#10b981"/>
          <text x="262" y="185" font-family="sans-serif" font-size="12" font-weight="bold" fill="#ffffff">LUNAS ✓</text>
        </svg>`;
        return 'data:image/svg+xml;utf8,' + encodeURIComponent(svg);
      }

      function createDefaultState() {
        const curM = currentMonthISO();
        const [y, m] = curM.split('-').map(Number);
        const todayDay = new Date().getDate();
        const pad = (n) => String(n).padStart(2, '0');
        const prevMDate = new Date(y, m - 2, 1);
        const prevM = `${prevMDate.getFullYear()}-${pad(prevMDate.getMonth() + 1)}`;

        const sampleProof1 = createSampleReceiptDataUri('Kamar 01', 'Andi Pratama', curM, 1200000);
        const sampleProofPrev = createSampleReceiptDataUri('Kamar 01', 'Andi Pratama', prevM, 1200000);

        return {
          kostName: 'KostKu Pro Harmoni',
          kostSubtitle: 'Manajemen Kamar, Bukti Transfer & Keuangan',
          kostLogo: '',
          theme: 'ocean',
          viewMode: 'hp',
          activeMonth: curM,
          rooms: [
            {
              id: 'r1', name: 'Kamar 01', price: 1200000, tenantName: 'Andi Pratama', phone: '081234567890',
              checkIn: `${prevM}-05`, checkOut: '', dueDay: 5, alarmEnabled: true,
              paidMonths: [prevM, curM],
              proofs: {
                [prevM]: { date: `${prevM}-05`, method: 'Transfer Bank', note: 'Transfer BCA a.n Andi Pratama', image: sampleProofPrev, amount: 1200000, tenantName: 'Andi Pratama' },
                [curM]: { date: `${curM}-05`, method: 'QRIS / E-Wallet', note: 'QRIS Mandiri a.n Andi Pratama', image: sampleProof1, amount: 1200000, tenantName: 'Andi Pratama' }
              }
            },
            {
              id: 'r2', name: 'Kamar 02', price: 1200000, tenantName: 'Siti Rahma', phone: '082198765432',
              checkIn: `${prevM}-${pad(todayDay)}`, checkOut: '', dueDay: todayDay, alarmEnabled: true,
              paidMonths: [prevM],
              proofs: {
                [prevM]: { date: `${prevM}-${pad(todayDay)}`, method: 'Transfer Bank', note: 'Transfer BRI a.n Siti Rahma', image: createSampleReceiptDataUri('Kamar 02', 'Siti Rahma', prevM, 1200000), amount: 1200000, tenantName: 'Siti Rahma' }
              }
            },
            {
              id: 'r3', name: 'Kamar 03', price: 1350000, tenantName: 'Budi Santoso', phone: '085211223344',
              checkIn: `${prevM}-02`, checkOut: '', dueDay: 2, alarmEnabled: true,
              paidMonths: [prevM],
              proofs: {
                [prevM]: { date: `${prevM}-02`, method: 'Transfer Bank', note: 'Transfer BNI a.n Budi Santoso', image: createSampleReceiptDataUri('Kamar 03', 'Budi Santoso', prevM, 1350000), amount: 1350000, tenantName: 'Budi Santoso' }
              }
            },
            { id: 'r4', name: 'Kamar 04', price: 1350000, tenantName: '', phone: '', checkIn: '', checkOut: '', dueDay: 1, alarmEnabled: true, paidMonths: [], proofs: {} },
            {
              id: 'r5', name: 'Kamar 05', price: 1500000, tenantName: 'Dewi Lestari', phone: '081355667788',
              checkIn: `${prevM}-${pad(Math.min(28, todayDay + 2))}`, checkOut: '', dueDay: Math.min(28, todayDay + 2), alarmEnabled: true,
              paidMonths: [prevM],
              proofs: {
                [prevM]: { date: `${prevM}-${pad(Math.min(28, todayDay + 2))}`, method: 'Transfer Bank', note: 'Transfer Mandiri a.n Dewi', image: createSampleReceiptDataUri('Kamar 05', 'Dewi Lestari', prevM, 1500000), amount: 1500000, tenantName: 'Dewi Lestari' }
              }
            },
            { id: 'r6', name: 'Kamar 06', price: 1500000, tenantName: '', phone: '', checkIn: '', checkOut: '', dueDay: 1, alarmEnabled: true, paidMonths: [], proofs: {} }
          ],
          transactions: [
            { id: 't1', date: `${curM}-05`, type: 'pemasukan', category: 'Sewa Kamar', amount: 1200000, desc: 'Sewa Kamar 01 - Andi Pratama (' + curM + ')', roomId: 'r1' },
            { id: 't2', date: `${curM}-06`, type: 'pengeluaran', category: 'Listrik & Token', amount: 450000, desc: 'Token Listrik Utama Kost', roomId: '' },
            { id: 't3', date: `${curM}-08`, type: 'pengeluaran', category: 'Air PDAM', amount: 185000, desc: 'Tagihan Air PDAM Bulanan', roomId: '' },
            { id: 't4', date: `${curM}-10`, type: 'pengeluaran', category: 'WiFi / Internet', amount: 350000, desc: 'Langganan WiFi Kost 50Mbps', roomId: '' },
            { id: 't5', date: `${prevM}-05`, type: 'pemasukan', category: 'Sewa Kamar', amount: 5250000, desc: 'Total Sewa Kamar Bulan Lalu', roomId: '' },
            { id: 't6', date: `${prevM}-12`, type: 'pengeluaran', category: 'Kebersihan & Sampah', amount: 200000, desc: 'Iuran Sampah & Kebersihan', roomId: '' }
          ],
          history: [
            {
              id: 'h1', roomId: 'r1', roomName: 'Kamar 01', tenantName: 'Andi Pratama', phone: '081234567890',
              price: 1200000, checkIn: `${prevM}-05`, checkOut: '', status: 'Aktif',
              payments: [
                { month: prevM, date: `${prevM}-05`, amount: 1200000, method: 'Transfer Bank', note: 'Transfer BCA a.n Andi Pratama', image: sampleProofPrev },
                { month: curM, date: `${curM}-05`, amount: 1200000, method: 'QRIS / E-Wallet', note: 'QRIS Mandiri a.n Andi Pratama', image: sampleProof1 }
              ]
            },
            {
              id: 'h2', roomId: 'r2', roomName: 'Kamar 02', tenantName: 'Siti Rahma', phone: '082198765432',
              price: 1200000, checkIn: `${prevM}-${pad(todayDay)}`, checkOut: '', status: 'Aktif',
              payments: [
                { month: prevM, date: `${prevM}-${pad(todayDay)}`, amount: 1200000, method: 'Transfer Bank', note: 'Transfer BRI a.n Siti Rahma', image: createSampleReceiptDataUri('Kamar 02', 'Siti Rahma', prevM, 1200000) }
              ]
            },
            {
              id: 'h3', roomId: 'r4', roomName: 'Kamar 04', tenantName: 'Riko Wijaya', phone: '081900112233',
              price: 1350000, checkIn: `${prevM}-01`, checkOut: `${prevM}-28`, status: 'Keluar',
              payments: [
                { month: prevM, date: `${prevM}-01`, amount: 1350000, method: 'Transfer Bank', note: 'Lunas Sewa Awal Masuk', image: createSampleReceiptDataUri('Kamar 04', 'Riko Wijaya', prevM, 1350000) }
              ]
            }
          ]
        };
      }

      function loadState() {
        try {
          const raw = localStorage.getItem(STORAGE_KEY) || localStorage.getItem('kostku_pro_state_v1');
          if (raw) {
            const parsed = JSON.parse(raw);
            if (parsed && Array.isArray(parsed.rooms)) {
              const def = createDefaultState();
              parsed.kostName = parsed.kostName || def.kostName;
              parsed.kostSubtitle = parsed.kostSubtitle || def.kostSubtitle;
              parsed.kostLogo = parsed.kostLogo || '';
              parsed.theme = parsed.theme || 'ocean';
              parsed.viewMode = parsed.viewMode || 'hp';
              parsed.rooms.forEach(r => { if (!r.proofs) r.proofs = {}; });
              return parsed;
            }
          }
        } catch (e) {}
        return createDefaultState();
      }

      const CLOUD_CONFIG_KEY = 'kostku_pro_cloud_cfg_v1';
      let state = loadState();
      let cloudConfig = loadCloudConfig();
      let currentTab = 'kamar';
      let currentRoomFilter = 'all';
      let chartInstance = null;
      let alarmInterval = null;
      let audioCtx = null;
      let pendingReceiptDataUrl = '';
      let cloudSyncTimer = null;
      let isSyncingCloud = false;

      function loadCloudConfig() {
        let cfg = { scriptUrl: '', autoSync: true, lastSync: '' };
        try {
          const raw = localStorage.getItem(CLOUD_CONFIG_KEY);
          if (raw) cfg = Object.assign(cfg, JSON.parse(raw));
        } catch (e) {}
        try {
          const params = new URLSearchParams(window.location.search);
          const urlParam = params.get('cloud');
          if (urlParam && urlParam.startsWith('https://')) {
            cfg.scriptUrl = urlParam.trim();
            localStorage.setItem(CLOUD_CONFIG_KEY, JSON.stringify(cfg));
          }
        } catch (e) {}
        return cfg;
      }

      function saveCloudConfig() {
        try {
          localStorage.setItem(CLOUD_CONFIG_KEY, JSON.stringify(cloudConfig));
        } catch (e) {}
        updateCloudStatusUI();
      }

      function updateCloudStatusUI(statusMode, customLabel) {
        const dot = document.getElementById('cloudDot');
        const text = document.getElementById('cloudStatusText');
        const badge = document.getElementById('cloudBadgeModal');
        const lastLbl = document.getElementById('cloudLastSyncLabel');
        if (!dot || !text) return;

        if (lastLbl) {
          lastLbl.textContent = cloudConfig.lastSync ? `Sync: ${cloudConfig.lastSync}` : 'Belum pernah sync';
        }

        if (!cloudConfig.scriptUrl) {
          dot.className = 'w-1.5 h-1.5 rounded-full bg-amber-300';
          text.textContent = 'Lokal';
          if (badge) {
            badge.className = 'px-2 py-0.5 rounded-full text-[10px] font-bold bg-amber-100 text-amber-800';
            badge.textContent = 'Mode Lokal';
          }
          return;
        }

        if (statusMode === 'syncing') {
          dot.className = 'w-1.5 h-1.5 rounded-full bg-sky-300 animate-ping';
          text.textContent = customLabel || 'Syncing...';
          if (badge) {
            badge.className = 'px-2 py-0.5 rounded-full text-[10px] font-bold bg-sky-100 text-sky-800';
            badge.textContent = 'Sinkronisasi...';
          }
        } else if (statusMode === 'error') {
          dot.className = 'w-1.5 h-1.5 rounded-full bg-rose-400';
          text.textContent = 'Cloud Offline';
          if (badge) {
            badge.className = 'px-2 py-0.5 rounded-full text-[10px] font-bold bg-rose-100 text-rose-800';
            badge.textContent = 'Gagal Konek (Cek URL/Izin)';
          }
        } else {
          dot.className = 'w-1.5 h-1.5 rounded-full bg-emerald-300';
          text.textContent = 'Cloud Aktif';
          if (badge) {
            badge.className = 'px-2 py-0.5 rounded-full text-[10px] font-bold bg-emerald-100 text-emerald-800';
            badge.textContent = 'Terhubung ke Sheets ✓';
          }
        }
      }

      async function pushStateToCloud(silent) {
        if (!cloudConfig.scriptUrl || !cloudConfig.scriptUrl.startsWith('https://')) {
          if (!silent) showToast('Masukkan URL Google Apps Script yang valid (https://...) terlebih dahulu.');
          return;
        }
        if (isSyncingCloud) return;
        isSyncingCloud = true;
        updateCloudStatusUI('syncing', 'Menyimpan...');
        try {
          state.updatedAt = Date.now();
          const res = await fetch(cloudConfig.scriptUrl, {
            method: 'POST',
            headers: { 'Content-Type': 'text/plain;charset=utf-8' },
            body: JSON.stringify({ action: 'save', updatedAt: state.updatedAt, state })
          });
          if (!res.ok) throw new Error('HTTP ' + res.status);
          const nowStr = new Date().toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' });
          cloudConfig.lastSync = nowStr;
          saveCloudConfig();
          updateCloudStatusUI('ok');
          if (!silent) showToast('☁️ Data berhasil dikirim & disinkronkan ke Google Sheets!');
        } catch (err) {
          updateCloudStatusUI('error');
          if (!silent) showToast('Gagal kirim ke Cloud. Pastikan URL Apps Script memiliki akses "Siapa saja / Anyone".');
        } finally {
          isSyncingCloud = false;
        }
      }

      async function pullStateFromCloud(silent) {
        if (!cloudConfig.scriptUrl || !cloudConfig.scriptUrl.startsWith('https://')) {
          if (!silent) showToast('Masukkan URL Google Apps Script terlebih dahulu.');
          return;
        }
        if (isSyncingCloud) return;
        isSyncingCloud = true;
        updateCloudStatusUI('syncing', 'Memuat...');
        try {
          const reqUrl = new URL(cloudConfig.scriptUrl);
          reqUrl.searchParams.set('action', 'load');
          reqUrl.searchParams.set('t', String(Date.now()));
          const res = await fetch(reqUrl.toString());
          if (!res.ok) throw new Error('HTTP ' + res.status);
          const data = await res.json();
          const remoteState = (data && data.state && Array.isArray(data.state.rooms)) ? data.state : (data && Array.isArray(data.rooms) ? data : null);
          if (remoteState) {
            state = remoteState;
            state.rooms.forEach(r => { if (!r.proofs) r.proofs = {}; });
            try { localStorage.setItem(STORAGE_KEY, JSON.stringify(state)); } catch (e) {}
            const nowStr = new Date().toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' });
            cloudConfig.lastSync = nowStr;
            saveCloudConfig();
            renderAll();
            updateCloudStatusUI('ok');
            if (!silent) showToast('☁️ Data terbaru dari Google Sheets berhasil dimuat!');
          } else {
            updateCloudStatusUI('ok');
            if (!silent) showToast('Cloud Google Sheets masih kosong. Klik "Kirim ke Cloud" untuk mengunggah data awal.');
          }
        } catch (err) {
          updateCloudStatusUI('error');
          if (!silent) showToast('Gagal memuat dari Cloud. Periksa koneksi atau URL Apps Script Anda.');
        } finally {
          isSyncingCloud = false;
        }
      }

      function saveState() {
        state.updatedAt = Date.now();
        try {
          localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
        } catch (e) {}
        if (cloudConfig.scriptUrl && cloudConfig.autoSync) {
          clearTimeout(cloudSyncTimer);
          cloudSyncTimer = setTimeout(() => { pushStateToCloud(true); }, 1200);
        }
      }

      function showToast(msg) {
        const box = document.getElementById('toastBox');
        box.textContent = msg;
        box.classList.remove('hidden');
        clearTimeout(box._timer);
        box._timer = setTimeout(() => box.classList.add('hidden'), 3200);
      }

      // Apply Branding, Theme & View Mode
      function applyBrandingAndTheme() {
        document.body.setAttribute('data-theme', state.theme || 'ocean');
        document.getElementById('kostTitleDisplay').textContent = state.kostName || 'KostKu Pro';
        document.getElementById('kostSubtitleDisplay').textContent = state.kostSubtitle || 'Manajemen Kamar, Bukti Transfer & Keuangan';

        const logoImg = document.getElementById('kostLogoImg');
        const logoDef = document.getElementById('kostLogoDefault');
        if (state.kostLogo) {
          logoImg.src = state.kostLogo;
          logoImg.classList.remove('hidden');
          logoDef.classList.add('hidden');
        } else {
          logoImg.src = '';
          logoImg.classList.add('hidden');
          logoDef.classList.remove('hidden');
        }

        const isHp = (state.viewMode || 'hp') === 'hp';
        document.body.classList.toggle('mode-hp', isHp);
        document.body.classList.toggle('mode-wide', !isHp);
        const bottomNav = document.getElementById('bottomNavBar');
        bottomNav.style.maxWidth = isHp ? '440px' : '1200px';
        document.getElementById('btnToggleViewMode').textContent = isHp ? '📱 Mode HP' : '💻 Layar Lebar';
        const roomsGrid = document.getElementById('roomsGrid');
        roomsGrid.className = isHp ? 'grid grid-cols-1 gap-3.5' : 'grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-4';
      }

      function getRoomDueInfo(room, monthISO) {
        if (!room.tenantName) return { status: 'kosong', label: 'Kosong', daysDiff: 999, dueDateISO: '' };
        const isPaid = Array.isArray(room.paidMonths) && room.paidMonths.includes(monthISO);
        const [y, m] = monthISO.split('-').map(Number);
        const maxDay = new Date(y, m, 0).getDate();
        const dueDay = Math.min(Math.max(1, Number(room.dueDay) || 1), maxDay);
        const dueDateISO = `${y}-${String(m).padStart(2, '0')}-${String(dueDay).padStart(2, '0')}`;

        if (isPaid) return { status: 'lunas', label: 'Lunas ✓', daysDiff: 0, dueDateISO };

        const today = new Date(todayISO() + 'T00:00:00');
        const dueDate = new Date(dueDateISO + 'T00:00:00');
        const diffDays = Math.round((dueDate - today) / (1000 * 60 * 60 * 24));

        if (diffDays < 0) return { status: 'terlambat', label: `Terlambat ${Math.abs(diffDays)} hr`, daysDiff: diffDays, dueDateISO };
        if (diffDays === 0) return { status: 'jatuh_tempo', label: 'Tempo Hari Ini!', daysDiff: 0, dueDateISO };
        if (diffDays <= 3) return { status: 'segera', label: `H-${diffDays} Tempo`, daysDiff: diffDays, dueDateISO };
        return { status: 'menunggu', label: `Tempo Tgl ${dueDay}`, daysDiff: diffDays, dueDateISO };
      }

      function playAlarmBeep() {
        try {
          if (!audioCtx) {
            const Ctx = window.AudioContext || window.webkitAudioContext;
            if (Ctx) audioCtx = new Ctx();
          }
          if (!audioCtx) return;
          if (audioCtx.state === 'suspended') audioCtx.resume();

          const now = audioCtx.currentTime;
          [0, 0.22, 0.44].forEach((offset, idx) => {
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'sine';
            osc.frequency.setValueAtTime(idx === 2 ? 1046.5 : 880, now + offset);
            gain.gain.setValueAtTime(0.001, now + offset);
            gain.gain.exponentialRampToValueAtTime(0.28, now + offset + 0.02);
            gain.gain.exponentialRampToValueAtTime(0.001, now + offset + 0.18);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start(now + offset);
            osc.stop(now + offset + 0.19);
          });

          if (navigator.vibrate) navigator.vibrate([250, 100, 250, 100, 400]);
        } catch (e) {}
      }

      function startAlarmLoop() {
        stopAlarmLoop();
        playAlarmBeep();
        alarmInterval = setInterval(playAlarmBeep, 2400);
        document.getElementById('btnStopAlarm').classList.remove('hidden');
        showToast('Alarm berbunyi! Klik "Matikan Suara" untuk berhenti.');
      }

      function stopAlarmLoop() {
        if (alarmInterval) {
          clearInterval(alarmInterval);
          alarmInterval = null;
        }
        document.getElementById('btnStopAlarm').classList.add('hidden');
      }

      function triggerBrowserNotification(title, body) {
        try {
          if ('Notification' in window && Notification.permission === 'granted') {
            new Notification(title, { body });
          }
        } catch (e) {}
      }

      function downloadICS(room, dueInfo) {
        const dt = dueInfo.dueDateISO.replace(/-/g, '');
        const ics = [
          'BEGIN:VCALENDAR',
          'VERSION:2.0',
          'PRODID:-//KostKu Pro//Alarm Jatuh Tempo//ID',
          'BEGIN:VEVENT',
          `UID:kostku-${room.id}-${dt}@kostku.local`,
          `DTSTART;VALUE=DATE:${dt}`,
          `SUMMARY:Tagihan ${state.kostName} - ${room.name} (${room.tenantName})`,
          `DESCRIPTION:Jatuh tempo sewa ${room.name} sebesar ${formatRp(room.price)} untuk ${room.tenantName}.`,
          'RRULE:FREQ=MONTHLY',
          'BEGIN:VALARM',
          'TRIGGER:-PT9H',
          'ACTION:DISPLAY',
          `DESCRIPTION:Alarm Bayar Kost ${room.name}`,
          'END:VALARM',
          'END:VEVENT',
          'END:VCALENDAR'
        ].join('\r\n');
        const blob = new Blob([ics], { type: 'text/calendar;charset=utf-8' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = `Alarm_${room.name.replace(/\s+/g, '_')}.ics`;
        a.click();
        URL.revokeObjectURL(url);
        showToast(`Kalender alarm ${room.name} diunduh!`);
      }

      function renderAll() {
        applyBrandingAndTheme();
        const [y, m] = state.activeMonth.split('-').map(Number);
        document.getElementById('activePeriodLabel').textContent = `${MONTH_NAMES[m - 1]} ${y}`;
        document.getElementById('monthPicker').value = state.activeMonth;

        renderRooms();
        renderAlarms();
        renderFinance();
        renderHistory();
      }

      function renderRooms() {
        const search = document.getElementById('roomSearchInput').value.trim().toLowerCase();
        const totalRooms = state.rooms.length;
        const occupied = state.rooms.filter(r => !!r.tenantName).length;
        const empty = totalRooms - occupied;
        const unpaidCount = state.rooms.filter(r => {
          const info = getRoomDueInfo(r, state.activeMonth);
          return r.tenantName && info.status !== 'lunas';
        }).length;

        document.getElementById('roomKpiGrid').innerHTML = `
          <div class="bg-gradient-to-br from-blue-600 to-indigo-600 text-white p-3.5 rounded-2xl shadow-sm">
            <p class="text-[11px] font-medium text-blue-100">Kamar Terisi / Total</p>
            <p class="text-xl font-extrabold mt-0.5">${occupied} <span class="text-xs font-normal text-blue-200">/ ${totalRooms} Kamar</span></p>
          </div>
          <div class="bg-gradient-to-br from-emerald-500 to-teal-600 text-white p-3.5 rounded-2xl shadow-sm">
            <p class="text-[11px] font-medium text-emerald-100">Kamar Kosong</p>
            <p class="text-xl font-extrabold mt-0.5">${empty} <span class="text-xs font-normal text-emerald-100">Tersedia</span></p>
          </div>
          <div class="col-span-2 bg-white p-3 rounded-2xl border border-slate-200 shadow-2xs flex items-center justify-between">
            <div>
              <p class="text-[11px] font-semibold text-slate-500">Status Tagihan Bulan ${MONTH_NAMES[parseInt(state.activeMonth.split('-')[1], 10) - 1]}</p>
              <p class="text-xs font-bold ${unpaidCount > 0 ? 'text-rose-600' : 'text-emerald-600'} mt-0.5">
                ${unpaidCount > 0 ? `${unpaidCount} Kamar Belum Bayar` : 'Semua Kamar Terisi Sudah Lunas'}
              </p>
            </div>
            <span class="px-2.5 py-1 rounded-xl text-xs font-extrabold ${unpaidCount > 0 ? 'bg-rose-100 text-rose-700' : 'bg-emerald-100 text-emerald-700'}">
              ${occupied - unpaidCount}/${occupied} Lunas
            </span>
          </div>
        `;

        const filtered = state.rooms.filter(r => {
          const info = getRoomDueInfo(r, state.activeMonth);
          if (currentRoomFilter === 'terisi' && !r.tenantName) return false;
          if (currentRoomFilter === 'kosong' && r.tenantName) return false;
          if (currentRoomFilter === 'belum_bayar' && (!r.tenantName || info.status === 'lunas')) return false;
          if (search) {
            const hay = `${r.name} ${r.tenantName} ${r.phone || ''}`.toLowerCase();
            if (!hay.includes(search)) return false;
          }
          return true;
        });

        const grid = document.getElementById('roomsGrid');
        if (filtered.length === 0) {
          grid.innerHTML = `<div class="col-span-full bg-white p-6 rounded-2xl border border-dashed border-slate-300 text-center text-xs text-slate-500">Tidak ada kamar yang sesuai pencarian.</div>`;
          return;
        }

        grid.innerHTML = filtered.map(room => {
          const due = getRoomDueInfo(room, state.activeMonth);
          const isOccupied = !!room.tenantName;
          const proof = (room.proofs && room.proofs[state.activeMonth]) || null;
          const totalProofsCount = room.proofs ? Object.keys(room.proofs).length : 0;

          let badgeClass = 'bg-slate-100 text-slate-600';
          let cardBorder = 'border-slate-200';
          if (due.status === 'lunas') { badgeClass = 'bg-emerald-100 text-emerald-700'; cardBorder = 'border-emerald-200'; }
          else if (due.status === 'terlambat' || due.status === 'jatuh_tempo') { badgeClass = 'bg-rose-100 text-rose-700'; cardBorder = 'border-rose-300'; }
          else if (due.status === 'segera') { badgeClass = 'bg-amber-100 text-amber-700'; cardBorder = 'border-amber-200'; }
          else if (due.status === 'menunggu') { badgeClass = 'bg-blue-100 text-blue-700'; }

          return `
            <div class="bg-white rounded-2xl border ${cardBorder} shadow-sm p-3.5 flex flex-col justify-between gap-3">
              <div>
                <div class="flex items-start justify-between gap-2">
                  <div class="flex items-center gap-2.5">
                    <div class="w-10 h-10 rounded-xl theme-bg-soft theme-text font-extrabold text-xs flex items-center justify-center shrink-0 border border-blue-100">
                      ${room.name.replace(/[^0-9A-Za-z]/g, '').slice(-2) || 'KM'}
                    </div>
                    <div>
                      <h4 class="text-sm font-extrabold text-slate-900">${room.name}</h4>
                      <p class="text-xs font-bold theme-text">${formatRp(room.price)} <span class="text-[10px] font-normal text-slate-400">/bln</span></p>
                    </div>
                  </div>
                  <span class="px-2.5 py-1 rounded-full text-[10px] font-extrabold ${badgeClass}">${due.label}</span>
                </div>

                ${isOccupied ? `
                  <div class="mt-3 pt-2.5 border-t border-slate-100 space-y-1.5 text-xs">
                    <div class="flex justify-between items-center">
                      <span class="text-slate-500">Penghuni:</span>
                      <span class="font-extrabold text-slate-900">${room.tenantName}</span>
                    </div>
                    <div class="flex justify-between items-center bg-blue-50/80 border border-blue-100 px-2.5 py-1.5 rounded-xl">
                      <span class="text-[11px] font-semibold text-blue-800">📞 No. Telp / HP:</span>
                      ${room.phone ? `
                        <div class="flex items-center gap-1.5">
                          <a href="tel:${room.phone}" class="font-extrabold text-blue-700 hover:underline">${room.phone}</a>
                          <a href="https://wa.me/${room.phone.replace(/[^0-9]/g, '').replace(/^0/, '62')}" target="_blank" rel="noopener" class="px-1.5 py-0.5 rounded-md bg-emerald-500 text-white text-[10px] font-bold" title="Chat WhatsApp">WA</a>
                        </div>
                      ` : `<span class="font-semibold text-slate-400">Belum diisi</span>`}
                    </div>
                    <div class="flex justify-between items-center">
                      <span class="text-slate-500">Mulai Kos (Masuk):</span>
                      <span class="font-semibold text-slate-700">${formatDateID(room.checkIn)}</span>
                    </div>
                    <div class="flex justify-between items-center">
                      <span class="text-slate-500">Tgl Keluar:</span>
                      <span class="font-semibold ${room.checkOut ? 'text-rose-600' : 'text-emerald-600'}">${room.checkOut ? formatDateID(room.checkOut) : 'Masih Aktif Kos'}</span>
                    </div>
                    <div class="flex justify-between items-center">
                      <span class="text-slate-500">Alarm Tempo:</span>
                      <span class="font-bold text-slate-800">Tgl ${room.dueDay} / bulan</span>
                    </div>

                    <!-- Bukti Transfer Box per Kamar -->
                    <div class="mt-2 p-2.5 rounded-xl ${proof ? 'bg-emerald-50/80 border border-emerald-200' : 'bg-slate-50 border border-slate-200/80'} flex items-center justify-between gap-2">
                      <div class="flex items-center gap-2 min-w-0">
                        ${proof && proof.image ? `
                          <img src="${proof.image}" alt="Bukti" class="w-9 h-9 rounded-lg object-cover border border-emerald-300 shrink-0 cursor-pointer" data-action="view-proof" data-id="${room.id}" />
                        ` : `
                          <div class="w-9 h-9 rounded-lg ${proof ? 'bg-emerald-200 text-emerald-800' : 'bg-slate-200 text-slate-500'} flex items-center justify-center text-xs font-bold shrink-0">🧾</div>
                        `}
                        <div class="min-w-0">
                          <p class="text-[11px] font-bold ${proof ? 'text-emerald-900' : 'text-slate-700'} truncate">
                            ${proof ? `Bukti Transfer (${proof.method})` : 'Belum Ada Bukti Transfer'}
                          </p>
                          <p class="text-[10px] ${proof ? 'text-emerald-700' : 'text-slate-500'} truncate">
                            ${proof ? `${formatDateID(proof.date)} • ${proof.note || 'Tersimpan'}` : `Total ${totalProofsCount} bukti tersimpan di rincian`}
                          </p>
                        </div>
                      </div>
                      ${proof ? `
                        <button type="button" data-action="view-proof" data-id="${room.id}" class="px-2 py-1 rounded-lg bg-white border border-emerald-300 text-emerald-700 text-[10px] font-bold shrink-0">Lihat</button>
                      ` : `
                        <button type="button" data-action="pay" data-id="${room.id}" class="px-2 py-1 rounded-lg bg-blue-50 border border-blue-200 text-blue-700 text-[10px] font-bold shrink-0">+ Upload</button>
                      `}
                    </div>
                  </div>
                ` : `
                  <div class="mt-3 py-4 text-center bg-slate-50 rounded-xl border border-dashed border-slate-200">
                    <p class="text-xs font-medium text-slate-400">Kamar Kosong — Siap Disewakan</p>
                  </div>
                `}
              </div>

              <!-- Action Buttons -->
              <div class="space-y-1.5 pt-2 border-t border-slate-100">
                ${isOccupied ? `
                  <div class="grid grid-cols-2 gap-1.5">
                    <button type="button" data-action="pay" data-id="${room.id}" class="px-2.5 py-2 rounded-xl ${due.status === 'lunas' ? 'bg-emerald-100 text-emerald-800' : 'bg-emerald-600 text-white'} text-[11px] font-bold min-h-[36px] flex items-center justify-center gap-1">
                      <span>${due.status === 'lunas' ? '🧾 Edit Bukti / Lunas' : '💳 Bayar & Bukti TF'}</span>
                    </button>
                    <button type="button" data-action="statement" data-id="${room.id}" class="px-2.5 py-2 rounded-xl bg-indigo-50 border border-indigo-200 text-indigo-700 hover:bg-indigo-100 text-[11px] font-bold min-h-[36px] flex items-center justify-center gap-1">
                      <span>📋 Rincian Awal-Keluar</span>
                    </button>
                  </div>
                  <div class="flex items-center gap-1.5">
                    <button type="button" data-action="edit" data-id="${room.id}" class="flex-1 px-2.5 py-1.5 rounded-xl border border-slate-200 text-slate-700 text-[11px] font-semibold min-h-[34px]">Edit Kamar</button>
                    ${due.status === 'lunas' ? `<button type="button" data-action="unpay" data-id="${room.id}" class="px-2.5 py-1.5 rounded-xl border border-slate-200 text-slate-500 text-[11px] font-semibold min-h-[34px]">Batal Lunas</button>` : ''}
                    <button type="button" data-action="checkout" data-id="${room.id}" class="px-2.5 py-1.5 rounded-xl border border-rose-200 text-rose-600 text-[11px] font-semibold min-h-[34px]">Keluar Kos</button>
                  </div>
                ` : `
                  <div class="flex items-center gap-1.5">
                    <button type="button" data-action="checkin" data-id="${room.id}" class="flex-1 px-3 py-2 rounded-xl theme-btn text-white text-xs font-bold min-h-[38px]">+ Input Penghuni Masuk</button>
                    <button type="button" data-action="statement" data-id="${room.id}" class="px-2.5 py-2 rounded-xl border border-slate-200 text-slate-600 text-[11px] font-semibold min-h-[38px]" title="Rincian Riwayat Kamar">📋 Rincian</button>
                    <button type="button" data-action="delete-room" data-id="${room.id}" class="px-2.5 py-2 rounded-xl border border-slate-200 text-rose-500 text-[11px] font-semibold min-h-[38px]">Hapus</button>
                  </div>
                `}
              </div>
            </div>
          `;
        }).join('');
      }

      function renderAlarms() {
        const occupiedRooms = state.rooms.filter(r => !!r.tenantName);
        const alerts = occupiedRooms.map(r => ({ room: r, due: getRoomDueInfo(r, state.activeMonth) }))
          .filter(x => x.due.status !== 'lunas')
          .sort((a, b) => a.due.daysDiff - b.due.daysDiff);

        const urgentCount = alerts.filter(x => x.room.alarmEnabled && (x.due.status === 'terlambat' || x.due.status === 'jatuh_tempo' || x.due.status === 'segera')).length;

        ['alarmBadgeDesktop', 'alarmBadgeMobile'].forEach(id => {
          const el = document.getElementById(id);
          if (urgentCount > 0) {
            el.textContent = urgentCount;
            el.classList.remove('hidden');
          } else {
            el.classList.add('hidden');
          }
        });

        const bellDot = document.getElementById('headerBellDot');
        const banner = document.getElementById('urgentAlarmBanner');
        const overdueOrToday = alerts.filter(x => x.room.alarmEnabled && (x.due.status === 'terlambat' || x.due.status === 'jatuh_tempo'));

        if (overdueOrToday.length > 0) {
          bellDot.classList.remove('hidden');
          banner.classList.remove('hidden');
          document.getElementById('urgentBannerTitle').textContent = `${overdueOrToday.length} kamar jatuh tempo pembayaran!`;
          document.getElementById('urgentBannerSub').textContent = overdueOrToday.map(x => `${x.room.name} (${x.room.tenantName})`).join(', ');
        } else {
          bellDot.classList.add('hidden');
          banner.classList.add('hidden');
        }

        const container = document.getElementById('alarmListContainer');
        if (alerts.length === 0) {
          container.innerHTML = `<div class="bg-white p-6 rounded-2xl border border-slate-200 text-center text-xs text-emerald-700 font-bold">🎉 Semua kamar terisi sudah lunas untuk periode ini!</div>`;
          return;
        }

        container.innerHTML = alerts.map(({ room, due }) => {
          const isUrgent = due.status === 'terlambat' || due.status === 'jatuh_tempo';
          const cleanPhone = (room.phone || '').replace(/[^0-9]/g, '').replace(/^0/, '62');
          const waText = encodeURIComponent(`Halo ${room.tenantName}, mengingatkan pembayaran sewa ${room.name} di ${state.kostName} periode ${state.activeMonth} sebesar ${formatRp(room.price)} jatuh tempo pada ${formatDateID(due.dueDateISO)}. Mohon kirimkan bukti transfer jika sudah bayar. Terima kasih.`);
          const waUrl = cleanPhone ? `https://wa.me/${cleanPhone}?text=${waText}` : '';

          return `
            <div class="bg-white p-3.5 rounded-2xl border ${isUrgent && room.alarmEnabled ? 'border-rose-400 alarm-pulse' : 'border-slate-200'} shadow-sm space-y-3">
              <div class="flex items-start justify-between gap-2">
                <div class="flex items-center gap-2.5">
                  <div class="w-10 h-10 rounded-xl ${isUrgent ? 'bg-rose-100 text-rose-600' : 'bg-amber-100 text-amber-700'} flex items-center justify-center shrink-0 font-extrabold text-xs">
                    Tgl ${room.dueDay}
                  </div>
                  <div>
                    <h4 class="text-xs font-extrabold text-slate-900">${room.name} — ${room.tenantName}</h4>
                    <p class="text-[11px] text-slate-500">${formatRp(room.price)} • Tempo: ${formatDateID(due.dueDateISO)}</p>
                  </div>
                </div>
                <span class="px-2 py-0.5 rounded-full text-[10px] font-bold ${isUrgent ? 'bg-rose-100 text-rose-700' : 'bg-amber-100 text-amber-700'}">${due.label}</span>
              </div>

              <div class="flex flex-wrap items-center gap-1.5 pt-2 border-t border-slate-100">
                <button type="button" data-alarm-action="toggle" data-id="${room.id}" class="px-2.5 py-1.5 rounded-xl border text-[11px] font-semibold ${room.alarmEnabled ? 'border-blue-200 bg-blue-50 text-blue-700' : 'border-slate-200 bg-slate-50 text-slate-500'}">
                  ${room.alarmEnabled ? '🔔 Alarm On' : '🔕 Alarm Off'}
                </button>
                <button type="button" data-alarm-action="ics" data-id="${room.id}" class="px-2.5 py-1.5 rounded-xl border border-slate-200 text-slate-700 text-[11px] font-semibold">📅 Kalender HP</button>
                ${waUrl ? `<a href="${waUrl}" target="_blank" rel="noopener" class="px-2.5 py-1.5 rounded-xl bg-emerald-50 border border-emerald-200 text-emerald-700 text-[11px] font-bold">💬 WA</a>` : ''}
                <button type="button" data-alarm-action="pay" data-id="${room.id}" class="ml-auto px-3 py-1.5 rounded-xl theme-btn text-white text-[11px] font-bold">Bayar &amp; Bukti</button>
              </div>
            </div>
          `;
        }).join('');
      }

      function renderFinance() {
        const monthTxs = state.transactions.filter(t => t.date && t.date.startsWith(state.activeMonth));
        const totalIn = monthTxs.filter(t => t.type === 'pemasukan').reduce((s, t) => s + Number(t.amount || 0), 0);
        const totalOut = monthTxs.filter(t => t.type === 'pengeluaran').reduce((s, t) => s + Number(t.amount || 0), 0);
        const net = totalIn - totalOut;

        document.getElementById('financeKpiGrid').innerHTML = `
          <div class="grid grid-cols-2 gap-2.5">
            <div class="bg-emerald-50 border border-emerald-200 p-3.5 rounded-2xl">
              <p class="text-[11px] font-semibold text-emerald-700">Pemasukan Bulan Ini</p>
              <p class="text-base font-extrabold text-emerald-700 mt-0.5">${formatRp(totalIn)}</p>
            </div>
            <div class="bg-rose-50 border border-rose-200 p-3.5 rounded-2xl">
              <p class="text-[11px] font-semibold text-rose-700">Pengeluaran Bulan Ini</p>
              <p class="text-base font-extrabold text-rose-600 mt-0.5">${formatRp(totalOut)}</p>
            </div>
          </div>
          <div class="theme-gradient text-white p-4 rounded-2xl shadow-sm flex items-center justify-between">
            <div>
              <p class="text-[11px] font-semibold text-white/85">Saldo Bersih / Keuntungan Bulan Ini</p>
              <p class="text-xl font-extrabold mt-0.5">${formatRp(net)}</p>
            </div>
            <span class="px-2.5 py-1 rounded-xl bg-white/20 text-[11px] font-bold">${net >= 0 ? 'Surplus' : 'Defisit'}</span>
          </div>
        `;

        const typeFilter = document.getElementById('txFilterType').value;
        const list = monthTxs
          .filter(t => typeFilter === 'all' || t.type === typeFilter)
          .sort((a, b) => b.date.localeCompare(a.date));

        const tbody = document.getElementById('txTableBody');
        if (list.length === 0) {
          tbody.innerHTML = `<tr><td colspan="4" class="px-3 py-6 text-center text-slate-400">Belum ada transaksi di bulan ini.</td></tr>`;
        } else {
          tbody.innerHTML = list.map(tx => `
            <tr class="hover:bg-slate-50">
              <td class="px-3 py-2.5 whitespace-nowrap text-slate-500 text-[11px]">${tx.date.slice(8, 10)}/${tx.date.slice(5, 7)}</td>
              <td class="px-3 py-2.5">
                <p class="font-bold text-slate-800">${tx.category}</p>
                <p class="text-[11px] text-slate-500">${tx.desc}</p>
              </td>
              <td class="px-3 py-2.5 text-right font-extrabold ${tx.type === 'pemasukan' ? 'text-emerald-600' : 'text-rose-600'} whitespace-nowrap">
                ${tx.type === 'pemasukan' ? '+' : '-'}${formatRp(tx.amount)}
              </td>
              <td class="px-3 py-2.5 text-center">
                <button type="button" data-del-tx="${tx.id}" class="text-[11px] font-bold text-rose-500 hover:text-rose-700 px-1.5 py-1">✕</button>
              </td>
            </tr>
          `).join('');
        }

        renderFinanceChart();
      }

      function renderFinanceChart() {
        const canvas = document.getElementById('financeChart');
        if (!canvas || typeof Chart === 'undefined') return;

        const [ay, am] = state.activeMonth.split('-').map(Number);
        const labels = [];
        const inData = [];
        const outData = [];

        for (let i = 5; i >= 0; i--) {
          const d = new Date(ay, am - 1 - i, 1);
          const mKey = `${d.getFullYear()}-${String(d.getMonth() + 1).padStart(2, '0')}`;
          labels.push(`${MONTH_NAMES[d.getMonth()].slice(0, 3)}`);
          const txs = state.transactions.filter(t => t.date && t.date.startsWith(mKey));
          inData.push(txs.filter(t => t.type === 'pemasukan').reduce((s, t) => s + Number(t.amount || 0), 0));
          outData.push(txs.filter(t => t.type === 'pengeluaran').reduce((s, t) => s + Number(t.amount || 0), 0));
        }

        if (chartInstance) chartInstance.destroy();
        chartInstance = new Chart(canvas.getContext('2d'), {
          type: 'bar',
          data: {
            labels,
            datasets: [
              { label: 'Masuk', data: inData, backgroundColor: '#10b981', borderRadius: 6 },
              { label: 'Keluar', data: outData, backgroundColor: '#f43f5e', borderRadius: 6 }
            ]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: { legend: { position: 'bottom' } },
            scales: {
              y: {
                beginAtZero: true,
                ticks: { callback: (v) => (v / 1000000).toFixed(1) + 'jt' }
              }
            }
          }
        });
      }

      function renderHistory() {
        const q = document.getElementById('historySearchInput').value.trim().toLowerCase();
        const list = state.history.filter(h => !q || `${h.roomName} ${h.tenantName}`.toLowerCase().includes(q));
        const tbody = document.getElementById('historyTableBody');
        if (list.length === 0) {
          tbody.innerHTML = `<tr><td colspan="4" class="px-3 py-6 text-center text-slate-400">Belum ada riwayat masuk/keluar penghuni.</td></tr>`;
          return;
        }
        tbody.innerHTML = list.map(h => `
          <tr class="hover:bg-slate-50">
            <td class="px-3 py-2.5">
              <p class="font-extrabold text-slate-900">${h.roomName}</p>
              <p class="font-semibold text-slate-700">${h.tenantName}</p>
              <p class="text-[10px] text-slate-400">${h.phone || '-'}</p>
            </td>
            <td class="px-3 py-2.5 text-[11px]">
              <p class="text-emerald-700 font-medium">Masuk: ${formatDateID(h.checkIn)}</p>
              <p class="text-rose-600 font-medium">Keluar: ${h.checkOut ? formatDateID(h.checkOut) : 'Masih Kos'}</p>
            </td>
            <td class="px-3 py-2.5">
              <span class="px-2 py-0.5 rounded-full text-[10px] font-bold ${h.status === 'Aktif' ? 'bg-blue-100 text-blue-700' : 'bg-slate-200 text-slate-700'}">${h.status}</span>
            </td>
            <td class="px-3 py-2.5 text-center">
              <button type="button" data-hist-stmt="${h.id}" class="px-2.5 py-1.5 rounded-xl bg-indigo-50 border border-indigo-200 text-indigo-700 font-bold text-[11px]">📋 Rincian</button>
            </td>
          </tr>
        `).join('');
      }

      // Open Full Room Statement (Mulai Awal Masuk Sampai Keluar Kos + Bukti Transfer)
      function openRoomStatementModal(roomId, historyId) {
        let room = roomId ? state.rooms.find(r => r.id === roomId) : null;
        let histItem = historyId ? state.history.find(h => h.id === historyId) : null;

        if (!room && histItem) {
          room = state.rooms.find(r => r.id === histItem.roomId || r.name === histItem.roomName);
        }

        const roomName = room ? room.name : (histItem ? histItem.roomName : 'Kamar');
        const tenantName = (histItem && histItem.tenantName) || (room && room.tenantName) || 'Belum Ada Penghuni Aktif';
        const phone = (histItem && histItem.phone) || (room && room.phone) || '-';
        const checkIn = (histItem && histItem.checkIn) || (room && room.checkIn) || '';
        const checkOut = (histItem && histItem.checkOut) || (room && room.checkOut) || '';
        const price = (room && room.price) || (histItem && histItem.price) || 0;

        // Collect all payments & transfer proofs for this room
        const paymentRecords = [];
        if (room && room.proofs) {
          Object.keys(room.proofs).sort().forEach(mKey => {
            const p = room.proofs[mKey];
            paymentRecords.push({
              month: mKey,
              date: p.date || `${mKey}-01`,
              amount: p.amount || room.price,
              method: p.method || 'Transfer Bank',
              note: p.note || '',
              image: p.image || '',
              tenantName: p.tenantName || room.tenantName
            });
          });
        } else if (histItem && Array.isArray(histItem.payments)) {
          histItem.payments.forEach(p => paymentRecords.push(p));
        }

        const totalPaid = paymentRecords.reduce((sum, p) => sum + Number(p.amount || 0), 0);

        // Calculate stay duration months list from checkIn to checkOut (or activeMonth)
        const stayMonths = [];
        if (checkIn) {
          const startYm = checkIn.slice(0, 7);
          const endYm = checkOut ? checkOut.slice(0, 7) : state.activeMonth;
          let [cy, cm] = startYm.split('-').map(Number);
          const [ey, em] = endYm.split('-').map(Number);
          let guard = 0;
          while ((cy < ey || (cy === ey && cm <= em)) && guard < 36) {
            stayMonths.push(`${cy}-${String(cm).padStart(2, '0')}`);
            cm++;
            if (cm > 12) { cm = 1; cy++; }
            guard++;
          }
        }

        document.getElementById('stmtTitle').textContent = `Rincian ${roomName} — ${state.kostName}`;
        document.getElementById('stmtSubtitle').textContent = `Rekap Lengkap Mulai Awal Masuk s/d Keluar Kos & Bukti Transfer`;

        const body = document.getElementById('stmtBody');
        body.innerHTML = `
          <!-- Header Card Penghuni -->
          <div class="bg-slate-900 text-white p-4 rounded-2xl space-y-2">
            <div class="flex items-center justify-between">
              <span class="px-2.5 py-0.5 rounded-full bg-white/15 text-[10px] font-bold uppercase">${roomName}</span>
              <span class="text-xs font-bold ${checkOut ? 'text-rose-300' : 'text-emerald-300'}">${checkOut ? 'Sudah Keluar (Check-Out)' : 'Penghuni Aktif'}</span>
            </div>
            <div>
              <h4 class="text-base font-extrabold">${tenantName}</h4>
              <p class="text-[11px] text-slate-300">No. HP: ${phone} • Tarif: ${formatRp(price)}/bulan</p>
            </div>
            <div class="grid grid-cols-2 gap-2 pt-2 border-t border-white/15 text-[11px]">
              <div>
                <span class="text-slate-400 block">Tanggal Awal Masuk:</span>
                <strong class="text-emerald-300">${checkIn ? formatDateID(checkIn) : '-'}</strong>
              </div>
              <div>
                <span class="text-slate-400 block">Tanggal Keluar Kos:</span>
                <strong class="text-rose-300">${checkOut ? formatDateID(checkOut) : 'Masih Menghuni'}</strong>
              </div>
            </div>
          </div>

          <!-- Ringkasan Total Pembayaran -->
          <div class="grid grid-cols-2 gap-2.5">
            <div class="bg-emerald-50 border border-emerald-200 p-3 rounded-xl">
              <span class="text-[10px] font-bold text-emerald-700 uppercase">Total Bulan Lunas</span>
              <p class="text-base font-extrabold text-emerald-800 mt-0.5">${paymentRecords.length} Bulan</p>
            </div>
            <div class="bg-blue-50 border border-blue-200 p-3 rounded-xl">
              <span class="text-[10px] font-bold text-blue-700 uppercase">Total Uang Masuk</span>
              <p class="text-base font-extrabold text-blue-800 mt-0.5">${formatRp(totalPaid)}</p>
            </div>
          </div>

          <!-- Timeline Per Bulan Mulai Awal Masuk s/d Sekarang/Keluar -->
          <div class="space-y-2">
            <h5 class="font-extrabold text-slate-900 text-xs">📅 Rincian Bulanan (Mulai Awal Masuk s/d Keluar)</h5>
            ${stayMonths.length === 0 ? `<p class="text-slate-400 text-xs">Belum ada rentang tanggal masuk.</p>` : stayMonths.map(ym => {
              const rec = paymentRecords.find(p => p.month === ym);
              return `
                <div class="p-3 rounded-xl border ${rec ? 'border-emerald-200 bg-emerald-50/50' : 'border-rose-200 bg-rose-50/40'} flex items-center justify-between gap-2">
                  <div class="flex items-center gap-2.5 min-w-0">
                    ${rec && rec.image ? `
                      <img src="${rec.image}" alt="Bukti" class="w-11 h-11 rounded-lg object-cover border border-emerald-300 shrink-0" />
                    ` : `
                      <div class="w-11 h-11 rounded-lg ${rec ? 'bg-emerald-200 text-emerald-800' : 'bg-rose-200 text-rose-700'} flex items-center justify-center font-bold text-xs shrink-0">
                        ${rec ? '✓' : '!'}
                      </div>
                    `}
                    <div class="min-w-0">
                      <p class="font-extrabold text-slate-900">${formatMonthID(ym)}</p>
                      ${rec ? `
                        <p class="text-[11px] text-emerald-800 font-semibold">${formatRp(rec.amount)} • ${rec.method}</p>
                        <p class="text-[10px] text-slate-500 truncate">Dibayar: ${formatDateID(rec.date)} ${rec.note ? '• ' + rec.note : ''}</p>
                      ` : `
                        <p class="text-[11px] text-rose-600 font-semibold">Belum tercatat pembayaran / bukti transfer</p>
                      `}
                    </div>
                  </div>
                  <span class="px-2 py-1 rounded-lg text-[10px] font-extrabold shrink-0 ${rec ? 'bg-emerald-600 text-white' : 'bg-rose-100 text-rose-700'}">
                    ${rec ? 'LUNAS' : 'BELUM'}
                  </span>
                </div>
              `;
            }).join('')}
          </div>

          <!-- Riwayat Penghuni Kamar Ini Sebelumnya -->
          <div class="space-y-2 pt-2 border-t border-slate-200">
            <h5 class="font-extrabold text-slate-900 text-xs">🗂️ Log Riwayat Penghuni di ${roomName}</h5>
            ${state.history.filter(h => h.roomName === roomName).map(h => `
              <div class="p-2.5 rounded-xl bg-slate-50 border border-slate-200 flex items-center justify-between">
                <div>
                  <p class="font-bold text-slate-800">${h.tenantName} <span class="text-[10px] font-normal text-slate-500">(${h.phone || '-'})</span></p>
                  <p class="text-[11px] text-slate-600">Masuk: ${formatDateID(h.checkIn)} — Keluar: ${h.checkOut ? formatDateID(h.checkOut) : 'Masih Aktif'}</p>
                </div>
                <span class="px-2 py-0.5 rounded-full text-[10px] font-bold ${h.status === 'Aktif' ? 'bg-blue-100 text-blue-700' : 'bg-slate-200 text-slate-700'}">${h.status}</span>
              </div>
            `).join('') || '<p class="text-slate-400">Belum ada log riwayat.</p>'}
          </div>
        `;

        document.getElementById('modalRoomStatement').classList.remove('hidden');
      }

      // Open Payment & Proof Modal
      function openPaymentProofModal(roomId) {
        const room = state.rooms.find(r => r.id === roomId);
        if (!room || !room.tenantName) return;
        const existing = (room.proofs && room.proofs[state.activeMonth]) || null;

        document.getElementById('ppRoomId').value = room.id;
        document.getElementById('ppRoomLabel').textContent = `${room.name} — ${room.tenantName}`;
        document.getElementById('ppMonthLabel').textContent = `Periode Sewa: ${formatMonthID(state.activeMonth)}`;
        document.getElementById('ppAmountLabel').textContent = formatRp(room.price);
        document.getElementById('ppDateInput').value = existing ? existing.date : todayISO();
        document.getElementById('ppMethodInput').value = existing ? existing.method : 'Transfer Bank';
        document.getElementById('ppNoteInput').value = existing ? (existing.note || '') : `Transfer Sewa ${room.name} a.n ${room.tenantName}`;

        pendingReceiptDataUrl = existing ? (existing.image || '') : '';
        const preview = document.getElementById('ppPreviewImg');
        const placeholder = document.getElementById('ppUploadPlaceholder');
        if (pendingReceiptDataUrl) {
          preview.src = pendingReceiptDataUrl;
          preview.classList.remove('hidden');
          placeholder.textContent = '📷 Klik untuk mengganti foto bukti transfer';
        } else {
          preview.src = '';
          preview.classList.add('hidden');
          placeholder.textContent = '📷 Klik untuk pilih foto struk / screenshot bukti transfer dari HP/Laptop';
        }

        document.getElementById('modalPaymentProof').classList.remove('hidden');
      }

      function switchTab(tabName) {
        currentTab = tabName;
        document.querySelectorAll('.tab-panel').forEach(p => p.classList.add('hidden'));
        document.getElementById(`tab-${tabName}`).classList.remove('hidden');

        document.querySelectorAll('.mobile-nav-btn').forEach(btn => {
          const active = btn.dataset.tab === tabName;
          btn.classList.toggle('text-blue-600', active);
          btn.classList.toggle('font-bold', active);
          btn.classList.toggle('text-slate-500', !active);
        });

        if (tabName === 'keuangan') renderFinanceChart();
      }

      function openTenantModal(roomId) {
        const modal = document.getElementById('modalTenant');
        const room = roomId ? state.rooms.find(r => r.id === roomId) : null;
        document.getElementById('tfRoomId').value = room ? room.id : '';
        document.getElementById('modalTenantTitle').textContent = room ? `Edit ${room.name}` : 'Tambah Kamar Baru';
        document.getElementById('tfRoomName').value = room ? room.name : `Kamar ${String(state.rooms.length + 1).padStart(2, '0')}`;
        document.getElementById('tfRentPrice').value = room ? room.price : 1200000;
        document.getElementById('tfTenantName').value = room ? room.tenantName : '';
        document.getElementById('tfTenantPhone').value = room ? room.phone : '';
        document.getElementById('tfCheckIn').value = room && room.checkIn ? room.checkIn : todayISO();
        document.getElementById('tfCheckOut').value = room ? (room.checkOut || '') : '';
        document.getElementById('tfDueDay').value = room && room.dueDay ? room.dueDay : new Date().getDate();
        document.getElementById('tfAlarmEnabled').checked = room ? room.alarmEnabled !== false : true;
        document.getElementById('tfPayFirstMonth').checked = false;
        modal.classList.remove('hidden');
      }

      // Event Listeners
      document.querySelectorAll('[data-tab]').forEach(btn => {
        btn.addEventListener('click', () => switchTab(btn.dataset.tab));
      });

      document.getElementById('btnToggleViewMode').addEventListener('click', () => {
        state.viewMode = state.viewMode === 'hp' ? 'wide' : 'hp';
        saveState();
        applyBrandingAndTheme();
      });

      const GAS_BACKEND_CODE = [
        "// === KODE BACKEND GOOGLE APPS SCRIPT KOSTKU PRO ===",
        "function doGet(e) {",
        "  var prop = PropertiesService.getScriptProperties().getProperty('KOSTKU_STATE');",
        "  var out = prop ? prop : JSON.stringify({ status: 'empty' });",
        "  return ContentService.createTextOutput(out).setMimeType(ContentService.MimeType.JSON);",
        "}",
        "",
        "function doPost(e) {",
        "  var raw = e.postData.contents;",
        "  var body = JSON.parse(raw);",
        "  var st = body.state || body;",
        "  PropertiesService.getScriptProperties().setProperty('KOSTKU_STATE', JSON.stringify({ state: st, updatedAt: new Date().toISOString() }));",
        "  var ss = SpreadsheetApp.getActiveSpreadsheet();",
        "  if (ss && st.rooms) {",
        "    var sh1 = ss.getSheetByName('Kamar') || ss.insertSheet('Kamar');",
        "    sh1.clearContents();",
        "    sh1.appendRow(['Kamar', 'Penghuni', 'No HP', 'Harga/Bln', 'Tgl Masuk', 'Tgl Keluar', 'Tempo Tgl', 'Bulan Lunas']);",
        "    st.rooms.forEach(function(r) {",
        "      sh1.appendRow([r.name, r.tenantName || 'Kosong', r.phone || '-', r.price, r.checkIn || '-', r.checkOut || '-', r.dueDay, (r.paidMonths || []).join(', ')]);",
        "    });",
        "    var sh2 = ss.getSheetByName('Keuangan') || ss.insertSheet('Keuangan');",
        "    sh2.clearContents();",
        "    sh2.appendRow(['Tanggal', 'Tipe', 'Kategori', 'Keterangan', 'Nominal']);",
        "    (st.transactions || []).forEach(function(t) {",
        "      sh2.appendRow([t.date, t.type, t.category, t.desc, t.amount]);",
        "    });",
        "  }",
        "  return ContentService.createTextOutput(JSON.stringify({ status: 'ok' })).setMimeType(ContentService.MimeType.JSON);",
        "}"
      ].join('\n');

      function openSettingsWithBranding() {
        document.getElementById('brandNameInput').value = state.kostName || '';
        document.getElementById('brandSubInput').value = state.kostSubtitle || '';
        document.getElementById('cloudScriptUrlInput').value = cloudConfig.scriptUrl || '';
        document.getElementById('cloudAutoSyncCheck').checked = cloudConfig.autoSync !== false;
        document.getElementById('gasCodeTextarea').value = GAS_BACKEND_CODE;
        updateCloudStatusUI();
        document.getElementById('modalSettings').classList.remove('hidden');
      }

      document.getElementById('btnEditBranding').addEventListener('click', openSettingsWithBranding);
      document.getElementById('btnQuickEditBrand').addEventListener('click', openSettingsWithBranding);
      document.getElementById('btnSettingsModal').addEventListener('click', openSettingsWithBranding);
      document.getElementById('btnCloudIndicator').addEventListener('click', openSettingsWithBranding);

      document.getElementById('btnSaveCloudUrl').addEventListener('click', () => {
        cloudConfig.scriptUrl = document.getElementById('cloudScriptUrlInput').value.trim();
        cloudConfig.autoSync = document.getElementById('cloudAutoSyncCheck').checked;
        saveCloudConfig();
        if (cloudConfig.scriptUrl) {
          pushStateToCloud(false);
        } else {
          showToast('Mode Cloud dinonaktifkan (kembali ke penyimpanan lokal).');
        }
      });

      document.getElementById('cloudAutoSyncCheck').addEventListener('change', (e) => {
        cloudConfig.autoSync = e.target.checked;
        saveCloudConfig();
      });

      document.getElementById('btnCloudPushNow').addEventListener('click', () => pushStateToCloud(false));
      document.getElementById('btnCloudPullNow').addEventListener('click', () => pullStateFromCloud(false));

      document.getElementById('btnCopyShareCloudLink').addEventListener('click', () => {
        const urlVal = document.getElementById('cloudScriptUrlInput').value.trim() || cloudConfig.scriptUrl;
        if (!urlVal) {
          showToast('Simpan URL Google Apps Script terlebih dahulu sebelum menyalin link HP.');
          return;
        }
        const base = window.location.href.split('?')[0];
        const shareUrl = `${base}?cloud=${encodeURIComponent(urlVal)}`;
        const temp = document.createElement('textarea');
        temp.value = shareUrl;
        document.body.appendChild(temp);
        temp.select();
        document.execCommand('copy');
        document.body.removeChild(temp);
        showToast('📲 Link otomatis konek HP berhasil disalin!');
      });

      document.getElementById('btnToggleGasGuide').addEventListener('click', () => {
        document.getElementById('gasGuideBox').classList.toggle('hidden');
      });

      document.getElementById('btnCopyGasCode').addEventListener('click', () => {
        const area = document.getElementById('gasCodeTextarea');
        area.select();
        document.execCommand('copy');
        showToast('📋 Kode Google Apps Script berhasil disalin!');
      });

      document.getElementById('brandLogoInput').addEventListener('change', (e) => {
        const file = e.target.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = () => {
          state.kostLogo = reader.result;
          saveState();
          applyBrandingAndTheme();
          showToast('Logo kost berhasil diperbarui!');
        };
        reader.readAsDataURL(file);
      });

      document.getElementById('btnRemoveLogo').addEventListener('click', () => {
        state.kostLogo = '';
        saveState();
        applyBrandingAndTheme();
        showToast('Logo dikembalikan ke standar.');
      });

      document.getElementById('btnSaveBranding').addEventListener('click', () => {
        const nameVal = document.getElementById('brandNameInput').value.trim();
        const subVal = document.getElementById('brandSubInput').value.trim();
        if (nameVal) state.kostName = nameVal;
        if (subVal) state.kostSubtitle = subVal;
        saveState();
        applyBrandingAndTheme();
        document.getElementById('modalSettings').classList.add('hidden');
        showToast('Nama & identitas kost berhasil disimpan!');
      });

      document.querySelectorAll('[data-set-theme]').forEach(btn => {
        btn.addEventListener('click', () => {
          state.theme = btn.dataset.setTheme;
          saveState();
          applyBrandingAndTheme();
          showToast('Tema warna aplikasi diperbarui!');
        });
      });

      document.getElementById('monthPicker').addEventListener('change', (e) => {
        if (e.target.value) {
          state.activeMonth = e.target.value;
          saveState();
          renderAll();
        }
      });

      document.getElementById('btnAddRoomTop').addEventListener('click', () => openTenantModal(null));

      document.querySelectorAll('.room-filter-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          currentRoomFilter = btn.dataset.roomFilter;
          document.querySelectorAll('.room-filter-btn').forEach(b => {
            const active = b.dataset.roomFilter === currentRoomFilter;
            b.className = active
              ? 'room-filter-btn px-3 py-1.5 rounded-xl text-[11px] font-bold bg-slate-900 text-white whitespace-nowrap'
              : 'room-filter-btn px-3 py-1.5 rounded-xl text-[11px] font-semibold bg-white border border-slate-200 text-slate-700 whitespace-nowrap';
          });
          renderRooms();
        });
      });

      document.getElementById('roomSearchInput').addEventListener('input', renderRooms);
      document.getElementById('historySearchInput').addEventListener('input', renderHistory);

      // Room Grid Actions
      document.getElementById('roomsGrid').addEventListener('click', (e) => {
        const btn = e.target.closest('[data-action]');
        if (!btn) return;
        const id = btn.dataset.id;
        const action = btn.dataset.action;
        const room = state.rooms.find(r => r.id === id);
        if (!room) return;

        if (action === 'edit' || action === 'checkin') {
          openTenantModal(id);
        } else if (action === 'pay') {
          openPaymentProofModal(id);
        } else if (action === 'view-proof') {
          const p = room.proofs && room.proofs[state.activeMonth];
          if (!p) return;
          document.getElementById('vrTitle').textContent = `Bukti Transfer ${room.name} (${formatMonthID(state.activeMonth)})`;
          document.getElementById('vrImage').src = p.image || createSampleReceiptDataUri(room.name, room.tenantName, state.activeMonth, room.price);
          document.getElementById('vrMeta').innerHTML = `
            <p><strong>Penghuni:</strong> ${room.tenantName}</p>
            <p><strong>Tanggal Bayar:</strong> ${formatDateID(p.date)}</p>
            <p><strong>Metode:</strong> ${p.method}</p>
            <p><strong>Catatan:</strong> ${p.note || '-'}</p>
          `;
          document.getElementById('modalViewReceipt').classList.remove('hidden');
        } else if (action === 'statement') {
          openRoomStatementModal(id, null);
        } else if (action === 'unpay') {
          room.paidMonths = (room.paidMonths || []).filter(m => m !== state.activeMonth);
          if (room.proofs) delete room.proofs[state.activeMonth];
          saveState();
          renderAll();
          showToast(`Status lunas ${room.name} dibatalkan.`);
        } else if (action === 'checkout') {
          document.getElementById('coRoomId').value = id;
          document.getElementById('coSummaryText').textContent = `Penghuni ${room.tenantName} di ${room.name} akan dicatat keluar kos. Seluruh rincian dari awal masuk sampai keluar tetap tersimpan di Riwayat.`;
          document.getElementById('coDateInput').value = todayISO();
          document.getElementById('modalCheckout').classList.remove('hidden');
        } else if (action === 'delete-room') {
          state.rooms = state.rooms.filter(r => r.id !== id);
          saveState();
          renderAll();
          showToast(`${room.name} dihapus.`);
        }
      });

      // Receipt file input inside modalPaymentProof
      document.getElementById('ppFileInput').addEventListener('change', (e) => {
        const file = e.target.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = () => {
          pendingReceiptDataUrl = reader.result;
          const preview = document.getElementById('ppPreviewImg');
          preview.src = pendingReceiptDataUrl;
          preview.classList.remove('hidden');
          document.getElementById('ppUploadPlaceholder').textContent = '📷 Foto bukti siap disimpan (Klik untuk ganti)';
        };
        reader.readAsDataURL(file);
      });

      // Submit Payment & Proof Form
      document.getElementById('paymentProofForm').addEventListener('submit', (e) => {
        e.preventDefault();
        const roomId = document.getElementById('ppRoomId').value;
        const room = state.rooms.find(r => r.id === roomId);
        if (!room) return;

        const payDate = document.getElementById('ppDateInput').value || todayISO();
        const method = document.getElementById('ppMethodInput').value;
        const note = document.getElementById('ppNoteInput').value.trim();
        const finalImg = pendingReceiptDataUrl || createSampleReceiptDataUri(room.name, room.tenantName, state.activeMonth, room.price);

        if (!Array.isArray(room.paidMonths)) room.paidMonths = [];
        const alreadyPaid = room.paidMonths.includes(state.activeMonth);
        if (!alreadyPaid) room.paidMonths.push(state.activeMonth);

        if (!room.proofs) room.proofs = {};
        room.proofs[state.activeMonth] = {
          date: payDate,
          method,
          note,
          image: finalImg,
          amount: Number(room.price),
          tenantName: room.tenantName
        };

        // Sync to active history record
        const hist = state.history.find(h => (h.roomId === room.id || h.roomName === room.name) && h.tenantName === room.tenantName && h.status === 'Aktif');
        if (hist) {
          if (!Array.isArray(hist.payments)) hist.payments = [];
          const existingIdx = hist.payments.findIndex(p => p.month === state.activeMonth);
          const payObj = { month: state.activeMonth, date: payDate, amount: Number(room.price), method, note, image: finalImg };
          if (existingIdx >= 0) hist.payments[existingIdx] = payObj;
          else hist.payments.push(payObj);
        }

        if (!alreadyPaid) {
          state.transactions.unshift({
            id: 'tx_' + Date.now(),
            date: payDate,
            type: 'pemasukan',
            category: 'Sewa Kamar',
            amount: Number(room.price),
            desc: `Sewa ${room.name} - ${room.tenantName} (${method})`,
            roomId: room.id
          });
        }

        saveState();
        renderAll();
        document.getElementById('modalPaymentProof').classList.add('hidden');
        showToast(`Pembayaran & bukti transfer ${room.name} tersimpan!`);
      });

      // Print Statement Button
      document.getElementById('btnPrintStatement').addEventListener('click', () => {
        document.body.classList.add('print-stmt-mode');
        window.print();
        setTimeout(() => document.body.classList.remove('print-stmt-mode'), 500);
      });

      function getMonthlyFinanceData() {
        const monthTxs = state.transactions
          .filter(t => t.date && t.date.startsWith(state.activeMonth))
          .sort((a, b) => a.date.localeCompare(b.date));
        const totalIn = monthTxs.filter(t => t.type === 'pemasukan').reduce((s, t) => s + Number(t.amount || 0), 0);
        const totalOut = monthTxs.filter(t => t.type === 'pengeluaran').reduce((s, t) => s + Number(t.amount || 0), 0);
        const net = totalIn - totalOut;
        return { monthTxs, totalIn, totalOut, net };
      }

      function openFinanceReportModal() {
        const { monthTxs, totalIn, totalOut, net } = getMonthlyFinanceData();
        const periodName = formatMonthID(state.activeMonth);
        const container = document.getElementById('financeReportBody');

        const roomRowsHtml = state.rooms.map((r, idx) => {
          const due = getRoomDueInfo(r, state.activeMonth);
          const proof = (r.proofs && r.proofs[state.activeMonth]) || null;
          return `
            <tr class="border-b border-slate-200">
              <td class="py-2 px-2.5 font-bold text-slate-900">${idx + 1}. ${r.name}</td>
              <td class="py-2 px-2.5 font-semibold text-slate-800">${r.tenantName || '<span class="text-slate-400 italic">Kosong</span>'}</td>
              <td class="py-2 px-2.5 font-bold text-blue-700">${r.phone || '-'}</td>
              <td class="py-2 px-2.5 text-right font-semibold">${formatRp(r.price)}</td>
              <td class="py-2 px-2.5 text-center">
                <span class="px-2 py-0.5 rounded-full text-[10px] font-bold ${due.status === 'lunas' ? 'bg-emerald-100 text-emerald-800' : (r.tenantName ? 'bg-rose-100 text-rose-700' : 'bg-slate-100 text-slate-500')}">
                  ${r.tenantName ? (due.status === 'lunas' ? 'LUNAS' : 'BELUM BAYAR') : 'KOSONG'}
                </span>
              </td>
              <td class="py-2 px-2.5 text-[11px] text-slate-600">${proof ? `${formatDateID(proof.date)} (${proof.method})` : '-'}</td>
            </tr>
          `;
        }).join('');

        const txRowsHtml = monthTxs.length === 0
          ? `<tr><td colspan="5" class="py-4 text-center text-slate-400">Belum ada transaksi tercatat pada periode ${periodName}.</td></tr>`
          : monthTxs.map((tx, idx) => `
            <tr class="border-b border-slate-200">
              <td class="py-2 px-2.5 text-slate-600">${idx + 1}</td>
              <td class="py-2 px-2.5 whitespace-nowrap">${formatDateID(tx.date)}</td>
              <td class="py-2 px-2.5 font-semibold text-slate-800">${tx.category}</td>
              <td class="py-2 px-2.5 text-slate-700">${tx.desc}</td>
              <td class="py-2 px-2.5 text-right font-bold ${tx.type === 'pemasukan' ? 'text-emerald-700' : 'text-rose-600'} whitespace-nowrap">
                ${tx.type === 'pemasukan' ? '+' : '-'} ${formatRp(tx.amount)}
              </td>
            </tr>
          `).join('');

        container.innerHTML = `
          <div class="border-b-2 border-slate-800 pb-3 flex items-center justify-between gap-3">
            <div class="flex items-center gap-3">
              ${state.kostLogo ? `<img src="${state.kostLogo}" class="w-12 h-12 rounded-xl object-cover border border-slate-300" />` : ''}
              <div>
                <h2 class="text-base font-extrabold text-slate-900 uppercase">${state.kostName || 'KostKu Pro'}</h2>
                <p class="text-[11px] text-slate-600">${state.kostSubtitle || 'Laporan Resmi Rincian Keuangan & Status Kamar'}</p>
              </div>
            </div>
            <div class="text-right">
              <span class="text-[10px] font-bold uppercase text-slate-400 block">Periode Laporan</span>
              <strong class="text-sm font-extrabold text-slate-900">${periodName}</strong>
              <span class="text-[10px] text-slate-500 block">Dicetak: ${formatDateID(todayISO())}</span>
            </div>
          </div>

          <!-- Ringkasan Eksekutif Keuangan -->
          <div class="grid grid-cols-3 gap-2.5">
            <div class="p-3 rounded-xl bg-emerald-50 border border-emerald-200">
              <span class="text-[10px] font-bold uppercase text-emerald-700">Total Pemasukan</span>
              <p class="text-sm font-extrabold text-emerald-800 mt-0.5">${formatRp(totalIn)}</p>
            </div>
            <div class="p-3 rounded-xl bg-rose-50 border border-rose-200">
              <span class="text-[10px] font-bold uppercase text-rose-700">Total Pengeluaran</span>
              <p class="text-sm font-extrabold text-rose-700 mt-0.5">${formatRp(totalOut)}</p>
            </div>
            <div class="p-3 rounded-xl bg-blue-50 border border-blue-200">
              <span class="text-[10px] font-bold uppercase text-blue-700">Saldo Bersih (Laba)</span>
              <p class="text-sm font-extrabold text-blue-900 mt-0.5">${formatRp(net)}</p>
            </div>
          </div>

          <!-- Tabel 1: Status Pembayaran Kamar & Nomor Telepon Penghuni -->
          <div class="space-y-1.5">
            <h4 class="text-xs font-extrabold text-slate-900 uppercase tracking-wide">1. Rincian Sewa Per Kamar &amp; Nomor Telepon Penghuni (${periodName})</h4>
            <div class="overflow-x-auto border border-slate-200 rounded-xl">
              <table class="w-full text-left border-collapse text-[11px]">
                <thead class="bg-slate-100 text-slate-700 font-bold uppercase">
                  <tr>
                    <th class="py-2 px-2.5">Kamar</th>
                    <th class="py-2 px-2.5">Nama Penghuni</th>
                    <th class="py-2 px-2.5">No. Telp / HP</th>
                    <th class="py-2 px-2.5 text-right">Harga/Bln</th>
                    <th class="py-2 px-2.5 text-center">Status</th>
                    <th class="py-2 px-2.5">Tgl Bayar &amp; Metode</th>
                  </tr>
                </thead>
                <tbody>${roomRowsHtml}</tbody>
              </table>
            </div>
          </div>

          <!-- Tabel 2: Rincian Transaksi Pemasukan & Pengeluaran -->
          <div class="space-y-1.5">
            <h4 class="text-xs font-extrabold text-slate-900 uppercase tracking-wide">2. Rincian Transaksi Kas Pemasukan &amp; Pengeluaran (${periodName})</h4>
            <div class="overflow-x-auto border border-slate-200 rounded-xl">
              <table class="w-full text-left border-collapse text-[11px]">
                <thead class="bg-slate-100 text-slate-700 font-bold uppercase">
                  <tr>
                    <th class="py-2 px-2.5">No</th>
                    <th class="py-2 px-2.5">Tanggal</th>
                    <th class="py-2 px-2.5">Kategori</th>
                    <th class="py-2 px-2.5">Keterangan Rincian</th>
                    <th class="py-2 px-2.5 text-right">Nominal</th>
                  </tr>
                </thead>
                <tbody>${txRowsHtml}</tbody>
                <tfoot class="bg-slate-50 font-extrabold text-slate-900 border-t-2 border-slate-300">
                  <tr>
                    <td colspan="4" class="py-2.5 px-2.5 text-right">TOTAL SALDO BERSIH (${periodName}):</td>
                    <td class="py-2.5 px-2.5 text-right text-blue-700">${formatRp(net)}</td>
                  </tr>
                </tfoot>
              </table>
            </div>
          </div>
        `;

        document.getElementById('modalFinanceReport').classList.remove('hidden');
      }

      function downloadFinancePdfFile() {
        const { monthTxs, totalIn, totalOut, net } = getMonthlyFinanceData();
        const periodName = formatMonthID(state.activeMonth);

        if (window.jspdf && window.jspdf.jsPDF) {
          const { jsPDF } = window.jspdf;
          const doc = new jsPDF({ orientation: 'portrait', unit: 'mm', format: 'a4' });

          doc.setFont('helvetica', 'bold');
          doc.setFontSize(15);
          doc.text((state.kostName || 'KOSTKU PRO').toUpperCase(), 14, 16);

          doc.setFont('helvetica', 'normal');
          doc.setFontSize(9.5);
          doc.text(`${state.kostSubtitle || 'Laporan Rincian Keuangan Bulanan'} | Periode: ${periodName}`, 14, 22);
          doc.text(`Tanggal Cetak: ${formatDateID(todayISO())}`, 14, 27);

          doc.setDrawColor(180);
          doc.line(14, 30, 196, 30);

          doc.setFont('helvetica', 'bold');
          doc.setFontSize(10);
          doc.text(`Total Pemasukan: ${formatRp(totalIn)}`, 14, 37);
          doc.text(`Total Pengeluaran: ${formatRp(totalOut)}`, 80, 37);
          doc.text(`Saldo Bersih: ${formatRp(net)}`, 145, 37);

          // Table 1: Rooms & Tenant Phone
          doc.setFontSize(10.5);
          doc.text(`1. Rincian Kamar & Nomor Telepon Penghuni (${periodName})`, 14, 46);

          const roomBody = state.rooms.map((r, i) => {
            const due = getRoomDueInfo(r, state.activeMonth);
            const proof = (r.proofs && r.proofs[state.activeMonth]) || null;
            return [
              `${i + 1}. ${r.name}`,
              r.tenantName || 'Kosong',
              r.phone || '-',
              formatRp(r.price),
              r.tenantName ? (due.status === 'lunas' ? 'LUNAS' : 'BELUM BAYAR') : 'KOSONG',
              proof ? `${formatDateID(proof.date)} (${proof.method})` : '-'
            ];
          });

          if (doc.autoTable) {
            doc.autoTable({
              startY: 49,
              head: [['Kamar', 'Nama Penghuni', 'No. Telp / HP', 'Harga Sewa', 'Status', 'Tgl Bayar & Metode']],
              body: roomBody,
              theme: 'grid',
              headStyles: { fillColor: [37, 99, 235], fontSize: 8.5 },
              styles: { fontSize: 8, cellPadding: 2 }
            });
          }

          const nextY = (doc.lastAutoTable && doc.lastAutoTable.finalY ? doc.lastAutoTable.finalY : 95) + 8;
          doc.setFont('helvetica', 'bold');
          doc.setFontSize(10.5);
          doc.text(`2. Rincian Transaksi Pemasukan & Pengeluaran (${periodName})`, 14, nextY);

          const txBody = monthTxs.map((tx, idx) => [
            String(idx + 1),
            formatDateID(tx.date),
            tx.category,
            tx.desc,
            tx.type === 'pemasukan' ? 'Pemasukan' : 'Pengeluaran',
            (tx.type === 'pemasukan' ? '+ ' : '- ') + formatRp(tx.amount)
          ]);

          if (doc.autoTable) {
            doc.autoTable({
              startY: nextY + 3,
              head: [['No', 'Tanggal', 'Kategori', 'Keterangan', 'Tipe', 'Nominal']],
              body: txBody.length ? txBody : [['-', '-', '-', 'Belum ada transaksi bulan ini', '-', 'Rp 0']],
              foot: [['', '', '', '', 'SALDO BERSIH', formatRp(net)]],
              theme: 'grid',
              headStyles: { fillColor: [16, 185, 129], fontSize: 8.5 },
              footStyles: { fillColor: [241, 245, 249], textColor: [15, 23, 42], fontStyle: 'bold', fontSize: 8.5 },
              styles: { fontSize: 8, cellPadding: 2 }
            });
          }

          doc.save(`Rincian_Keuangan_${(state.kostName || 'Kost').replace(/\s+/g, '_')}_${state.activeMonth}.pdf`);
          showToast('File PDF Rincian Keuangan berhasil diunduh!');
        } else {
          document.body.classList.add('print-finance-mode');
          window.print();
          setTimeout(() => document.body.classList.remove('print-finance-mode'), 500);
        }
      }

      document.getElementById('btnOpenFinancePdf').addEventListener('click', openFinanceReportModal);
      document.getElementById('btnOpenFinancePdfTop').addEventListener('click', openFinanceReportModal);
      document.getElementById('btnDownloadFinancePdf').addEventListener('click', downloadFinancePdfFile);
      document.getElementById('btnPrintFinanceReport').addEventListener('click', () => {
        document.body.classList.add('print-finance-mode');
        window.print();
        setTimeout(() => document.body.classList.remove('print-finance-mode'), 500);
      });

      // History Table Statement Click
      document.getElementById('historyTableBody').addEventListener('click', (e) => {
        const btn = e.target.closest('[data-hist-stmt]');
        if (!btn) return;
        openRoomStatementModal(null, btn.dataset.histStmt);
      });

      // Alarm List Actions
      document.getElementById('alarmListContainer').addEventListener('click', (e) => {
        const btn = e.target.closest('[data-alarm-action]');
        if (!btn) return;
        const id = btn.dataset.id;
        const action = btn.dataset.alarmAction;
        const room = state.rooms.find(r => r.id === id);
        if (!room) return;

        if (action === 'pay') {
          openPaymentProofModal(id);
          stopAlarmLoop();
        } else if (action === 'toggle') {
          room.alarmEnabled = !room.alarmEnabled;
          saveState();
          renderAll();
          showToast(`Alarm ${room.name} ${room.alarmEnabled ? 'diaktifkan' : 'dinonaktifkan'}.`);
        } else if (action === 'ics') {
          downloadICS(room, getRoomDueInfo(room, state.activeMonth));
        }
      });

      // Tenant Form Submit
      document.getElementById('tenantForm').addEventListener('submit', (e) => {
        e.preventDefault();
        const id = document.getElementById('tfRoomId').value;
        const name = document.getElementById('tfRoomName').value.trim();
        const price = Number(document.getElementById('tfRentPrice').value) || 0;
        const tenantName = document.getElementById('tfTenantName').value.trim();
        const phone = document.getElementById('tfTenantPhone').value.trim();
        const checkIn = tenantName ? document.getElementById('tfCheckIn').value : '';
        const checkOut = tenantName ? document.getElementById('tfCheckOut').value : '';
        let dueDay = parseInt(document.getElementById('tfDueDay').value, 10);
        if (!dueDay && checkIn) dueDay = parseInt(checkIn.split('-')[2], 10);
        dueDay = Math.min(31, Math.max(1, dueDay || 1));
        const alarmEnabled = document.getElementById('tfAlarmEnabled').checked;
        const payNow = document.getElementById('tfPayFirstMonth').checked;

        let targetRoom;
        if (id) {
          targetRoom = state.rooms.find(r => r.id === id);
          const wasEmpty = !targetRoom.tenantName;
          Object.assign(targetRoom, { name, price, tenantName, phone, checkIn, checkOut, dueDay, alarmEnabled });
          if (wasEmpty && tenantName) {
            state.history.unshift({ id: 'h_' + Date.now(), roomId: targetRoom.id, roomName: name, tenantName, phone, price, checkIn, checkOut, status: 'Aktif', payments: [] });
          }
        } else {
          targetRoom = { id: 'r_' + Date.now(), name, price, tenantName, phone, checkIn, checkOut, dueDay, alarmEnabled, paidMonths: [], proofs: {} };
          state.rooms.push(targetRoom);
          if (tenantName) {
            state.history.unshift({ id: 'h_' + Date.now(), roomId: targetRoom.id, roomName: name, tenantName, phone, price, checkIn, checkOut, status: 'Aktif', payments: [] });
          }
        }

        document.getElementById('modalTenant').classList.add('hidden');
        if (payNow && tenantName) {
          saveState();
          renderAll();
          openPaymentProofModal(targetRoom.id);
        } else {
          saveState();
          renderAll();
          showToast(`Data ${name} berhasil disimpan.`);
        }
      });

      // Check-Out Form Submit
      document.getElementById('checkoutForm').addEventListener('submit', (e) => {
        e.preventDefault();
        const id = document.getElementById('coRoomId').value;
        const coDate = document.getElementById('coDateInput').value;
        const room = state.rooms.find(r => r.id === id);
        if (!room) return;

        const hist = state.history.find(h => (h.roomId === room.id || h.roomName === room.name) && h.tenantName === room.tenantName && h.status === 'Aktif');
        const collectedPayments = room.proofs ? Object.keys(room.proofs).map(m => ({ month: m, ...room.proofs[m] })) : [];

        if (hist) {
          hist.checkOut = coDate;
          hist.status = 'Keluar';
          if (collectedPayments.length > 0) hist.payments = collectedPayments;
        } else {
          state.history.unshift({
            id: 'h_' + Date.now(),
            roomId: room.id,
            roomName: room.name,
            tenantName: room.tenantName,
            phone: room.phone,
            price: room.price,
            checkIn: room.checkIn,
            checkOut: coDate,
            status: 'Keluar',
            payments: collectedPayments
          });
        }

        room.tenantName = '';
        room.phone = '';
        room.checkIn = '';
        room.checkOut = '';
        room.paidMonths = [];
        room.proofs = {};

        saveState();
        renderAll();
        document.getElementById('modalCheckout').classList.add('hidden');
        showToast(`${room.name} kini kosong & rincian dari awal masuk s/d keluar tersimpan di Riwayat.`);
      });

      // Finance Form
      document.querySelectorAll('.tx-type-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          const t = btn.dataset.txType;
          document.getElementById('txTypeInput').value = t;
          document.querySelectorAll('.tx-type-btn').forEach(b => {
            const active = b.dataset.txType === t;
            if (active && t === 'pemasukan') b.className = 'tx-type-btn py-2 rounded-xl text-xs font-bold border border-emerald-500 bg-emerald-50 text-emerald-700 min-h-[40px]';
            else if (active && t === 'pengeluaran') b.className = 'tx-type-btn py-2 rounded-xl text-xs font-bold border border-rose-500 bg-rose-50 text-rose-700 min-h-[40px]';
            else b.className = 'tx-type-btn py-2 rounded-xl text-xs font-bold border border-slate-200 bg-white text-slate-600 min-h-[40px]';
          });
        });
      });

      document.getElementById('quickTxForm').addEventListener('submit', (e) => {
        e.preventDefault();
        const tx = {
          id: 'tx_' + Date.now(),
          type: document.getElementById('txTypeInput').value,
          date: document.getElementById('txDateInput').value,
          category: document.getElementById('txCategoryInput').value,
          amount: Number(document.getElementById('txAmountInput').value) || 0,
          desc: document.getElementById('txDescInput').value.trim(),
          roomId: ''
        };
        state.transactions.unshift(tx);
        saveState();
        renderFinance();
        document.getElementById('txAmountInput').value = '';
        document.getElementById('txDescInput').value = '';
        showToast('Transaksi keuangan tersimpan.');
      });

      document.getElementById('txFilterType').addEventListener('change', renderFinance);

      document.getElementById('txTableBody').addEventListener('click', (e) => {
        const btn = e.target.closest('[data-del-tx]');
        if (!btn) return;
        const id = btn.dataset.delTx;
        state.transactions = state.transactions.filter(t => t.id !== id);
        saveState();
        renderFinance();
        showToast('Transaksi dihapus.');
      });

      document.getElementById('btnExportCsv').addEventListener('click', () => {
        const rows = [['Tanggal', 'Tipe', 'Kategori', 'Keterangan', 'Nominal']];
        state.transactions.filter(t => t.date && t.date.startsWith(state.activeMonth)).forEach(t => {
          rows.push([t.date, t.type, t.category, `"${(t.desc || '').replace(/"/g, '""')}"`, t.amount]);
        });
        const csv = rows.map(r => r.join(',')).join('\n');
        const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = `Laporan_Keuangan_${state.kostName.replace(/\s+/g, '_')}_${state.activeMonth}.csv`;
        a.click();
        URL.revokeObjectURL(url);
      });

      document.getElementById('btnTestAlarm').addEventListener('click', startAlarmLoop);
      document.getElementById('btnStopAlarm').addEventListener('click', stopAlarmLoop);
      document.getElementById('btnBannerPlayAlarm').addEventListener('click', startAlarmLoop);
      document.getElementById('btnBannerViewAlarms').addEventListener('click', () => switchTab('alarm'));
      document.getElementById('btnAlarmSoundHeader').addEventListener('click', () => {
        switchTab('alarm');
        playAlarmBeep();
      });

      document.getElementById('btnRequestNotif').addEventListener('click', () => {
        if (!('Notification' in window)) {
          showToast('Browser ini tidak mendukung notifikasi sistem.');
          return;
        }
        Notification.requestPermission().then(perm => {
          if (perm === 'granted') {
            triggerBrowserNotification(`Alarm ${state.kostName} Aktif!`, 'Notifikasi jatuh tempo kamar siap mengingatkan Anda.');
            showToast('Notifikasi HP/Laptop berhasil diaktifkan!');
          } else {
            showToast('Izin notifikasi ditolak atau diblokir browser.');
          }
        });
      });

      document.getElementById('btnBackupJson').addEventListener('click', () => {
        const blob = new Blob([JSON.stringify(state, null, 2)], { type: 'application/json' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = `Backup_Kost_${todayISO()}.json`;
        a.click();
        URL.revokeObjectURL(url);
        showToast('File backup JSON berhasil diunduh.');
      });

      document.getElementById('inputRestoreJson').addEventListener('change', (e) => {
        const file = e.target.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = () => {
          try {
            const parsed = JSON.parse(reader.result);
            if (parsed && Array.isArray(parsed.rooms)) {
              state = parsed;
              saveState();
              renderAll();
              document.getElementById('modalSettings').classList.add('hidden');
              showToast('Data berhasil dipulihkan!');
            }
          } catch (err) {
            showToast('Format file backup tidak valid.');
          }
        };
        reader.readAsText(file);
      });

      document.getElementById('btnResetEmpty').addEventListener('click', () => {
        state.rooms = state.rooms.map(r => ({ ...r, tenantName: '', phone: '', checkIn: '', checkOut: '', paidMonths: [], proofs: {} }));
        state.transactions = [];
        state.history = [];
        saveState();
        renderAll();
        document.getElementById('modalSettings').classList.add('hidden');
        showToast('Semua data penghuni & transaksi dikosongkan.');
      });

      document.querySelectorAll('[data-close-modal]').forEach(btn => {
        btn.addEventListener('click', () => {
          document.getElementById(btn.dataset.closeModal).classList.add('hidden');
        });
      });

      // Live Clock on Phone Notch
      function updateNotchClock() {
        const d = new Date();
        const hh = String(d.getHours()).padStart(2, '0');
        const mm = String(d.getMinutes()).padStart(2, '0');
        const el = document.getElementById('liveClockNotch');
        if (el) el.textContent = `${hh}:${mm}`;
      }
      updateNotchClock();
      setInterval(updateNotchClock, 30000);

      document.getElementById('txDateInput').value = todayISO();
      renderAll();
      updateCloudStatusUI();
      if (cloudConfig.scriptUrl) {
        pullStateFromCloud(true);
      }
      setInterval(() => {
        if (cloudConfig.scriptUrl && cloudConfig.autoSync && !isSyncingCloud) {
          pullStateFromCloud(true);
        }
      }, 25000);
    })();
  </script>
</body>
</html>
