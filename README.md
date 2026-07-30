<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ระบบจัดการทุนการศึกษา - มูลนิธิเติมใจ เต็มสุข</title>
  
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Chart.js -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <!-- Lucide Icons -->
  <script src="https://unpkg.com/lucide@latest"></script>
  
  <!-- Google Fonts Sarabun -->
  <link href="https://fonts.googleapis.com/css2?family=Sarabun:wght@300;400;500;600;700&display=swap" rel="stylesheet">
  
  <style>
    body { font-family: "Sarabun", sans-serif; }
    .modal-backdrop { background: rgba(7, 20, 42, 0.75); backdrop-filter: blur(5px); }
    .custom-scrollbar::-webkit-scrollbar { width: 6px; height: 6px; }
    .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 99px; }
    .custom-scrollbar::-webkit-scrollbar-track { background: #f1f5f9; }
    
    .tab-button.active { 
      background: #f59e0b; 
      color: #07142a; 
      font-weight: 700; 
      box-shadow: 0 4px 12px rgba(245, 158, 11, 0.3); 
    }
    .tab-button:not(.active) { color: #dbeafe; }
    .tab-button:not(.active):hover { background: #102a56; color: #ffffff; }

    @keyframes slideIn {
      from { transform: translateY(-100%); opacity: 0; }
      to { transform: translateY(0); opacity: 1; }
    }
    .toast-animate { animation: slideIn 0.3s ease-out forwards; }
  </style>
</head>
<body class="min-h-screen w-full bg-slate-100 text-slate-800 antialiased selection:bg-amber-300 selection:text-slate-900">

  <header class="sticky top-0 z-30 border-b-4 border-amber-400 bg-[#07142A] text-white shadow-lg">
    <div class="mx-auto flex max-w-7xl items-center justify-between gap-4 px-4 py-3 sm:px-6">
      <!-- Logo & Title -->
      <div class="flex min-w-0 items-center gap-3">
        <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-full border-2 border-white bg-gradient-to-br from-amber-300 to-amber-600 text-[#07142A] shadow-md">
          <i data-lucide="heart-handshake" class="h-6 w-6"></i>
        </div>
        <div class="min-w-0">
          <p class="text-xs font-semibold uppercase tracking-widest text-amber-300">มูลนิธิ</p>
          <h1 class="text-xl font-bold leading-tight text-white sm:text-2xl">เติมใจ เต็มสุข</h1>
          <p class="hidden text-xs text-slate-300 sm:block">เติมสุขด้วยใจเรา • ระบบจัดการและติดตามทุนการศึกษา</p>
        </div>
      </div>

      <!-- Navigation Desktop & Year Selector -->
      <div class="flex items-center gap-3">
        <nav class="hidden items-center gap-1 md:flex" aria-label="เมนูหลัก">
          <button type="button" class="tab-button active rounded-lg px-3 py-2 text-sm transition flex items-center gap-1.5" data-tab="dashboard">
            <i data-lucide="pie-chart" class="h-4 w-4"></i>
            <span>แดชบอร์ดสรุปผล</span>
          </button> 
          <button type="button" class="tab-button rounded-lg px-3 py-2 text-sm transition flex items-center gap-1.5" data-tab="students">
            <i data-lucide="graduation-cap" class="h-4 w-4"></i>
            <span>รายชื่อนักเรียน</span>
          </button> 
          <button type="button" class="tab-button rounded-lg px-3 py-2 text-sm transition flex items-center gap-1.5" data-tab="customization">
            <i data-lucide="sliders-horizontal" class="h-4 w-4"></i>
            <span>ตั้งค่าระบบ</span>
          </button> 
          <button type="button" class="tab-button rounded-lg px-3 py-2 text-sm transition flex items-center gap-1.5" data-tab="data">
            <i data-lucide="file-up" class="h-4 w-4"></i>
            <span>นำเข้า/ส่งออก</span>
          </button>
        </nav>

        <!-- Current Year Controller -->
        <div class="flex items-center gap-2 rounded-xl border border-amber-400/40 bg-[#0B1E3D] px-3 py-1.5 shadow-inner">
          <i data-lucide="calendar-days" class="h-4 w-4 text-amber-400"></i> 
          <label class="hidden text-xs font-semibold text-amber-200 sm:block" for="system-year">ปีพิจารณา (พ.ศ.)</label> 
          <input id="system-year" type="number" min="2500" max="2600" value="2569" class="w-16 rounded border border-slate-600 bg-[#07142A] px-1 py-0.5 text-center text-xs font-bold text-amber-300 outline-none focus:ring-2 focus:ring-amber-400">
        </div>

        <button id="mobile-menu-btn" type="button" class="rounded-lg p-2 text-slate-200 hover:bg-[#102A56] md:hidden" aria-label="เปิดเมนู"> 
          <i data-lucide="menu" class="h-6 w-6"></i> 
        </button>
      </div>
    </div>

    <!-- Mobile Drawer Navigation -->
    <nav id="mobile-menu" class="hidden border-t border-slate-700 bg-[#0B1E3D] px-4 py-3 md:hidden" aria-label="เมนูมือถือ">
      <div class="grid gap-1">
        <button type="button" class="mobile-tab text-left rounded-lg px-3 py-2 text-sm text-white hover:bg-[#07142A] flex items-center gap-2" data-tab="dashboard">
          <i data-lucide="pie-chart" class="h-4 w-4 text-amber-400"></i> แดชบอร์ดสรุปผล
        </button> 
        <button type="button" class="mobile-tab text-left rounded-lg px-3 py-2 text-sm text-white hover:bg-[#07142A] flex items-center gap-2" data-tab="students">
          <i data-lucide="graduation-cap" class="h-4 w-4 text-amber-400"></i> รายชื่อนักเรียน
        </button> 
        <button type="button" class="mobile-tab text-left rounded-lg px-3 py-2 text-sm text-white hover:bg-[#07142A] flex items-center gap-2" data-tab="customization">
          <i data-lucide="sliders-horizontal" class="h-4 w-4 text-amber-400"></i> ตั้งค่าระบบ
        </button> 
        <button type="button" class="mobile-tab text-left rounded-lg px-3 py-2 text-sm text-white hover:bg-[#07142A] flex items-center gap-2" data-tab="data">
          <i data-lucide="file-up" class="h-4 w-4 text-amber-400"></i> นำเข้า/ส่งออก
        </button>
      </div>
    </nav>
  </header>

  <!-- Notification Toast Container -->
  <div id="toast-container" class="fixed top-20 right-4 z-50 flex flex-col gap-2 pointer-events-none"></div>

  <main class="mx-auto w-full max-w-7xl px-4 py-6 sm:px-6">

    <!-- ==================== TAB 1: DASHBOARD ==================== -->
    <section id="tab-dashboard" class="tab-panel space-y-6">
      <!-- Summary Cards -->
      <div class="grid gap-4 sm:grid-cols-2 lg:grid-cols-4">
        <article class="rounded-2xl border border-emerald-200 bg-white p-5 shadow-sm transition hover:shadow-md">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-xs font-semibold text-emerald-700">อยู่ระหว่างรับทุน</p>
              <p id="stat-active" class="mt-1 text-3xl font-extrabold text-emerald-600">0</p>
            </div>
            <div class="rounded-xl bg-emerald-100 p-3 text-emerald-600">
              <i data-lucide="user-check" class="h-6 w-6"></i>
            </div>
          </div>
          <p class="mt-2 text-xs text-slate-500">นักเรียนที่กำลังศึกษาและได้รับทุนในปีนี้</p>
        </article>

        <article class="rounded-2xl border border-amber-200 bg-white p-5 shadow-sm transition hover:shadow-md">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-xs font-semibold text-amber-700">กำลังจะจบช่วงชั้นปีนี้</p>
              <p id="stat-ending" class="mt-1 text-3xl font-extrabold text-amber-600">0</p>
            </div>
            <div class="rounded-xl bg-amber-100 p-3 text-amber-600">
              <i data-lucide="clock-4" class="h-6 w-6"></i>
            </div>
          </div>
          <p class="mt-2 text-xs text-slate-500">ต้องพิจารณาต่อสัญญาหรือจบช่วงชั้น</p>
        </article>

        <article class="rounded-2xl border border-blue-200 bg-white p-5 shadow-sm transition hover:shadow-md">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-xs font-semibold text-blue-700">จบการศึกษา/ครบสัญญาแล้ว</p>
              <p id="stat-graduated" class="mt-1 text-3xl font-extrabold text-blue-600">0</p>
            </div>
            <div class="rounded-xl bg-blue-100 p-3 text-blue-600">
              <i data-lucide="award" class="h-6 w-6"></i>
            </div>
          </div>
          <p class="mt-2 text-xs text-slate-500">สำเร็จการศึกษาตามระยะเวลาสัญญา</p>
        </article>

        <article class="rounded-2xl border border-slate-200 bg-white p-5 shadow-sm transition hover:shadow-md">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-xs font-semibold text-slate-600">งบประมาณทุนรวมปีนี้</p>
              <p id="stat-budget" class="mt-1 text-2xl font-extrabold text-slate-800">฿0</p>
            </div>
            <div class="rounded-xl bg-slate-100 p-3 text-slate-600">
              <i data-lucide="coins" class="h-6 w-6"></i>
            </div>
          </div>
          <p class="mt-2 text-xs text-slate-500">ยอดรวมทุนการศึกษาประจำปี พ.ศ. <span class="year-label-text">2569</span></p>
        </article>
      </div>

      <!-- Budget Table & Visual Distributions -->
      <div class="grid gap-6 lg:grid-cols-12">
        <!-- Budget Breakdown Table -->
        <section class="overflow-hidden rounded-2xl border border-slate-200 bg-white shadow-sm lg:col-span-5">
          <div class="flex items-center justify-between bg-[#07142A] px-5 py-4 text-white">
            <h2 class="text-base font-bold flex items-center gap-2">
              <i data-lucide="wallet" class="h-5 w-5 text-amber-400"></i> สรุปงบประมาณแยกตามสถานะ
            </h2>
            <span id="year-tag" class="rounded-full bg-[#102A56] px-3 py-1 text-xs text-amber-300 font-semibold">พ.ศ. 2569</span>
          </div>
          <div class="overflow-x-auto p-4 custom-scrollbar">
            <table class="w-full text-left text-sm">
              <thead class="bg-slate-100 text-xs uppercase text-slate-500">
                <tr>
                  <th class="px-3 py-2.5">สถานะทุน</th>
                  <th class="px-3 py-2.5 text-center">จำนวน (คน)</th>
                  <th class="px-3 py-2.5 text-right">จำนวนเงิน (บาท)</th>
                </tr>
              </thead>
              <tbody id="budget-table" class="divide-y divide-slate-100"></tbody>
              <tfoot class="bg-[#07142A] font-bold text-white">
                <tr>
                  <td class="px-3 py-3">รวมงบประมาณทุนทั้งหมด</td>
                  <td id="budget-total-count" class="px-3 py-3 text-center text-amber-300">0</td>
                  <td id="budget-total" class="px-3 py-3 text-right text-amber-300">฿0</td>
                </tr>
              </tfoot>
            </table>
          </div>
        </section>

        <!-- Stats by Level & School -->
        <div class="space-y-6 lg:col-span-7">
          <div class="grid gap-6 md:grid-cols-2">
            <!-- Level Stats -->
            <section class="rounded-2xl border border-slate-200 bg-white p-5 shadow-sm">
              <div class="mb-3 flex items-center justify-between border-b pb-2">
                <h2 class="text-sm font-bold text-slate-800 flex items-center gap-1.5">
                  <i data-lucide="layers" class="h-4 w-4 text-blue-600"></i> ช่วงชั้นการศึกษา
                </h2>
                <span id="level-total" class="rounded-full bg-blue-100 px-2.5 py-0.5 text-xs font-semibold text-blue-800">0 คน</span>
              </div>
              <ul id="level-list" class="space-y-2 text-sm custom-scrollbar max-h-48 overflow-y-auto pr-1"></ul>
            </section>

            <!-- School Stats -->
            <section class="rounded-2xl border border-slate-200 bg-white p-5 shadow-sm">
              <div class="mb-3 flex items-center justify-between border-b pb-2">
                <h2 class="text-sm font-bold text-slate-800 flex items-center gap-1.5">
                  <i data-lucide="building-2" class="h-4 w-4 text-emerald-600"></i> สถานศึกษา / โรงเรียน
                </h2>
                <span id="school-total" class="rounded-full bg-emerald-100 px-2.5 py-0.5 text-xs font-semibold text-emerald-800">0 คน</span>
              </div>
              <ul id="school-list" class="space-y-2 text-sm custom-scrollbar max-h-48 overflow-y-auto pr-1"></ul>
            </section>
          </div>

          <!-- Trend Chart Section -->
          <section class="rounded-2xl border border-slate-200 bg-white p-5 shadow-sm">
            <div class="mb-4 flex items-start justify-between gap-3">
              <div>
                <h2 class="text-base font-bold text-slate-900 flex items-center gap-2">
                  <i data-lucide="trending-up" class="h-5 w-5 text-indigo-600"></i> จำนวนนักเรียนที่ได้รับทุนแบ่งตามปีที่เริ่มรับทุน
                </h2>
                <p class="text-xs text-slate-500">สถิติและแนวโน้มการมอบทุนการศึกษาย้อนหลัง</p>
              </div>
              <span class="rounded-full border border-blue-200 bg-blue-50 px-3 py-1 text-xs font-semibold text-blue-700">กราฟสรุป</span>
            </div>
            <div class="relative h-64 w-full">
              <canvas id="yearly-chart"></canvas>
            </div>
          </section>
        </div>
      </div>
    </section>

    <!-- ==================== TAB 2: STUDENTS LIST ==================== -->
    <section id="tab-students" class="tab-panel hidden space-y-6">
      <!-- Toolbar & Action Bar -->
      <section class="space-y-4 rounded-2xl border border-slate-200 bg-white p-5 shadow-sm">
        <div class="flex flex-col justify-between gap-4 md:flex-row md:items-center">
          <div>
            <h2 class="text-lg font-bold text-slate-900 flex items-center gap-2">
              <i data-lucide="users" class="h-5 w-5 text-blue-600"></i> ฐานข้อมูลรายชื่อนักเรียนทุน
            </h2>
            <p class="text-xs text-slate-500">ระบบคำนวณระดับชั้นปัจจุบันและสถานะการต่อสัญญาโดยอัตโนมัติตามปี พ.ศ. ที่เลือก</p>
          </div>
          <div class="flex flex-wrap gap-2">
            <button id="bulk-delete-btn" type="button" class="hidden rounded-xl bg-red-600 px-4 py-2 text-sm font-semibold text-white shadow hover:bg-red-700 transition flex items-center gap-1">
              <i data-lucide="trash-2" class="h-4 w-4"></i> ลบที่เลือก (<span id="selected-count">0</span>)
            </button> 
            <button id="add-student-btn" type="button" class="rounded-xl bg-blue-700 px-4 py-2 text-sm font-semibold text-white shadow hover:bg-blue-800 transition flex items-center gap-1.5">
              <i data-lucide="user-plus" class="h-4 w-4"></i> เพิ่มนักเรียนใหม่
            </button> 
            <button id="quick-export-csv-btn" type="button" class="rounded-xl bg-emerald-600 px-4 py-2 text-sm font-semibold text-white shadow hover:bg-emerald-700 transition flex items-center gap-1.5">
              <i data-lucide="download" class="h-4 w-4"></i> ส่งออก CSV
            </button>
          </div>
        </div>

        <!-- Search & Dynamic Filters -->
        <div class="grid gap-3 border-t border-slate-100 pt-4 sm:grid-cols-2 lg:grid-cols-4">
          <div class="relative">
            <i data-lucide="search" class="pointer-events-none absolute left-3 top-2.5 h-4 w-4 text-slate-400"></i>
            <input id="search-input" type="search" class="w-full rounded-xl border border-slate-300 py-2 pl-9 pr-3 text-sm outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-200 transition" placeholder="ค้นหาชื่อ, รหัส, เบอร์โทร...">
          </div> 
          <div>
            <select id="school-filter" class="w-full rounded-xl border border-slate-300 bg-white px-3 py-2 text-sm outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-200 transition">
              <option value="">-- โรงเรียนทั้งหมด --</option>
            </select>
          </div> 
          <div>
            <select id="level-filter" class="w-full rounded-xl border border-slate-300 bg-white px-3 py-2 text-sm outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-200 transition">
              <option value="">-- ช่วงชั้นทั้งหมด --</option>
            </select>
          </div> 
          <div>
            <select id="status-filter" class="w-full rounded-xl border border-slate-300 bg-white px-3 py-2 text-sm outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-200 transition">
              <option value="">-- สถานะทุนทั้งหมด --</option>
            </select>
          </div>
        </div>
      </section>

      <!-- Students Main Data Table -->
      <section class="overflow-hidden rounded-2xl border border-slate-200 bg-white shadow-sm">
        <div class="overflow-x-auto custom-scrollbar">
          <table class="w-full text-left text-sm">
            <thead class="bg-[#07142A] text-xs uppercase text-slate-100">
              <tr id="student-head"></tr>
            </thead>
            <tbody id="student-body" class="divide-y divide-slate-100"></tbody>
          </table>
        </div>
        <div class="flex flex-col justify-between gap-2 border-t border-slate-200 bg-slate-50 px-5 py-3 text-xs text-slate-500 sm:flex-row sm:items-center">
          <span id="student-count">แสดงผล 0 จาก 0 รายการ</span>
          <span class="text-slate-400">* หมายเหตุ: คำนวณชั้นปีและสถานะสัญญาตามปี พ.ศ. พิจารณาปัจจุบัน</span>
        </div>
      </section>
    </section>

    <!-- ==================== TAB 3: CUSTOMIZATION ==================== -->
    <section id="tab-customization" class="tab-panel hidden space-y-6">
      <section class="rounded-2xl border border-slate-200 bg-white p-6 shadow-sm">
        <h2 class="text-lg font-bold text-slate-900 flex items-center gap-2">
          <i data-lucide="settings" class="h-5 w-5 text-amber-500"></i> ตั้งค่ารายการแสดงผลและข้อมูลระบบ
        </h2>
        <p class="mt-1 text-sm text-slate-500">เลือกเปิด/ปิดการแสดงคอลัมน์ในตาราง และจัดการตัวเลือกสถานที่/สถานะ</p>

        <!-- Dynamic Column Toggle Section -->
        <div class="mt-6 rounded-xl border border-slate-200 bg-slate-50 p-5">
          <h3 class="font-bold text-slate-800">คอลัมน์ที่จะแสดงในตารางข้อมูลนักเรียน</h3>
          <p class="mb-4 text-xs text-slate-500">คลิกเพื่อแสดงหรือซ่อนคอลัมน์ในหน้าตารางข้อมูลหลัก</p>
          <div id="column-options" class="grid gap-3 sm:grid-cols-2 lg:grid-cols-4"></div>
        </div>

        <!-- Master Lists Management -->
        <div class="mt-6 grid gap-6 md:grid-cols-3">
          <!-- Schools Master List -->
          <section class="rounded-xl border border-slate-200 bg-slate-50 p-4">
            <h3 class="font-bold text-slate-800 flex items-center gap-1.5">
              <i data-lucide="building" class="h-4 w-4 text-emerald-600"></i> รายชื่อสถานศึกษา
            </h3>
            <div class="mt-3 flex gap-2">
              <input id="new-school" type="text" class="min-w-0 flex-1 rounded-lg border border-slate-300 px-3 py-1.5 text-xs outline-none focus:ring-1 focus:ring-emerald-500" placeholder="เพิ่มชื่อโรงเรียน...">
              <button type="button" class="add-master rounded-lg bg-emerald-600 px-3 py-1.5 text-xs font-semibold text-white hover:bg-emerald-700" data-type="schools">เพิ่ม</button>
            </div>
            <ul id="schools-list" class="mt-3 space-y-1.5 text-xs max-h-52 overflow-y-auto custom-scrollbar pr-1"></ul>
          </section>

          <!-- Levels Master List -->
          <section class="rounded-xl border border-slate-200 bg-slate-50 p-4">
            <h3 class="font-bold text-slate-800 flex items-center gap-1.5">
              <i data-lucide="graduation-cap" class="h-4 w-4 text-blue-600"></i> ระดับช่วงชั้น
            </h3>
            <div class="mt-3 flex gap-2">
              <input id="new-level" type="text" class="min-w-0 flex-1 rounded-lg border border-slate-300 px-3 py-1.5 text-xs outline-none focus:ring-1 focus:ring-blue-500" placeholder="เพิ่มระดับช่วงชั้น...">
              <button type="button" class="add-master rounded-lg bg-blue-600 px-3 py-1.5 text-xs font-semibold text-white hover:bg-blue-700" data-type="levels">เพิ่ม</button>
            </div>
            <ul id="levels-list" class="mt-3 space-y-1.5 text-xs max-h-52 overflow-y-auto custom-scrollbar pr-1"></ul>
          </section>

          <!-- Statuses Master List -->
          <section class="rounded-xl border border-slate-200 bg-slate-50 p-4">
            <h3 class="font-bold text-slate-800 flex items-center gap-1.5">
              <i data-lucide="tag" class="h-4 w-4 text-amber-600"></i> สถานะกำกับดูแล
            </h3>
            <div class="mt-3 flex gap-2">
              <input id="new-status" type="text" class="min-w-0 flex-1 rounded-lg border border-slate-300 px-3 py-1.5 text-xs outline-none focus:ring-1 focus:ring-amber-500" placeholder="เพิ่มสถานะ...">
              <button type="button" class="add-master rounded-lg bg-amber-600 px-3 py-1.5 text-xs font-semibold text-white hover:bg-amber-700" data-type="statuses">เพิ่ม</button>
            </div>
            <ul id="statuses-list" class="mt-3 space-y-1.5 text-xs max-h-52 overflow-y-auto custom-scrollbar pr-1"></ul>
          </section>
        </div>

        <!-- Reset & Clear Controls -->
        <div class="mt-6 rounded-xl border border-red-100 bg-red-50/50 p-5">
          <h3 class="font-bold text-red-800 flex items-center gap-2">
            <i data-lucide="database-zap" class="h-5 w-5 text-red-600"></i> การจัดการข้อมูลและรีเซ็ตระบบ
          </h3>
          <p class="mt-1 text-xs text-slate-600">จัดการข้อมูลนักเรียนเริ่มต้น หรือกู้คืนข้อมูลที่ถูกลบล่าสุด</p>
          <div class="mt-4 flex flex-wrap gap-3">
            <button id="clear-all-data-btn" type="button" class="rounded-xl bg-red-600 px-4 py-2 text-xs font-semibold text-white shadow hover:bg-red-700 transition flex items-center gap-1.5">
              <i data-lucide="trash-2" class="h-4 w-4"></i> ล้างข้อมูลนักเรียนทั้งหมด (เริ่มต้นตารางว่าง)
            </button>
            <button id="undo-delete-btn" type="button" class="rounded-xl bg-amber-600 px-4 py-2 text-xs font-semibold text-white shadow hover:bg-amber-700 transition flex items-center gap-1.5">
              <i data-lucide="undo-2" class="h-4 w-4"></i> กู้คืนข้อมูลที่ลบล่าสุด
            </button>
          </div>
        </div>
      </section>
    </section>

    <!-- ==================== TAB 4: IMPORT / EXPORT DATA ==================== -->
    <section id="tab-data" class="tab-panel hidden space-y-6">
      <section class="rounded-2xl border border-slate-200 bg-white p-6 shadow-sm">
        <h2 class="text-lg font-bold text-slate-900 flex items-center gap-2">
          <i data-lucide="arrow-left-right" class="h-5 w-5 text-indigo-600"></i> นำเข้าและส่งออกข้อมูล
        </h2>
        <p class="mb-6 text-xs text-slate-500">สำรองข้อมูล ส่งออกเป็น CSV สำหรับ Excel และนำเข้าไฟล์ข้อมูล</p>

        <!-- Control Action Cards -->
        <div class="grid gap-6 md:grid-cols-2 mb-8">
          <!-- Export Options -->
          <div class="rounded-xl border border-slate-200 bg-slate-50 p-5 space-y-4">
            <h3 class="font-bold text-slate-800 text-sm flex items-center gap-2">
              <i data-lucide="file-spreadsheets" class="h-4 w-4 text-emerald-600"></i> ส่งออกข้อมูล (Export)
            </h3>
            <p class="text-xs text-slate-500">ส่งออกข้อมูลรายชื่อนักเรียนทุนเป็นไฟล์ CSV สำหรับเปิดบน Microsoft Excel หรือ Google Sheets</p>
            <div class="flex flex-wrap gap-2 pt-2">
              <button id="export-csv-btn" type="button" class="rounded-xl bg-emerald-600 px-4 py-2 text-xs font-semibold text-white shadow hover:bg-emerald-700 transition flex items-center gap-1.5">
                <i data-lucide="download" class="h-4 w-4"></i> ดาวน์โหลด CSV
              </button>
              <button id="preview-csv-btn" type="button" class="rounded-xl bg-slate-700 px-4 py-2 text-xs font-semibold text-white shadow hover:bg-slate-800 transition flex items-center gap-1.5">
                <i data-lucide="eye" class="h-4 w-4"></i> ดู/คัดลอกข้อความ CSV
              </button>
            </div>
          </div>

          <!-- Import Options -->
          <div class="rounded-xl border border-slate-200 bg-slate-50 p-5 space-y-4">
            <h3 class="font-bold text-slate-800 text-sm flex items-center gap-2">
              <i data-lucide="upload-cloud" class="h-4 w-4 text-blue-600"></i> นำเข้าข้อมูล (Import CSV)
            </h3>
            <p class="text-xs text-slate-500">เลือกไฟล์ `.csv` จากคอมพิวเตอร์ของคุณเพื่อนำเข้าข้อมูลนักเรียน</p>
            <div class="flex items-center gap-2 pt-2">
              <input id="csv-file-input" type="file" accept=".csv" class="hidden">
              <label for="csv-file-input" class="cursor-pointer rounded-xl bg-blue-700 px-4 py-2 text-xs font-semibold text-white shadow hover:bg-blue-800 transition flex items-center gap-1.5">
                <i data-lucide="file-up" class="h-4 w-4"></i> เลือกไฟล์ CSV
              </label>
              <span id="selected-filename" class="text-xs text-slate-500 italic">ยังไม่ได้เลือกไฟล์</span>
            </div>
          </div>
        </div>

        <!-- Export Data Table Preview -->
        <h3 class="font-bold text-slate-800 text-sm mb-3">ตัวอย่างข้อมูลที่จะส่งออกทั้งหมด</h3>
        <section class="overflow-hidden rounded-xl border border-slate-200 bg-white">
          <div class="overflow-x-auto custom-scrollbar">
            <table class="w-full text-left text-sm">
              <thead class="bg-[#07142A] text-xs uppercase text-slate-100">
                <tr id="export-data-head"></tr>
              </thead>
              <tbody id="export-data-body" class="divide-y divide-slate-100"></tbody>
            </table>
          </div>
          <p id="export-data-empty" class="hidden px-5 py-10 text-center text-sm text-slate-400">ไม่พบข้อมูลนักเรียนในระบบ</p>
        </section>
      </section>
    </section>
  </main>

  <!-- Modal 1: Add / Edit Student -->
  <div id="student-modal" class="modal-backdrop fixed inset-0 z-40 hidden overflow-y-auto p-4">
    <div class="flex min-h-full items-center justify-center">
      <section class="w-full max-w-2xl overflow-hidden rounded-2xl bg-white shadow-2xl transition-all transform">
        <div class="flex items-center justify-between bg-[#07142A] px-6 py-4 text-white">
          <h2 class="flex items-center gap-2 text-lg font-bold">
            <i data-lucide="user-pen" class="h-5 w-5 text-amber-400"></i>
            <span id="modal-title-text">เพิ่มนักเรียนรับทุนใหม่</span>
          </h2>
          <button type="button" class="close-student-modal rounded p-1 text-slate-300 hover:text-white" aria-label="ปิด">
            <i data-lucide="x" class="h-5 w-5"></i>
          </button>
        </div>
        <form id="student-form" class="space-y-4 p-6">
          <input id="edit-id" type="hidden">
          <div class="grid gap-4 sm:grid-cols-2">
            <div>
              <label class="mb-1 block text-xs font-semibold text-slate-700" for="form-code">รหัสนักเรียน / ทุน *</label>
              <input id="form-code" required type="text" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-blue-500">
            </div>
            <div>
              <label class="mb-1 block text-xs font-semibold text-slate-700" for="form-name">ชื่อ - นามสกุล *</label>
              <input id="form-name" required type="text" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-blue-500">
            </div>
            <div>
              <label class="mb-1 block text-xs font-semibold text-slate-700" for="form-school">สถานศึกษา / โรงเรียน *</label>
              <input id="form-school" required type="text" list="school-datalist" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-blue-500">
              <datalist id="school-datalist"></datalist>
            </div>
            <div>
              <label class="mb-1 block text-xs font-semibold text-slate-700" for="form-start-grade-name">ระดับชั้นเรียนที่เริ่มรับทุน *</label>
              <select id="form-start-grade-name" required class="w-full rounded-xl border border-blue-300 bg-blue-50/50 px-3 py-2 text-sm font-medium text-blue-900 outline-none focus:ring-2 focus:ring-blue-500 transition">
                <option value="">-- เลือกระดับชั้นเรียน --</option>
              </select>
            </div>
            <div>
              <label class="mb-1 block text-xs font-semibold text-slate-700">ช่วงชั้นการศึกษา (คำนวณอัตโนมัติ)</label>
              <input id="form-level-display" type="text" readonly class="w-full rounded-xl border border-slate-200 bg-slate-100 px-3 py-2 text-sm font-semibold text-slate-700 outline-none cursor-not-allowed" placeholder="จะแสดงอัตโนมัติเมื่อเลือกชั้นเรียน">
              <input id="form-level" type="hidden">
            </div>
            <div>
              <label class="mb-1 block text-xs font-semibold text-slate-700" for="form-contract-years">ระยะเวลาสัญญารับทุนจนจบช่วงชั้น (ปี) *</label>
              <div class="flex items-center gap-2">
                <input id="form-contract-years" required type="number" min="1" max="6" value="3" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm font-bold text-slate-800 outline-none focus:ring-2 focus:ring-blue-500">
                <span class="shrink-0 text-xs text-slate-500 font-medium">ปี</span>
              </div>
              <p class="mt-1 text-[11px] text-amber-600 font-medium">* ระบบคำนวณปีที่เหลือก่อนจบช่วงชั้นให้อัตโนมัติ</p>
            </div>
            <div>
              <label class="mb-1 block text-xs font-semibold text-slate-700" for="form-gpa">เกรดเฉลี่ยสะสม (GPA)</label>
              <input id="form-gpa" type="number" min="0" max="4" step="0.01" placeholder="3.50" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-blue-500">
            </div>
            <div>
              <label class="mb-1 block text-xs font-semibold text-slate-700" for="form-status">สถานะทุนกำกับดูแล *</label>
              <select id="form-status" required class="w-full rounded-xl border border-slate-300 bg-white px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-blue-500"></select>
            </div>
            <div>
              <label class="mb-1 block text-xs font-semibold text-slate-700" for="form-amount">จำนวนเงินทุน (บาท/ปี) *</label>
              <input id="form-amount" required type="number" min="0" step="500" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-blue-500">
            </div>
            <div>
              <label class="mb-1 block text-xs font-semibold text-slate-700" for="form-start-year">ปีที่เริ่มรับทุน (พ.ศ.) *</label>
              <input id="form-start-year" required type="number" min="2500" max="2600" value="2569" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-blue-500">
            </div>
            <div class="sm:col-span-2">
              <label class="mb-1 block text-xs font-semibold text-slate-700" for="form-phone">เบอร์โทรศัพท์ติดต่อ</label>
              <input id="form-phone" type="text" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-blue-500">
            </div>
          </div>
          <div>
            <label class="mb-1 block text-xs font-semibold text-slate-700" for="form-remarks">หมายเหตุเพิ่มเติม</label>
            <textarea id="form-remarks" rows="2" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-blue-500"></textarea>
          </div>
          <div class="flex justify-end gap-2 border-t border-slate-100 pt-4">
            <button type="button" class="close-student-modal rounded-xl bg-slate-100 px-4 py-2 text-sm font-medium text-slate-700 hover:bg-slate-200">ยกเลิก</button>
            <button type="submit" class="rounded-xl bg-blue-700 px-5 py-2 text-sm font-semibold text-white hover:bg-blue-800">บันทึกข้อมูล</button>
          </div>
        </form>
      </section>
    </div>
  </div>

  <!-- Modal 2: Confirm Delete -->
  <div id="confirm-modal" class="modal-backdrop fixed inset-0 z-50 hidden p-4">
    <div class="flex min-h-full items-center justify-center">
      <section class="w-full max-w-sm rounded-2xl bg-white p-6 shadow-2xl">
        <h2 id="confirm-title" class="text-lg font-bold text-slate-900">ยืนยันการลบข้อมูล</h2>
        <p id="confirm-message" class="mt-2 text-sm text-slate-500">คุณแน่ใจหรือไม่ว่าต้องการลบรายการนี้?</p>
        <div class="mt-5 flex justify-end gap-2">
          <button id="confirm-cancel" type="button" class="rounded-xl bg-slate-100 px-4 py-2 text-sm font-medium text-slate-700 hover:bg-slate-200">ยกเลิก</button>
          <button id="confirm-action" type="button" class="rounded-xl bg-red-600 px-4 py-2 text-sm font-semibold text-white hover:bg-red-700">ยืนยันการลบ</button>
        </div>
      </section>
    </div>
  </div>

  <!-- Modal 3: CSV Preview / Copy Fallback Modal -->
  <div id="export-preview-modal" class="modal-backdrop fixed inset-0 z-50 hidden overflow-y-auto p-4">
    <div class="flex min-h-full items-center justify-center">
      <section class="w-full max-w-3xl overflow-hidden rounded-2xl bg-white shadow-2xl">
        <div class="flex items-center justify-between bg-[#07142A] px-6 py-4 text-white">
          <h2 class="text-base font-bold flex items-center gap-2">
            <i data-lucide="file-text" class="h-5 w-5 text-amber-400"></i> ข้อมูล CSV สำหรับนำเข้า Excel
          </h2>
          <button id="close-export-preview" type="button" class="rounded p-1 text-slate-300 hover:text-white" aria-label="ปิด">
            <i data-lucide="x" class="h-5 w-5"></i>
          </button>
        </div>
        <div class="space-y-4 p-6">
          <p class="text-sm text-slate-600">
            หากเบราว์เซอร์ไม่ดาวน์โหลดไฟล์โดยตรง สามารถกดปุ่ม <span class="font-bold text-blue-600">"คัดลอกข้อความ CSV"</span> แล้วนำไปวางในโปรแกรม Notepad แล้วบันทึกเป็นชื่อไฟล์ <code class="bg-slate-100 px-1 py-0.5 rounded text-red-600 font-mono">data.csv</code> ได้ครับ
          </p>
          <div>
            <textarea id="export-preview-content" readonly rows="10" class="w-full rounded-xl border border-slate-300 bg-slate-50 p-3 font-mono text-xs text-slate-800 outline-none focus:ring-2 focus:ring-blue-500"></textarea>
          </div>
          <div class="flex justify-end gap-2 border-t border-slate-100 pt-4">
            <button id="copy-csv-text-btn" type="button" class="rounded-xl bg-blue-700 px-4 py-2 text-sm font-semibold text-white hover:bg-blue-800 flex items-center gap-1.5">
              <i data-lucide="copy" class="h-4 w-4"></i> คัดลอกข้อความ CSV
            </button>
            <button id="download-modal-csv-btn" type="button" class="rounded-xl bg-emerald-600 px-4 py-2 text-sm font-semibold text-white hover:bg-emerald-700 flex items-center gap-1.5">
              <i data-lucide="download" class="h-4 w-4"></i> สั่งดาวน์โหลดอีกครั้ง
            </button>
          </div>
        </div>
      </section>
    </div>
  </div>

  <script>
    // Initial Master Settings
    const DEFAULT_MASTER = {
      schools: [
        "โรงเรียนอนุบาลวัดบ้านนา",
        "โรงเรียนเมืองนครนายก",
        "โรงเรียนเตรียมอุดมศึกษาภาคกลาง",
        "โรงเรียนสาธิตประจำจังหวัด"
      ],
      levels: [
        "อนุบาล (อ.1 - อ.3)",
        "ประถมศึกษาตอนต้น (ป.1 - ป.3)",
        "ประถมศึกษาตอนปลาย (ป.4 - ป.6)",
        "มัธยมศึกษาตอนต้น (ม.1 - ม.3)",
        "มัธยมศึกษาตอนปลาย (ม.4 - ม.6)",
        "ปวช. (ปวช.1 - ปวช.3)",
        "ปวส. (ปวส.1 - ปวส.2)",
        "ปริญญาตรี (ปี 1 - ปี 4)"
      ],
      statuses: [
        "ปกติ (มอบต่อ)",
        "กำลังจะหมดสัญญา",
        "สำเร็จการศึกษา / หมดสัญญา",
        "ยุติทุนชั่วคราว",
        "พ้นสภาพนักเรียนทุน"
      ]
    };

    // Grade Structure Mapping Definition
    const GRADE_STRUCTURE = [
      {
        level: "อนุบาล (อ.1 - อ.3)",
        grades: [
          { name: "อ.1", levelGradeIndex: 1, totalInStage: 3 },
          { name: "อ.2", levelGradeIndex: 2, totalInStage: 3 },
          { name: "อ.3", levelGradeIndex: 3, totalInStage: 3 }
        ]
      },
      {
        level: "ประถมศึกษาตอนต้น (ป.1 - ป.3)",
        grades: [
          { name: "ป.1", levelGradeIndex: 1, totalInStage: 3 },
          { name: "ป.2", levelGradeIndex: 2, totalInStage: 3 },
          { name: "ป.3", levelGradeIndex: 3, totalInStage: 3 }
        ]
      },
      {
        level: "ประถมศึกษาตอนปลาย (ป.4 - ป.6)",
        grades: [
          { name: "ป.4", levelGradeIndex: 1, totalInStage: 3 },
          { name: "ป.5", levelGradeIndex: 2, totalInStage: 3 },
          { name: "ป.6", levelGradeIndex: 3, totalInStage: 3 }
        ]
      },
      {
        level: "มัธยมศึกษาตอนต้น (ม.1 - ม.3)",
        grades: [
          { name: "ม.1", levelGradeIndex: 1, totalInStage: 3 },
          { name: "ม.2", levelGradeIndex: 2, totalInStage: 3 },
          { name: "ม.3", levelGradeIndex: 3, totalInStage: 3 }
        ]
      },
      {
        level: "มัธยมศึกษาตอนปลาย (ม.4 - ม.6)",
        grades: [
          { name: "ม.4", levelGradeIndex: 1, totalInStage: 3 },
          { name: "ม.5", levelGradeIndex: 2, levelGradeIndex: 2, totalInStage: 3 },
          { name: "ม.6", levelGradeIndex: 3, totalInStage: 3 }
        ]
      },
      {
        level: "ปวช. (ปวช.1 - ปวช.3)",
        grades: [
          { name: "ปวช.1", levelGradeIndex: 1, totalInStage: 3 },
          { name: "ปวช.2", levelGradeIndex: 2, totalInStage: 3 },
          { name: "ปวช.3", levelGradeIndex: 3, totalInStage: 3 }
        ]
      },
      {
        level: "ปวส. (ปวส.1 - ปวส.2)",
        grades: [
          { name: "ปวส.1", levelGradeIndex: 1, totalInStage: 2 },
          { name: "ปวส.2", levelGradeIndex: 2, totalInStage: 2 }
        ]
      },
      {
        level: "ปริญญาตรี (ปี 1 - ปี 4)",
        grades: [
          { name: "ปี 1", levelGradeIndex: 1, totalInStage: 4 },
          { name: "ปี 2", levelGradeIndex: 2, totalInStage: 4 },
          { name: "ปี 3", levelGradeIndex: 3, totalInStage: 4 },
          { name: "ปี 4", levelGradeIndex: 4, totalInStage: 4 }
        ]
      }
    ];

    // Table Columns Configuration Definition
    const TABLE_COLUMNS = [
      { id: "select", label: "เลือก", default: true },
      { id: "code", label: "รหัสนักเรียน/ทุน", default: true },
      { id: "name", label: "ชื่อ - นามสกุล", default: true },
      { id: "school", label: "สถานศึกษา/โรงเรียน", default: true },
      { id: "level", label: "ช่วงชั้นการศึกษา", default: true },
      { id: "startGradeName", label: "ชั้นที่เริ่มรับทุน", default: true },
      { id: "currentGradeName", label: "ชั้นเรียนปัจจุบัน", default: true },
      { id: "contractYears", label: "ระยะเวลาสัญญา (ปี)", default: false },
      { id: "yearsRemaining", label: "จำนวนปีคงเหลือ", default: true },
      { id: "startYear", label: "ปีเริ่มรับทุน (พ.ศ.)", default: true },
      { id: "gpa", label: "เกรดเฉลี่ย (GPA)", default: true },
      { id: "amount", label: "จำนวนเงินทุน (บาท/ปี)", default: true },
      { id: "status", label: "สถานะทุน", default: true },
      { id: "phone", label: "เบอร์โทรศัพท์", default: false },
      { id: "actions", label: "จัดการ", default: true }
    ];

    // Sample Students Initial Data
    const SAMPLE_STUDENTS = [
      {
        id: "1",
        code: "SCH-2567-001",
        name: "เด็กหญิงสมส้ม สดใส",
        school: "โรงเรียนอนุบาลวัดบ้านนา",
        level: "ประถมศึกษาตอนต้น (ป.1 - ป.3)",
        startGradeName: "ป.1",
        contractYears: 3,
        gpa: 3.85,
        status: "ปกติ (มอบทุนต่อเนื่อง)",
        amount: 5000,
        startYear: 2567,
        phone: "081-234-5678",
        remarks: "ผลการเรียนดีประพฤติดี"
      },
      {
        id: "2",
        code: "SCH-2568-002",
        name: "นายปิติ มีสุข",
        school: "โรงเรียนเมืองนครนายก",
        level: "มัธยมศึกษาตอนต้น (ม.1 - ม.3)",
        startGradeName: "ม.1",
        contractYears: 3,
        gpa: 3.42,
        status: "ปกติ (มอบทุนต่อเนื่อง)",
        amount: 8000,
        startYear: 2568,
        phone: "089-876-5432",
        remarks: "ครอบครัวรายได้น้อย"
      },
      {
        id: "3",
        code: "SCH-2569-003",
        name: "นางสาวมานี รักเรียน",
        school: "โรงเรียนเตรียมอุดมศึกษาภาคกลาง",
        level: "มัธยมศึกษาตอนปลาย (ม.4 - ม.6)",
        startGradeName: "ม.4",
        contractYears: 3,
        gpa: 3.90,
        status: "ปกติ (มอบทุนต่อเนื่อง)",
        amount: 12000,
        startYear: 2569,
        phone: "086-111-2222",
        remarks: "นักเรียนทุนใหม่ปี 2569"
      }
    ];

    // State Variables
    let students = [];
    let master = JSON.parse(JSON.stringify(DEFAULT_MASTER));
    let columns = {};
    let selectedStudentIds = new Set();
    let chartInstance = null;
    let studentToDeleteId = null;
    let lastDeletedStudents = [];

    // Helper: Lookup grade structure info
    function getGradeInfo(gradeName) {
      for (const group of GRADE_STRUCTURE) {
        const found = group.grades.find(g => g.name === gradeName);
        if (found) {
          return {
            level: group.level,
            gradeName: found.name,
            indexInStage: found.levelGradeIndex,
            totalInStage: found.totalInStage,
            remainingInStage: found.totalInStage - found.levelGradeIndex + 1,
            allGradesInStage: group.grades
          };
        }
      }
      return null;
    }

    // Helper: Calculate progression dynamically
    function calculateStudentCurrentInfo(s, sysYear) {
      const startYr = parseInt(s.startYear) || 2569;
      const elapsed = sysYear - startYr;
      const gradeInfo = getGradeInfo(s.startGradeName) || {
        level: s.level || "ไม่ระบุ",
        indexInStage: 1,
        totalInStage: parseInt(s.contractYears) || 3,
        remainingInStage: parseInt(s.contractYears) || 3,
        allGradesInStage: []
      };

      const contractYears = parseInt(s.contractYears) || gradeInfo.remainingInStage;

      // Handle manual override inactive statuses
      if (s.status === "ยุติทุนชั่วคราว" || s.status === "พ้นสภาพนักเรียนทุน") {
        return {
          currentGradeName: s.startGradeName || "ไม่ระบุ",
          level: gradeInfo.level,
          yearsRemaining: 0,
          statusCategory: s.status,
          dynamicStatus: s.status,
          isEnding: false,
          isGraduated: false,
          badgeColor: "bg-slate-100 text-slate-700 font-medium",
          statusBadgeColor: s.status === "พ้นสภาพนักเรียนทุน" ? "bg-red-100 text-red-800 border-red-300 font-semibold" : "bg-orange-100 text-orange-800 border-orange-300 font-semibold"
        };
      }
      
      if (elapsed < 0) {
        return {
          currentGradeName: s.startGradeName || "ยังไม่เริ่ม",
          level: gradeInfo.level,
          yearsRemaining: contractYears,
          statusCategory: "ยังไม่ถึงปีรับทุน",
          dynamicStatus: "ยังไม่ถึงปีรับทุน",
          isEnding: false,
          isGraduated: false,
          badgeColor: "bg-slate-100 text-slate-700 font-medium",
          statusBadgeColor: "bg-slate-100 text-slate-700 border-slate-300"
        };
      }

      if (elapsed >= contractYears) {
        return {
          currentGradeName: "จบช่วงชั้น/ครบสัญญาแล้ว",
          level: gradeInfo.level,
          yearsRemaining: 0,
          statusCategory: "จบการศึกษา/ครบสัญญา",
          dynamicStatus: "สำเร็จการศึกษา / หมดสัญญา",
          isEnding: false,
          isGraduated: true,
          badgeColor: "bg-blue-100 text-blue-800 font-semibold",
          statusBadgeColor: "bg-blue-100 text-blue-800 border-blue-300 font-semibold"
        };
      }

      const currentGradeIdx = (gradeInfo.indexInStage || 1) + elapsed;
      let currentGradeName = s.startGradeName;
      
      if (gradeInfo.allGradesInStage && gradeInfo.allGradesInStage.length > 0) {
        const match = gradeInfo.allGradesInStage.find(g => g.levelGradeIndex === currentGradeIdx);
        if (match) {
          currentGradeName = match.name;
        } else {
          currentGradeName = `${gradeInfo.level} (ปีที่ ${currentGradeIdx})`;
        }
      }

      const remainingYears = contractYears - elapsed;
      const isEnding = (remainingYears === 1);
      const dynamicStatus = isEnding ? "กำลังจะหมดสัญญา" : "ปกติ (มอบต่อ)";

      return {
        currentGradeName: currentGradeName,
        level: gradeInfo.level,
        yearsRemaining: remainingYears,
        statusCategory: isEnding ? "กำลังจะจบช่วงชั้นปีนี้" : "อยู่ระหว่างรับทุน",
        dynamicStatus: dynamicStatus,
        isEnding: isEnding,
        isGraduated: false,
        badgeColor: isEnding ? "bg-amber-100 text-amber-800 font-semibold" : "bg-emerald-100 text-emerald-800 font-semibold",
        statusBadgeColor: isEnding ? "bg-amber-100 text-amber-800 border-amber-300 font-bold" : "bg-emerald-100 text-emerald-800 border-emerald-300 font-semibold"
      };
    }

    // Storage Management
    function loadStorage() {
      const savedStudents = localStorage.getItem("foundation_students");
      if (savedStudents !== null) {
        try {
          students = JSON.parse(savedStudents);
        } catch (e) {
          students = JSON.parse(JSON.stringify(SAMPLE_STUDENTS));
        }
      } else {
        students = JSON.parse(JSON.stringify(SAMPLE_STUDENTS));
        localStorage.setItem("foundation_students", JSON.stringify(students));
      }

      const savedMaster = localStorage.getItem("foundation_master");
      if (savedMaster) {
        try { master = JSON.parse(savedMaster); } catch (e) {}
      }

      const savedCols = localStorage.getItem("foundation_columns");
      if (savedCols) {
        try { columns = JSON.parse(savedCols); } catch (e) {}
      } else {
        TABLE_COLUMNS.forEach(col => { columns[col.id] = col.default; });
      }
    }

    function saveStorage(notifyTabs = true) {
      localStorage.setItem("foundation_students", JSON.stringify(students));
      localStorage.setItem("foundation_master", JSON.stringify(master));
      localStorage.setItem("foundation_columns", JSON.stringify(columns));
      if (notifyTabs) {
        window.dispatchEvent(new Event("storage"));
      }
    }

    // Toast Notification System
    function showToast(message, type = "success") {
      const container = document.getElementById("toast-container");
      const toast = document.createElement("div");
      
      const bgColor = type === "success" ? "bg-emerald-600" : type === "error" ? "bg-red-600" : "bg-blue-600";
      const iconName = type === "success" ? "check-circle" : type === "error" ? "alert-circle" : "info";
      
      toast.className = `toast-animate pointer-events-auto flex items-center gap-2 rounded-xl ${bgColor} px-4 py-3 text-sm font-semibold text-white shadow-xl`;
      toast.innerHTML = `<i data-lucide="${iconName}" class="h-5 w-5"></i><span>${message}</span>`;
      
      container.appendChild(toast);
      lucide.createIcons();
      
      setTimeout(() => {
        toast.style.opacity = "0";
        toast.style.transition = "opacity 0.3s ease";
        setTimeout(() => toast.remove(), 300);
      }, 3000);
    }

    // Main UI Update Function
    function updateUI() {
      const sysYear = parseInt(document.getElementById("system-year").value) || 2569;

      // Update Year Text Displays
      document.querySelectorAll(".year-label-text").forEach(el => el.textContent = sysYear);
      document.getElementById("year-tag").textContent = `พ.ศ. ${sysYear}`;

      // 1. Calculate Dashboard Metrics
      let activeCount = 0;
      let endingCount = 0;
      let graduatedCount = 0;
      let totalBudget = 0;

      const levelCounts = {};
      const schoolCounts = {};
      const statusCounts = {};
      const startYearCounts = {};

      students.forEach(s => {
        const info = calculateStudentCurrentInfo(s, sysYear);
        const amount = parseFloat(s.amount) || 0;

        if (info.isGraduated) {
          graduatedCount++;
        } else if (info.isEnding) {
          endingCount++;
          activeCount++;
          totalBudget += amount;
        } else {
          activeCount++;
          totalBudget += amount;
        }

        // Level Stats
        levelCounts[info.level] = (levelCounts[info.level] || 0) + 1;
        // School Stats
        schoolCounts[s.school] = (schoolCounts[s.school] || 0) + 1;
        // Status Stats (Dynamic)
        const stKey = info.dynamicStatus || s.status || "ไม่ระบุ";
        statusCounts[stKey] = statusCounts[stKey] || { count: 0, budget: 0 };
        statusCounts[stKey].count += 1;
        statusCounts[stKey].budget += amount;
        // Start Year Stats
        const syKey = s.startYear || "ไม่ระบุ";
        startYearCounts[syKey] = (startYearCounts[syKey] || 0) + 1;
      });

      // Update Stat Cards
      document.getElementById("stat-active").textContent = activeCount.toLocaleString();
      document.getElementById("stat-ending").textContent = endingCount.toLocaleString();
      document.getElementById("stat-graduated").textContent = graduatedCount.toLocaleString();
      document.getElementById("stat-budget").textContent = `฿${totalBudget.toLocaleString()}`;

      // Render Budget Breakdown Table
      const budgetBody = document.getElementById("budget-table");
      budgetBody.innerHTML = "";
      let totalCountSum = 0;
      let totalBudgetSum = 0;

      Object.keys(statusCounts).forEach(st => {
        const item = statusCounts[st];
        totalCountSum += item.count;
        totalBudgetSum += item.budget;
        const tr = document.createElement("tr");
        tr.className = "hover:bg-slate-50 transition";
        tr.innerHTML = `
          <td class="px-3 py-2.5 font-medium text-slate-800">${st}</td>
          <td class="px-3 py-2.5 text-center font-bold text-slate-700">${item.count}</td>
          <td class="px-3 py-2.5 text-right font-semibold text-emerald-700">฿${item.budget.toLocaleString()}</td>
        `;
        budgetBody.appendChild(tr);
      });

      document.getElementById("budget-total-count").textContent = totalCountSum;
      document.getElementById("budget-total").textContent = `฿${totalBudgetSum.toLocaleString()}`;

      // Render Distribution Lists
      renderDistributionList("level-list", "level-total", levelCounts);
      renderDistributionList("school-list", "school-total", schoolCounts);

      // Render Chart
      renderChart(startYearCounts);

      // 2. Render Main Students Table
      renderStudentsTable(sysYear);

      // 3. Render Customization Tab
      renderCustomizationOptions();

      // 4. Render Data Export Preview
      renderExportPreview(sysYear);

      // Populate Dynamic Dropdowns
      populateFilterSelects();

      // Refresh Lucide Icons
      lucide.createIcons();
    }

    function renderDistributionList(listId, totalId, countsMap) {
      const listEl = document.getElementById(listId);
      const totalEl = document.getElementById(totalId);
      listEl.innerHTML = "";

      const keys = Object.keys(countsMap);
      let grandTotal = 0;

      keys.forEach(key => {
        grandTotal += countsMap[key];
        const li = document.createElement("li");
        li.className = "flex items-center justify-between rounded-lg bg-slate-50 px-3 py-2 hover:bg-slate-100 transition";
        li.innerHTML = `
          <span class="font-medium text-slate-700 truncate max-w-[200px]" title="${key}">${key}</span>
          <span class="rounded-md bg-white border border-slate-200 px-2 py-0.5 text-xs font-bold text-slate-800">${countsMap[key]} คน</span>
        `;
        listEl.appendChild(li);
      });

      if (keys.length === 0) {
        listEl.innerHTML = `<li class="text-center text-xs text-slate-400 py-4">ไม่พบข้อมูล</li>`;
      }

      totalEl.textContent = `${grandTotal} คน`;
    }

    function renderChart(startYearCounts) {
      const ctx = document.getElementById("yearly-chart").getContext("2d");
      const years = Object.keys(startYearCounts).sort();
      const counts = years.map(y => startYearCounts[y]);

      if (chartInstance) {
        chartInstance.destroy();
      }

      chartInstance = new Chart(ctx, {
        type: "bar",
        data: {
          labels: years.map(y => `ปี พ.ศ. ${y}`),
          datasets: [{
            label: "จำนวนนักเรียนที่เริ่มรับทุน (คน)",
            data: counts,
            backgroundColor: "#3b82f6",
            borderRadius: 8,
            hoverBackgroundColor: "#2563eb"
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: { display: false }
          },
          scales: {
            y: {
              beginAtZero: true,
              ticks: { stepSize: 1, precision: 0 }
            }
          }
        }
      });
    }

    function renderStudentsTable(sysYear) {
      const headTr = document.getElementById("student-head");
      const bodyTb = document.getElementById("student-body");
      headTr.innerHTML = "";
      bodyTb.innerHTML = "";

      // Active Columns
      const activeCols = TABLE_COLUMNS.filter(c => columns[c.id]);

      // Render Head
      activeCols.forEach(c => {
        const th = document.createElement("th");
        th.className = "px-3 py-3 font-semibold";
        if (c.id === "select") {
          th.innerHTML = `<input id="select-all" type="checkbox" class="h-4 w-4 rounded border-slate-300 text-blue-600 focus:ring-blue-500">`;
        } else if (c.id === "actions") {
          th.className += " text-center";
          th.textContent = c.label;
        } else {
          th.textContent = c.label;
        }
        headTr.appendChild(th);
      });

      // Filter Logic
      const searchTerm = (document.getElementById("search-input").value || "").toLowerCase();
      const schoolVal = document.getElementById("school-filter").value;
      const levelVal = document.getElementById("level-filter").value;
      const statusVal = document.getElementById("status-filter").value;

      const filtered = students.filter(s => {
        const info = calculateStudentCurrentInfo(s, sysYear);
        const matchSearch = !searchTerm || 
          (s.name && s.name.toLowerCase().includes(searchTerm)) ||
          (s.code && s.code.toLowerCase().includes(searchTerm)) ||
          (s.phone && s.phone.includes(searchTerm));
        const matchSchool = !schoolVal || s.school === schoolVal;
        const matchLevel = !levelVal || info.level === levelVal;
        const matchStatus = !statusVal || s.status === statusVal || info.dynamicStatus === statusVal;

        return matchSearch && matchSchool && matchLevel && matchStatus;
      });

      document.getElementById("student-count").textContent = `แสดงผล ${filtered.length} จาก ${students.length} รายการ`;

      // Render Body
      filtered.forEach(s => {
        const info = calculateStudentCurrentInfo(s, sysYear);
        const tr = document.createElement("tr");
        tr.className = "hover:bg-slate-50 transition border-b border-slate-100";

        activeCols.forEach(c => {
          const td = document.createElement("td");
          td.className = "px-3 py-3.5 text-xs text-slate-700 font-medium";

          switch (c.id) {
            case "select":
              td.innerHTML = `<input type="checkbox" class="student-select h-4 w-4 rounded border-slate-300 text-blue-600 focus:ring-blue-500" data-id="${s.id}" ${selectedStudentIds.has(s.id) ? "checked" : ""}>`;
              break;
            case "code":
              td.innerHTML = `<span class="font-mono font-bold text-slate-900">${s.code}</span>`;
              break;
            case "name":
              td.innerHTML = `<span class="font-bold text-slate-900">${s.name}</span>`;
              break;
            case "school":
              td.textContent = s.school || "-";
              break;
            case "level":
              td.textContent = info.level;
              break;
            case "startGradeName":
              td.innerHTML = `<span class="rounded bg-slate-100 px-2 py-0.5 font-bold text-slate-800">${s.startGradeName || "-"}</span>`;
              break;
            case "currentGradeName":
              td.innerHTML = `<span class="rounded px-2.5 py-1 ${info.badgeColor}">${info.currentGradeName}</span>`;
              break;
            case "contractYears":
              td.textContent = `${s.contractYears || 3} ปี`;
              break;
            case "yearsRemaining":
              td.innerHTML = `<span class="font-bold text-amber-700">${info.yearsRemaining} ปี</span>`;
              break;
            case "startYear":
              td.textContent = s.startYear || "-";
              break;
            case "gpa":
              td.innerHTML = `<span class="font-bold text-blue-700">${parseFloat(s.gpa || 0).toFixed(2)}</span>`;
              break;
            case "amount":
              td.innerHTML = `<span class="font-bold text-emerald-700">฿${(parseFloat(s.amount) || 0).toLocaleString()}</span>`;
              break;
            case "status":
              td.innerHTML = `<span class="rounded-full border px-2.5 py-0.5 text-xs ${info.statusBadgeColor}">${info.dynamicStatus}</span>`;
              break;
            case "phone":
              td.textContent = s.phone || "-";
              break;
            case "actions":
              td.className += " text-center";
              td.innerHTML = `
                <div class="flex items-center justify-center gap-1">
                  <button type="button" class="edit-btn rounded-lg p-1.5 text-blue-600 hover:bg-blue-50" data-id="${s.id}" title="แก้ไข">
                    <i data-lucide="pencil" class="h-4 w-4"></i>
                  </button>
                  <button type="button" class="delete-btn rounded-lg p-1.5 text-red-600 hover:bg-red-50" data-id="${s.id}" title="ลบ">
                    <i data-lucide="trash-2" class="h-4 w-4"></i>
                  </button>
                </div>
              `;
              break;
          }
          tr.appendChild(td);
        });

        bodyTb.appendChild(tr);
      });

      if (filtered.length === 0) {
        bodyTb.innerHTML = `<tr><td colspan="${activeCols.length}" class="px-4 py-8 text-center text-sm text-slate-400">ไม่พบข้อมูลนักเรียนที่ตรงกับการค้นหา</td></tr>`;
      }

      // Bulk Delete Toggle State
      const bulkBtn = document.getElementById("bulk-delete-btn");
      if (selectedStudentIds.size > 0) {
        bulkBtn.classList.remove("hidden");
        document.getElementById("selected-count").textContent = selectedStudentIds.size;
      } else {
        bulkBtn.classList.add("hidden");
      }
    }

    function renderCustomizationOptions() {
      // Columns Toggle
      const colContainer = document.getElementById("column-options");
      colContainer.innerHTML = "";
      TABLE_COLUMNS.forEach(c => {
        if (c.id === "select" || c.id === "actions") return;
        const label = document.createElement("label");
        label.className = "flex items-center gap-2 rounded-xl bg-white p-3 border border-slate-200 text-xs font-semibold text-slate-700 shadow-sm cursor-pointer hover:border-blue-400 transition";
        label.innerHTML = `
          <input type="checkbox" class="column-toggle h-4 w-4 rounded border-slate-300 text-blue-600 focus:ring-blue-500" data-id="${c.id}" ${columns[c.id] ? "checked" : ""}>
          <span>${c.label}</span>
        `;
        colContainer.appendChild(label);
      });

      // Master Lists
      renderMasterList("schools-list", master.schools, "schools");
      renderMasterList("levels-list", master.levels, "levels");
      renderMasterList("statuses-list", master.statuses, "statuses");
    }

    function renderMasterList(elementId, items, type) {
      const el = document.getElementById(elementId);
      el.innerHTML = "";
      items.forEach((item, idx) => {
        const li = document.createElement("li");
        li.className = "flex items-center justify-between rounded-lg bg-white border border-slate-200 px-3 py-1.5";
        li.innerHTML = `
          <span class="text-slate-700 font-medium">${item}</span>
          <button type="button" class="remove-master text-red-500 hover:text-red-700 p-0.5" data-type="${type}" data-index="${idx}">
            <i data-lucide="x" class="h-3.5 w-3.5"></i>
          </button>
        `;
        el.appendChild(li);
      });
    }

    function renderExportPreview(sysYear) {
      const headTr = document.getElementById("export-data-head");
      const bodyTb = document.getElementById("export-data-body");
      const emptyMsg = document.getElementById("export-data-empty");
      
      headTr.innerHTML = "";
      bodyTb.innerHTML = "";

      if (students.length === 0) {
        emptyMsg.classList.remove("hidden");
        return;
      }
      emptyMsg.classList.add("hidden");

      const headers = ["รหัส", "ชื่อ-นามสกุล", "โรงเรียน", "ช่วงชั้น", "ชั้นเริ่มรับทุน", "ชั้นปัจจุบัน", "เกรด", "ปีเริ่มรับทุน", "ทุน/ปี (บาท)", "สถานะ"];
      headers.forEach(h => {
        const th = document.createElement("th");
        th.className = "px-3 py-2.5 font-semibold";
        th.textContent = h;
        headTr.appendChild(th);
      });

      students.forEach(s => {
        const info = calculateStudentCurrentInfo(s, sysYear);
        const tr = document.createElement("tr");
        tr.className = "border-b border-slate-100 text-xs";
        tr.innerHTML = `
          <td class="px-3 py-2 font-mono font-bold">${s.code}</td>
          <td class="px-3 py-2 font-bold">${s.name}</td>
          <td class="px-3 py-2">${s.school}</td>
          <td class="px-3 py-2">${info.level}</td>
          <td class="px-3 py-2">${s.startGradeName}</td>
          <td class="px-3 py-2 font-semibold text-blue-700">${info.currentGradeName}</td>
          <td class="px-3 py-2">${s.gpa}</td>
          <td class="px-3 py-2">${s.startYear}</td>
          <td class="px-3 py-2 font-semibold text-emerald-700">฿${(parseFloat(s.amount)||0).toLocaleString()}</td>
          <td class="px-3 py-2 font-semibold">${info.dynamicStatus}</td>
        `;
        bodyTb.appendChild(tr);
      });
    }

    function populateFilterSelects() {
      // Form Start Grade Dropdown
      const gradeSelect = document.getElementById("form-start-grade-name");
      if (gradeSelect.options.length <= 1) {
        gradeSelect.innerHTML = `<option value="">-- เลือกระดับชั้นเรียน --</option>`;
        GRADE_STRUCTURE.forEach(group => {
          const optgroup = document.createElement("optgroup");
          optgroup.label = group.level;
          group.grades.forEach(g => {
            const opt = document.createElement("option");
            opt.value = g.name;
            opt.textContent = `${g.name} (${group.level})`;
            optgroup.appendChild(opt);
          });
          gradeSelect.appendChild(optgroup);
        });
      }

      // School Dropdowns
      const schoolFilter = document.getElementById("school-filter");
      const currentSchoolVal = schoolFilter.value;
      schoolFilter.innerHTML = `<option value="">-- โรงเรียนทั้งหมด --</option>`;
      master.schools.forEach(sch => {
        const opt = document.createElement("option");
        opt.value = sch;
        opt.textContent = sch;
        schoolFilter.appendChild(opt);
      });
      schoolFilter.value = currentSchoolVal;

      // School Datalist for Form
      const datalist = document.getElementById("school-datalist");
      datalist.innerHTML = "";
      master.schools.forEach(sch => {
        const opt = document.createElement("option");
        opt.value = sch;
        datalist.appendChild(opt);
      });

      // Level Dropdowns
      const levelFilter = document.getElementById("level-filter");
      const currentLevelVal = levelFilter.value;
      levelFilter.innerHTML = `<option value="">-- ช่วงชั้นทั้งหมด --</option>`;
      master.levels.forEach(lvl => {
        const opt = document.createElement("option");
        opt.value = lvl;
        opt.textContent = lvl;
        levelFilter.appendChild(opt);
      });
      levelFilter.value = currentLevelVal;

      // Status Dropdowns
      const statusFilter = document.getElementById("status-filter");
      const currentStatusVal = statusFilter.value;
      statusFilter.innerHTML = `<option value="">-- สถานะทุนทั้งหมด --</option>`;
      master.statuses.forEach(st => {
        const opt = document.createElement("option");
        opt.value = st;
        opt.textContent = st;
        statusFilter.appendChild(opt);
      });
      statusFilter.value = currentStatusVal;

      // Form Status Dropdown
      const formStatus = document.getElementById("form-status");
      formStatus.innerHTML = "";
      master.statuses.forEach(st => {
        const opt = document.createElement("option");
        opt.value = st;
        opt.textContent = st;
        formStatus.appendChild(opt);
      });
    }

    // Tab Navigation System
    function setupTabs() {
      const buttons = document.querySelectorAll(".tab-button, .mobile-tab");
      buttons.forEach(btn => {
        btn.addEventListener("click", () => {
          const tabKey = btn.dataset.tab;
          
          document.querySelectorAll(".tab-panel").forEach(p => p.classList.add("hidden"));
          document.querySelectorAll(".tab-button").forEach(b => b.classList.remove("active"));
          
          document.getElementById(`tab-${tabKey}`).classList.remove("hidden");
          document.querySelectorAll(`.tab-button[data-tab="${tabKey}"]`).forEach(b => b.classList.add("active"));
          
          document.getElementById("mobile-menu").classList.add("hidden");
        });
      });

      document.getElementById("mobile-menu-btn").addEventListener("click", () => {
        document.getElementById("mobile-menu").classList.toggle("hidden");
      });
    }

    // Modal Control Functions
    function openStudentModal(student = null) {
      const modal = document.getElementById("student-modal");
      const form = document.getElementById("student-form");
      const title = document.getElementById("modal-title-text");

      form.reset();

      if (student) {
        title.textContent = "แก้ไขข้อมูลนักเรียนรับทุน";
        document.getElementById("edit-id").value = student.id;
        document.getElementById("form-code").value = student.code || "";
        document.getElementById("form-name").value = student.name || "";
        document.getElementById("form-school").value = student.school || "";
        document.getElementById("form-start-grade-name").value = student.startGradeName || "";
        
        const gInfo = getGradeInfo(student.startGradeName);
        document.getElementById("form-level-display").value = gInfo ? gInfo.level : (student.level || "");
        document.getElementById("form-level").value = gInfo ? gInfo.level : (student.level || "");
        
        document.getElementById("form-contract-years").value = student.contractYears || 3;
        document.getElementById("form-gpa").value = student.gpa || "";
        document.getElementById("form-status").value = student.status || master.statuses[0];
        document.getElementById("form-amount").value = student.amount || 0;
        document.getElementById("form-start-year").value = student.startYear || document.getElementById("system-year").value;
        document.getElementById("form-phone").value = student.phone || "";
        document.getElementById("form-remarks").value = student.remarks || "";
      } else {
        title.textContent = "เพิ่มนักเรียนรับทุนใหม่";
        document.getElementById("edit-id").value = "";
        document.getElementById("form-code").value = `SCH-${document.getElementById("system-year").value}-${String(students.length + 1).padStart(3, '0')}`;
        document.getElementById("form-start-year").value = document.getElementById("system-year").value;
        document.getElementById("form-contract-years").value = 3;
        document.getElementById("form-level-display").value = "";
        document.getElementById("form-level").value = "";
      }

      modal.classList.remove("hidden");
    }

    function closeStudentModal() {
      document.getElementById("student-modal").classList.add("hidden");
    }

    // Dynamic Grade Selection Listener
    document.getElementById("form-start-grade-name").addEventListener("change", (e) => {
      const selectedGrade = e.target.value;
      const info = getGradeInfo(selectedGrade);
      if (info) {
        document.getElementById("form-level-display").value = info.level;
        document.getElementById("form-level").value = info.level;
        document.getElementById("form-contract-years").value = info.remainingInStage;
      } else {
        document.getElementById("form-level-display").value = "";
        document.getElementById("form-level").value = "";
      }
    });

    // Form Submit Handler
    document.getElementById("student-form").addEventListener("submit", (e) => {
      e.preventDefault();
      
      const editId = document.getElementById("edit-id").value;
      const startGrade = document.getElementById("form-start-grade-name").value;
      const gInfo = getGradeInfo(startGrade);

      const studentData = {
        id: editId || String(Date.now()),
        code: document.getElementById("form-code").value.trim(),
        name: document.getElementById("form-name").value.trim(),
        school: document.getElementById("form-school").value.trim(),
        startGradeName: startGrade,
        level: gInfo ? gInfo.level : document.getElementById("form-level").value,
        contractYears: parseInt(document.getElementById("form-contract-years").value) || 3,
        gpa: parseFloat(document.getElementById("form-gpa").value) || 0,
        status: document.getElementById("form-status").value,
        amount: parseFloat(document.getElementById("form-amount").value) || 0,
        startYear: parseInt(document.getElementById("form-start-year").value) || 2569,
        phone: document.getElementById("form-phone").value.trim(),
        remarks: document.getElementById("form-remarks").value.trim()
      };

      if (editId) {
        const idx = students.findIndex(s => s.id === editId);
        if (idx !== -1) students[idx] = studentData;
        showToast("แก้ไขข้อมูลนักเรียนเรียบร้อยแล้ว");
      } else {
        students.push(studentData);
        showToast("เพิ่มนักเรียนทุนใหม่เรียบร้อยแล้ว");
      }

      saveStorage();
      updateUI();
      closeStudentModal();
    });

    // CSV Export Logic with Thai Language UTF-8 BOM
    function generateCSVString() {
      const sysYear = parseInt(document.getElementById("system-year").value) || 2569;
      const headers = ["รหัสทุน", "ชื่อ-นามสกุล", "สถานศึกษา", "ช่วงชั้น", "ชั้นเริ่มรับทุน", "ชั้นปัจจุบัน", "ระยะเวลาสัญญา(ปี)", "ปีคงเหลือ", "ปีเริ่มรับทุน", "เกรดเฉลี่ย", "จำนวนเงินทุน(บาท)", "สถานะทุน", "เบอร์โทรศัพท์", "หมายเหตุ"];
      
      let csvLines = [headers.join(",")];

      students.forEach(s => {
        const info = calculateStudentCurrentInfo(s, sysYear);
        const row = [
          `"${s.code || ''}"`,
          `"${s.name || ''}"`,
          `"${s.school || ''}"`,
          `"${info.level || ''}"`,
          `"${s.startGradeName || ''}"`,
          `"${info.currentGradeName || ''}"`,
          `"${s.contractYears || 3}"`,
          `"${info.yearsRemaining}"`,
          `"${s.startYear || ''}"`,
          `"${s.gpa || 0}"`,
          `"${s.amount || 0}"`,
          `"${info.dynamicStatus || s.status || ''}"`,
          `"${s.phone || ''}"`,
          `"${s.remarks || ''}"`
        ];
        csvLines.push(row.join(","));
      });

      return csvLines.join("\n");
    }

    function downloadCSV() {
      const csvContent = generateCSVString();
      const bom = "\uFEFF"; // UTF-8 BOM for Thai Excel
      const blob = new Blob([bom + csvContent], { type: "text/csv;charset=utf-8;" });
      const url = URL.createObjectURL(blob);
      
      const link = document.createElement("a");
      link.setAttribute("href", url);
      link.setAttribute("download", `นักเรียนทุน_มูลนิธิเติมใจเต็มสุข_${document.getElementById("system-year").value}.csv`);
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);

      showToast("ส่งออกไฟล์ CSV เรียบร้อยแล้ว");
    }

    function openExportPreviewModal() {
      const csvStr = generateCSVString();
      document.getElementById("export-preview-content").value = csvStr;
      document.getElementById("export-preview-modal").classList.remove("hidden");
    }

    // CSV Import Logic
    document.getElementById("csv-file-input").addEventListener("change", (e) => {
      const file = e.target.files[0];
      if (!file) return;

      document.getElementById("selected-filename").textContent = file.name;

      const reader = new FileReader();
      reader.onload = (evt) => {
        try {
          const text = evt.target.result;
          const lines = text.split(/\r\n|\n/);
          if (lines.length <= 1) {
            showToast("ไฟล์ CSV ไม่มีข้อมูล", "error");
            return;
          }

          let importedCount = 0;
          for (let i = 1; i < lines.length; i++) {
            const line = lines[i].trim();
            if (!line) continue;
            
            // Basic CSV Split Regex
            const cols = line.split(/,(?=(?:[^\"]*\"[^\"]*\")*[^\"]*$)/).map(c => c.replace(/^"|"$/g, '').trim());
            
            if (cols.length >= 3) {
              const startGrade = cols[4] || "ป.1";
              const gInfo = getGradeInfo(startGrade);

              students.push({
                id: String(Date.now() + i),
                code: cols[0] || `SCH-${Date.now()}-${i}`,
                name: cols[1] || "ไม่ระบุชื่อ",
                school: cols[2] || "ไม่ระบุโรงเรียน",
                level: gInfo ? gInfo.level : (cols[3] || "ประถมศึกษาตอนต้น (ป.1 - ป.3)"),
                startGradeName: startGrade,
                contractYears: parseInt(cols[6]) || 3,
                startYear: parseInt(cols[8]) || 2569,
                gpa: parseFloat(cols[9]) || 0,
                amount: parseFloat(cols[10]) || 0,
                status: cols[11] || master.statuses[0],
                phone: cols[12] || "",
                remarks: cols[13] || ""
              });
              importedCount++;
            }
          }

          saveStorage();
          updateUI();
          showToast(`นำเข้าข้อมูลสำเร็จ ${importedCount} รายการ`);
        } catch (err) {
          showToast("เกิดข้อผิดพลาดในการอ่านไฟล์ CSV", "error");
        }
      };
      reader.readAsText(file, "UTF-8");
    });

    // Real-time Storage Sync Across Tabs
    window.addEventListener("storage", () => {
      loadStorage();
      updateUI();
    });

    // Real-time Event Listeners for Dynamic Filters and System Controls
    document.getElementById("system-year").addEventListener("input", updateUI);
    document.getElementById("search-input").addEventListener("input", updateUI);
    document.getElementById("school-filter").addEventListener("change", updateUI);
    document.getElementById("level-filter").addEventListener("change", updateUI);
    document.getElementById("status-filter").addEventListener("change", updateUI);

    // Initial Setup on Window Load
    window.onload = function() {
      loadStorage();
      setupTabs();

      // Button Event Bindings
      document.getElementById("add-student-btn").addEventListener("click", () => openStudentModal());
      document.querySelectorAll(".close-student-modal").forEach(b => b.addEventListener("click", closeStudentModal));
      
      document.getElementById("quick-export-csv-btn").addEventListener("click", downloadCSV);
      document.getElementById("export-csv-btn").addEventListener("click", downloadCSV);
      document.getElementById("preview-csv-btn").addEventListener("click", openExportPreviewModal);
      document.getElementById("download-modal-csv-btn").addEventListener("click", downloadCSV);
      document.getElementById("close-export-preview").addEventListener("click", () => {
        document.getElementById("export-preview-modal").classList.add("hidden");
      });

      document.getElementById("copy-csv-text-btn").addEventListener("click", () => {
        const textarea = document.getElementById("export-preview-content");
        textarea.select();
        document.execCommand("copy");
        showToast("คัดลอกข้อความ CSV เรียบร้อยแล้ว");
      });

      // Confirm Delete Modal Actions
      document.getElementById("confirm-cancel").addEventListener("click", () => {
        document.getElementById("confirm-modal").classList.add("hidden");
      });

      document.getElementById("confirm-action").addEventListener("click", () => {
        if (studentToDeleteId && studentToDeleteId !== "BULK") {
          lastDeletedStudents = students.filter(s => s.id === studentToDeleteId);
          students = students.filter(s => s.id !== studentToDeleteId);
          selectedStudentIds.delete(studentToDeleteId);
          saveStorage();
          updateUI();
          showToast("ลบข้อมูลนักเรียนเรียบร้อยแล้ว");
        } else if (studentToDeleteId === "BULK") {
          lastDeletedStudents = students.filter(s => selectedStudentIds.has(s.id));
          students = students.filter(s => !selectedStudentIds.has(s.id));
          selectedStudentIds.clear();
          saveStorage();
          updateUI();
          showToast("ลบข้อมูลรายการที่เลือกเรียบร้อยแล้ว");
        }
        document.getElementById("confirm-modal").classList.add("hidden");
      });

      // Bulk Delete Action
      document.getElementById("bulk-delete-btn").addEventListener("click", () => {
        studentToDeleteId = "BULK";
        document.getElementById("confirm-title").textContent = "ยืนยันการลบแบบกลุ่ม";
        document.getElementById("confirm-message").textContent = `คุณแน่ใจหรือไม่ว่าต้องการลบรายชื่อนักเรียนที่เลือกจำนวน ${selectedStudentIds.size} รายการ?`;
        document.getElementById("confirm-modal").classList.remove("hidden");
      });

      // Clear & Undo Data Buttons
      document.getElementById("clear-all-data-btn").addEventListener("click", () => {
        if (confirm("คุณแน่ใจหรือไม่ว่าต้องการล้างข้อมูลนักเรียนทั้งหมดในระบบ?")) {
          lastDeletedStudents = [...students];
          students = [];
          selectedStudentIds.clear();
          saveStorage();
          updateUI();
          showToast("ล้างข้อมูลนักเรียนทั้งหมดแล้ว");
        }
      });

      document.getElementById("undo-delete-btn").addEventListener("click", () => {
        if (!lastDeletedStudents || lastDeletedStudents.length === 0) {
          showToast("ไม่พบข้อมูลที่ถูกลบล่าสุด", "error");
          return;
        }
        const restoredCount = lastDeletedStudents.length;
        lastDeletedStudents.forEach(item => {
          if (!students.some(s => s.id === item.id)) {
            students.push(item);
          }
        });
        lastDeletedStudents = [];
        saveStorage();
        updateUI();
        showToast(`กู้คืนข้อมูลนักเรียนจำนวน ${restoredCount} รายการเรียบร้อยแล้ว`);
      });

      // Dynamic Table Interactions (Select / Edit / Delete)
      document.addEventListener("click", (e) => {
        const editBtn = e.target.closest(".edit-btn");
        if (editBtn) {
          const id = editBtn.dataset.id;
          const s = students.find(x => x.id === id);
          if (s) openStudentModal(s);
        }

        const deleteBtn = e.target.closest(".delete-btn");
        if (deleteBtn) {
          studentToDeleteId = deleteBtn.dataset.id;
          const s = students.find(x => x.id === studentToDeleteId);
          document.getElementById("confirm-title").textContent = "ยืนยันการลบข้อมูล";
          document.getElementById("confirm-message").textContent = `คุณแน่ใจหรือไม่ว่าต้องการลบข้อมูลของ ${s ? s.name : 'นักเรียนคนนี้'}?`;
          document.getElementById("confirm-modal").classList.remove("hidden");
        }

        const removeMasterBtn = e.target.closest(".remove-master");
        if (removeMasterBtn) {
          const type = removeMasterBtn.dataset.type;
          const index = parseInt(removeMasterBtn.dataset.index);
          master[type].splice(index, 1);
          saveStorage();
          updateUI();
        }
      });

      // Dynamic Master Add Buttons
      document.querySelectorAll(".add-master").forEach(btn => {
        btn.addEventListener("click", () => {
          const type = btn.dataset.type;
          let inputId = type === "schools" ? "new-school" : type === "levels" ? "new-level" : "new-status";
          const input = document.getElementById(inputId);
          const val = input.value.trim();
          if (val) {
            if (!master[type].includes(val)) {
              master[type].push(val);
              input.value = "";
              saveStorage();
              updateUI();
              showToast("เพิ่มรายการใหม่เรียบร้อยแล้ว");
            }
          }
        });
      });

      // Column Visibility Toggles
      document.addEventListener("change", (e) => {
        if (e.target.classList.contains("column-toggle")) {
          const colId = e.target.dataset.id;
          columns[colId] = e.target.checked;
          saveStorage();
          updateUI();
        }

        if (e.target.id === "select-all") {
          const isChecked = e.target.checked;
          document.querySelectorAll(".student-select").forEach(cb => {
            cb.checked = isChecked;
            const id = cb.dataset.id;
            if (isChecked) selectedStudentIds.add(id);
            else selectedStudentIds.delete(id);
          });
          updateUI();
        }

        if (e.target.classList.contains("student-select")) {
          const id = e.target.dataset.id;
          if (e.target.checked) selectedStudentIds.add(id);
          else selectedStudentIds.delete(id);
          updateUI();
        }
      });

      // Initial UI Render
      updateUI();
    };
  </script>
</body>
</html>