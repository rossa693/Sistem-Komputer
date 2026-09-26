<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplikasi Asesmen Informatika SD</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <!-- Chart.js for Teacher Analytics -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Canvas Confetti for Completion Celebration -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <!-- SheetJS for Excel Export -->
    <script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Plus Jakarta Sans', 'sans-serif'],
                    },
                    colors: {
                        brand: {
                            50: '#f0f7ff',
                            100: '#e0effe',
                            500: '#3b82f6',
                            600: '#2563eb',
                            700: '#1d4ed8',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
            background-color: #f8fafc;
        }
        .custom-scrollbar::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        .option-card {
            transition: all 0.2s ease-in-out;
        }
        .option-card:hover {
            transform: translateY(-2px);
        }
        @keyframes pulse-fast {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.4; }
        }
        .animate-timer-warning {
            animation: pulse-fast 1s infinite;
        }
        @media print {
            body * {
                visibility: hidden !important;
            }
            #printable-certificate, #printable-certificate * {
                visibility: visible !important;
            }
            #printable-certificate {
                position: fixed !important;
                left: 0 !important;
                top: 0 !important;
                width: 100% !important;
                height: auto !important;
                margin: 0 !important;
                padding: 20px !important;
                border: 2px solid #2563eb !important;
                box-shadow: none !important;
                background-color: #ffffff !important;
            }
            .no-print {
                display: none !important;
            }
        }
    </style>
</head>
<body class="text-slate-800 bg-slate-50 min-h-screen flex flex-col justify-between select-none">

    <!-- TOP NAVIGATION HEADER -->
    <header class="bg-white border-b border-slate-200 sticky top-0 z-30 shadow-sm no-print">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between items-center h-16">
                <!-- App Logo & Title -->
                <div class="flex items-center space-x-3">
                    <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-blue-600 to-indigo-500 flex items-center justify-center text-white shadow-md shadow-blue-200">
                        <i class="fa-solid fa-laptop-code text-lg"></i>
                    </div>
                    <div>
                        <h1 class="font-bold text-lg text-slate-800 leading-tight">InforKids SD</h1>
                        <p class="text-xs font-semibold text-slate-500">Asesmen Informatika - Perangkat Keras</p>
                    </div>
                </div>

                <!-- Navigation Tabs / Mode Selector -->
                <div class="flex items-center space-x-2 sm:space-x-4">
                    <div class="bg-slate-100 p-1 rounded-xl flex text-xs font-semibold">
                        <button id="btn-mode-student" onclick="switchView('student')" class="px-3 py-1.5 rounded-lg bg-white text-blue-600 shadow-sm transition-all flex items-center gap-1.5">
                            <i class="fa-solid fa-user-graduate"></i>
                            <span class="hidden sm:inline">Mode</span> Siswa
                        </button>
                        <button id="btn-mode-teacher" onclick="openTeacherLogin()" class="px-3 py-1.5 rounded-lg text-slate-600 hover:text-slate-900 transition-all flex items-center gap-1.5">
                            <i class="fa-solid fa-chalkboard-user"></i>
                            <span class="hidden sm:inline">Dashboard</span> Guru
                        </button>
                    </div>

                    <!-- Timer Badge (Visible during assessment) -->
                    <div id="quiz-header-timer" class="hidden items-center gap-2 bg-blue-50 border border-blue-200 text-blue-700 px-3 py-1.5 rounded-xl text-xs font-bold transition-all">
                        <i class="fa-regular fa-clock animate-pulse"></i>
                        <span id="timer-display">30:00</span>
                    </div>
                </div>
            </div>
        </div>
    </header>

    <!-- MAIN CONTENT CONTAINER -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6 flex-grow w-full">

        <!-- ================= STUDENT VIEW ================= -->
        <div id="student-view" class="space-y-6">

            <!-- SETUP / FORM IDENTITAS SISWA (BEFORE QUIZ) -->
            <div id="setup-panel" class="bg-white rounded-3xl p-6 sm:p-8 shadow-sm border border-slate-200 space-y-6 max-w-4xl mx-auto">
                
                <!-- Welcome Banner & Subject Info -->
                <div class="text-center space-y-3 border-b border-slate-100 pb-6">
                    <div class="inline-flex items-center gap-2 bg-blue-50 text-blue-700 px-4 py-1.5 rounded-full text-xs font-bold tracking-wide uppercase">
                        <i class="fa-solid fa-laptop-code"></i> ASESMEN INFORMATIKA ONLINE
                    </div>
                    <h2 class="text-2xl sm:text-3xl font-extrabold text-slate-800 tracking-tight">SISTEM KOMPUTER</h2>
                    <p class="text-sm font-semibold text-slate-600">Mengenal Jenis-Jenis Perangkat Keras (Hardware)</p>

                    <!-- Hardware Icons Grid Strip -->
                    <div class="pt-3 flex flex-wrap justify-center items-center gap-2 sm:gap-3 text-slate-600 text-xs font-semibold">
                        <span class="bg-slate-100 px-3 py-1.5 rounded-xl flex items-center gap-1.5"><i class="fa-solid fa-desktop text-blue-500"></i> Monitor</span>
                        <span class="bg-slate-100 px-3 py-1.5 rounded-xl flex items-center gap-1.5"><i class="fa-solid fa-keyboard text-emerald-500"></i> Keyboard</span>
                        <span class="bg-slate-100 px-3 py-1.5 rounded-xl flex items-center gap-1.5"><i class="fa-solid fa-computer-mouse text-purple-500"></i> Mouse</span>
                        <span class="bg-slate-100 px-3 py-1.5 rounded-xl flex items-center gap-1.5"><i class="fa-solid fa-microchip text-indigo-500"></i> CPU</span>
                        <span class="bg-slate-100 px-3 py-1.5 rounded-xl flex items-center gap-1.5"><i class="fa-solid fa-print text-amber-500"></i> Printer</span>
                        <span class="bg-slate-100 px-3 py-1.5 rounded-xl flex items-center gap-1.5"><i class="fa-solid fa-volume-high text-rose-500"></i> Speaker</span>
                        <span class="bg-slate-100 px-3 py-1.5 rounded-xl flex items-center gap-1.5"><i class="fa-solid fa-camera text-teal-500"></i> Webcam</span>
                        <span class="bg-slate-100 px-3 py-1.5 rounded-xl flex items-center gap-1.5"><i class="fa-solid fa-laptop text-sky-500"></i> Laptop</span>
                    </div>
                </div>

                <!-- Form Identitas Siswa -->
                <div class="space-y-5">
                    <h3 class="font-bold text-sm text-slate-800 uppercase tracking-wider flex items-center gap-2">
                        <i class="fa-solid fa-id-card text-blue-600"></i> Identitas Siswa
                    </h3>
                    
                    <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                        <div class="space-y-1.5 sm:col-span-2">
                            <label for="student-name" class="text-xs font-bold text-slate-700">Nama Lengkap <span class="text-rose-500">*</span></label>
                            <input type="text" id="student-name" placeholder="Masukkan nama lengkap kamu..." class="w-full px-4 py-2.5 rounded-xl border border-slate-300 text-xs sm:text-sm focus:ring-2 focus:ring-blue-500 focus:outline-none font-medium">
                        </div>

                        <div class="space-y-1.5">
                            <label for="student-absen" class="text-xs font-bold text-slate-700">Nomor Absen <span class="text-rose-500">*</span></label>
                            <input type="number" id="student-absen" min="1" max="50" placeholder="No. Absen" class="w-full px-4 py-2.5 rounded-xl border border-slate-300 text-xs sm:text-sm focus:ring-2 focus:ring-blue-500 focus:outline-none font-medium">
                        </div>
                    </div>

                    <!-- Tombol Pilihan Kelas -->
                    <div class="space-y-2">
                        <label class="text-xs font-bold text-slate-700 block">
                            Pilih Kelas <span class="text-rose-500">*</span>
                        </label>
                        <input type="hidden" id="student-class" value="">

                        <div class="space-y-3 bg-slate-50 p-4 rounded-2xl border border-slate-200">
                            <!-- Kelas 4 -->
                            <div>
                                <span class="text-[11px] font-bold text-slate-500 uppercase tracking-wider mb-1.5 block">Kelas 4</span>
                                <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-2">
                                    <button type="button" onclick="selectClassButton('4 Al Halim', this)" class="class-select-btn px-3 py-2.5 rounded-xl border border-slate-200 bg-white text-slate-700 text-xs font-bold hover:bg-slate-100 transition-all text-center">
                                        4 Al Halim
                                    </button>
                                    <button type="button" onclick="selectClassButton('4 Al Latief', this)" class="class-select-btn px-3 py-2.5 rounded-xl border border-slate-200 bg-white text-slate-700 text-xs font-bold hover:bg-slate-100 transition-all text-center">
                                        4 Al Latief
                                    </button>
                                </div>
                            </div>

                            <!-- Kelas 5 -->
                            <div>
                                <span class="text-[11px] font-bold text-slate-500 uppercase tracking-wider mb-1.5 block">Kelas 5</span>
                                <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-2">
                                    <button type="button" onclick="selectClassButton('5 As Shobur', this)" class="class-select-btn px-3 py-2.5 rounded-xl border border-slate-200 bg-white text-slate-700 text-xs font-bold hover:bg-slate-100 transition-all text-center">
                                        5 As Shobur
                                    </button>
                                    <button type="button" onclick="selectClassButton('5 Al Ghofur', this)" class="class-select-btn px-3 py-2.5 rounded-xl border border-slate-200 bg-white text-slate-700 text-xs font-bold hover:bg-slate-100 transition-all text-center">
                                        5 Al Ghofur
                                    </button>
                                    <button type="button" onclick="selectClassButton('5 Al Hafidz', this)" class="class-select-btn px-3 py-2.5 rounded-xl border border-slate-200 bg-white text-slate-700 text-xs font-bold hover:bg-slate-100 transition-all text-center">
                                        5 Al Hafidz
                                    </button>
                                </div>
                            </div>

                            <!-- Kelas 6 -->
                            <div>
                                <span class="text-[11px] font-bold text-slate-500 uppercase tracking-wider mb-1.5 block">Kelas 6</span>
                                <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-2">
                                    <button type="button" onclick="selectClassButton('6 An Nuur', this)" class="class-select-btn px-3 py-2.5 rounded-xl border border-slate-200 bg-white text-slate-700 text-xs font-bold hover:bg-slate-100 transition-all text-center">
                                        6 An Nuur
                                    </button>
                                    <button type="button" onclick="selectClassButton('6 Al Wakil', this)" class="class-select-btn px-3 py-2.5 rounded-xl border border-slate-200 bg-white text-slate-700 text-xs font-bold hover:bg-slate-100 transition-all text-center">
                                        6 Al Wakil
                                    </button>
                                    <button type="button" onclick="selectClassButton('6 Al Ghani', this)" class="class-select-btn px-3 py-2.5 rounded-xl border border-slate-200 bg-white text-slate-700 text-xs font-bold hover:bg-slate-100 transition-all text-center">
                                        6 Al Ghani
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Instructions Box -->
                <div class="bg-amber-50/80 border border-amber-200 rounded-2xl p-5 space-y-3">
                    <h4 class="font-bold text-xs sm:text-sm text-amber-900 flex items-center gap-2">
                        <i class="fa-solid fa-circle-info text-amber-600"></i> Petunjuk Pengerjaan Asesmen:
                    </h4>
                    <ul class="text-xs text-amber-900/90 space-y-2 list-disc list-inside leading-relaxed font-medium">
                        <li>Bacalah setiap soal dengan teliti sebelum memilih jawaban.</li>
                        <li>Pilih satu jawaban yang paling tepat dari pilihan A, B, C, atau D.</li>
                        <li>Jumlah soal pengerjaan adalah <strong>30 soal pilihan ganda</strong>.</li>
                        <li>Waktu pengerjaan dibatasi selama <strong>30 menit (30:00)</strong>.</li>
                        <li>Waktu akan terus berjalan secara otomatis setelah tombol <strong>"Mulai Asesmen"</strong> ditekan.</li>
                        <li>Jangan menutup halaman atau berpindah aplikasi selama pengerjaan.</li>
                        <li>Periksa kembali jawaban kamu sebelum mengumpulkan.</li>
                        <li>Setelah waktu habis (00:00), jawaban kamu akan otomatis dikumpulkan.</li>
                    </ul>
                </div>

                <!-- Agreement Checkbox & Start Button -->
                <div class="pt-2 space-y-4 border-t border-slate-100">
                    <label class="flex items-start gap-3 cursor-pointer select-none">
                        <input type="checkbox" id="check-ready" onchange="toggleStartButton()" class="mt-0.5 w-4 h-4 rounded text-blue-600 focus:ring-blue-500 border-slate-300 cursor-pointer">
                        <span class="text-xs sm:text-sm text-slate-700 font-semibold">
                            Saya sudah membaca petunjuk dan siap mengerjakan dengan jujur.
                        </span>
                    </label>

                    <button id="btn-start-quiz" onclick="startQuiz()" disabled class="w-full py-3.5 rounded-xl bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-700 hover:to-indigo-700 disabled:from-slate-300 disabled:to-slate-300 disabled:opacity-60 disabled:cursor-not-allowed text-white font-bold text-sm shadow-md transition-all flex items-center justify-center gap-2">
                        <i class="fa-solid fa-play"></i> MULAI ASESMEN
                    </button>
                </div>
            </div>

            <!-- ASSESSMENT WORKSPACE (ACTIVE DURING QUIZ) -->
            <div id="quiz-panel" class="hidden grid grid-cols-1 lg:grid-cols-4 gap-6">

                <!-- Left Column: Question Area (3 cols) -->
                <div class="lg:col-span-3 space-y-4">
                    
                    <!-- Progress Card -->
                    <div class="bg-white rounded-2xl p-4 border border-slate-200 shadow-sm flex items-center justify-between gap-4">
                        <div class="flex items-center gap-3">
                            <span id="question-badge" class="px-3 py-1 bg-blue-100 text-blue-700 rounded-lg text-xs font-bold">Soal 1 dari 30</span>
                            <span id="element-tag" class="text-xs font-semibold text-slate-500 bg-slate-100 px-2.5 py-1 rounded-lg hidden sm:inline-block">
                                <i class="fa-solid fa-hardware text-indigo-500 mr-1"></i> Perangkat Keras
                            </span>
                        </div>
                        <div class="flex items-center gap-2 flex-grow max-w-xs">
                            <div class="w-full bg-slate-100 h-2.5 rounded-full overflow-hidden">
                                <div id="progress-bar" class="bg-blue-600 h-full rounded-full transition-all duration-300" style="width: 3%;"></div>
                            </div>
                            <span id="progress-percent" class="text-xs font-bold text-slate-600">3%</span>
                        </div>
                    </div>

                    <!-- Question & Answers Main Card -->
                    <div class="bg-white rounded-2xl p-6 sm:p-8 border border-slate-200 shadow-sm space-y-6">
                        
                        <!-- Question Text -->
                        <div>
                            <p id="question-text" class="text-base sm:text-lg font-bold text-slate-800 leading-relaxed mb-2">
                                Memuat soal...
                            </p>
                        </div>

                        <!-- Answer Options Container -->
                        <div id="options-container" class="space-y-3">
                            <!-- Options dynamically inserted -->
                        </div>
                    </div>

                    <!-- Bottom Controls / Navigation Buttons -->
                    <div class="flex items-center justify-between bg-white rounded-2xl p-4 border border-slate-200 shadow-sm">
                        <button id="btn-prev" onclick="navigateQuestion(-1)" class="px-4 py-2.5 rounded-xl border border-slate-300 text-slate-700 text-xs font-bold hover:bg-slate-50 disabled:opacity-40 disabled:cursor-not-allowed flex items-center gap-2">
                            <i class="fa-solid fa-arrow-left"></i> Sebelumnya
                        </button>

                        <button id="btn-next" onclick="navigateQuestion(1)" class="px-5 py-2.5 rounded-xl bg-blue-600 hover:bg-blue-700 text-white text-xs font-bold shadow-sm flex items-center gap-2">
                            Berikutnya <i class="fa-solid fa-arrow-right"></i>
                        </button>

                        <button id="btn-finish" onclick="confirmFinishQuiz()" class="hidden px-5 py-2.5 rounded-xl bg-emerald-600 hover:bg-emerald-700 text-white text-xs font-bold shadow-sm flex items-center gap-2">
                            KUMPULKAN JAWABAN <i class="fa-solid fa-paper-plane"></i>
                        </button>
                    </div>
                </div>

                <!-- Right Column: Question Navigator Sidebar (1 col) -->
                <div class="lg:col-span-1 space-y-4">
                    <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm space-y-4">
                        <h3 class="font-bold text-xs uppercase tracking-wider text-slate-500 flex items-center justify-between">
                            <span>Nomor Soal</span>
                            <i class="fa-solid fa-border-all text-slate-400"></i>
                        </h3>

                        <!-- Grid 1 to 30 -->
                        <div id="question-grid" class="grid grid-cols-5 gap-2">
                            <!-- Dynamic Question Grid Buttons -->
                        </div>

                        <!-- Legend -->
                        <div class="pt-3 border-t border-slate-100 space-y-2 text-[11px] font-medium text-slate-600">
                            <div class="flex items-center gap-2">
                                <span class="w-3.5 h-3.5 rounded-md bg-blue-600 border border-blue-600 inline-block"></span>
                                <span>Sudah dijawab</span>
                            </div>
                            <div class="flex items-center gap-2">
                                <span class="w-3.5 h-3.5 rounded-md bg-slate-100 border border-slate-300 inline-block"></span>
                                <span>Belum dijawab</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- RESULT REPORT CARD PANEL (AFTER FINISHING QUIZ) -->
            <div id="result-panel" class="hidden space-y-6 max-w-4xl mx-auto">
                <!-- Banner Header -->
                <div class="bg-gradient-to-r from-blue-600 via-indigo-600 to-purple-600 rounded-3xl p-6 sm:p-8 text-white shadow-xl flex flex-col md:flex-row items-center justify-between gap-6">
                    <div class="space-y-2 text-center md:text-left">
                        <span class="px-3 py-1 bg-white/20 backdrop-blur-md rounded-full text-xs font-bold tracking-wide uppercase">🎉 ASESMEN SELESAI</span>
                        <h2 class="text-2xl sm:text-3xl font-extrabold" id="result-student-name">Nama Siswa</h2>
                        <p class="text-xs sm:text-sm text-blue-100" id="result-meta">Kelas • No. Absen • Informatik SD</p>
                    </div>

                    <!-- Score Gauge Box -->
                    <div class="bg-white/10 backdrop-blur-md border border-white/20 p-5 rounded-2xl text-center min-w-[170px]">
                        <span class="text-xs uppercase font-bold text-blue-100 block mb-1">Nilai Akhir</span>
                        <span class="text-4xl sm:text-5xl font-black tracking-tight" id="score-val">0</span>
                        <span class="text-xs text-blue-200 block mt-1" id="score-grade">Predikat: -</span>
                    </div>
                </div>

                <!-- Detail Diagnostics Cards -->
                <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                    <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm flex items-center gap-4">
                        <div class="w-12 h-12 rounded-xl bg-emerald-100 text-emerald-600 flex items-center justify-center text-xl font-bold">
                            <i class="fa-solid fa-circle-check"></i>
                        </div>
                        <div>
                            <p class="text-xs text-slate-500 font-medium">Jumlah Benar</p>
                            <p class="text-xl font-bold text-slate-800" id="correct-count">0 / 30</p>
                        </div>
                    </div>

                    <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm flex items-center gap-4">
                        <div class="w-12 h-12 rounded-xl bg-rose-100 text-rose-600 flex items-center justify-center text-xl font-bold">
                            <i class="fa-solid fa-circle-xmark"></i>
                        </div>
                        <div>
                            <p class="text-xs text-slate-500 font-medium">Jumlah Salah</p>
                            <p class="text-xl font-bold text-slate-800" id="wrong-count">0</p>
                        </div>
                    </div>

                    <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm flex items-center gap-4">
                        <div class="w-12 h-12 rounded-xl bg-blue-100 text-blue-600 flex items-center justify-center text-xl font-bold">
                            <i class="fa-solid fa-clock"></i>
                        </div>
                        <div>
                            <p class="text-xs text-slate-500 font-medium">Durasi Pengerjaan</p>
                            <p class="text-xl font-bold text-slate-800" id="time-spent-display">00:00</p>
                        </div>
                    </div>
                </div>

                <!-- Motivation Message -->
                <div class="bg-amber-50 border border-amber-200 rounded-2xl p-5 text-center space-y-1">
                    <p class="font-bold text-sm text-amber-900" id="motivation-text">Hebat! Terima kasih sudah menyelesaikan asesmen dengan sungguh-sungguh.</p>
                    <p class="text-xs text-amber-700">Data hasil pengerjaan kamu telah disimpan secara otomatis.</p>
                </div>

                <!-- Actions: Print & Retake -->
                <div class="flex justify-center items-center gap-4 pt-2 no-print relative z-20">
                    <button type="button" id="btn-print-result" onclick="printResult()" class="px-6 py-3 rounded-xl bg-slate-800 hover:bg-slate-900 active:bg-slate-950 text-white font-bold text-xs shadow-md transition-all flex items-center gap-2 cursor-pointer active:scale-95 pointer-events-auto">
                        <i class="fa-solid fa-print"></i> Cetak Hasil / Simpan PDF
                    </button>
                </div>
            </div>

        </div>

        <!-- ================= TEACHER VIEW ================= -->
        <div id="teacher-view" class="hidden space-y-6">
            
            <!-- Dashboard Overview Bar -->
            <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
                <div>
                    <h2 class="text-xl font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-chart-line text-indigo-600"></i> Dashboard Guru & Realtime Monitoring
                    </h2>
                    <p class="text-xs text-slate-500 mt-1">Pantau hasil pengerjaan asesmen siswa secara realtime dan unduh rekapitulasi Excel.</p>
                </div>

                <div class="flex items-center gap-3">
                    <select id="filter-class" onchange="renderTeacherStats()" class="px-3.5 py-2 rounded-xl border border-slate-300 text-xs font-semibold focus:ring-2 focus:ring-blue-500 focus:outline-none">
                        <option value="all">Semua Kelas</option>
                        <option value="4 Al Halim">4 Al Halim</option>
                        <option value="4 Al Latief">4 Al Latief</option>
                        <option value="5 As Shobur">5 As Shobur</option>
                        <option value="5 Al Ghofur">5 Al Ghofur</option>
                        <option value="5 Al Hafidz">5 Al Hafidz</option>
                        <option value="6 An Nuur">6 An Nuur</option>
                        <option value="6 Al Wakil">6 Al Wakil</option>
                        <option value="6 Al Ghani">6 Al Ghani</option>
                    </select>
                    <button onclick="exportExcel()" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-700 text-white font-bold text-xs rounded-xl shadow-sm flex items-center gap-1.5">
                        <i class="fa-solid fa-file-excel"></i> Export Excel (.xlsx)
                    </button>
                </div>
            </div>

            <!-- Teacher Summary Cards -->
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm">
                    <p class="text-xs text-slate-500 font-medium">Total Peserta</p>
                    <p class="text-2xl font-black text-slate-800 mt-1" id="t-total-students">0</p>
                </div>

                <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm">
                    <p class="text-xs text-slate-500 font-medium">Rata-Rata Nilai</p>
                    <p class="text-2xl font-black text-blue-600 mt-1" id="t-avg-score">0</p>
                </div>

                <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm">
                    <p class="text-xs text-slate-500 font-medium">Nilai Tertinggi</p>
                    <p class="text-2xl font-black text-emerald-600 mt-1" id="t-high-score">0</p>
                </div>

                <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm">
                    <p class="text-xs text-slate-500 font-medium">Nilai Terendah</p>
                    <p class="text-2xl font-black text-rose-600 mt-1" id="t-low-score">0</p>
                </div>
            </div>

            <!-- Student Roster Table -->
            <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden space-y-4">
                <div class="p-5 border-b border-slate-100 flex items-center justify-between">
                    <h3 class="font-bold text-base text-slate-800">Daftar Hasil Asesmen Siswa</h3>
                    <div class="relative">
                        <input type="text" id="search-student" onkeyup="filterStudentTable()" placeholder="Cari nama siswa..." class="pl-8 pr-3 py-1.5 rounded-xl border border-slate-300 text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                        <i class="fa-solid fa-magnifying-glass absolute left-2.5 top-2.5 text-slate-400 text-xs"></i>
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full text-left text-xs text-slate-700">
                        <thead class="bg-slate-50 text-slate-500 uppercase text-[10px] tracking-wider font-bold border-b border-slate-200">
                            <tr>
                                <th class="px-4 py-3.5">No</th>
                                <th class="px-4 py-3.5">Nama Siswa</th>
                                <th class="px-4 py-3.5">Kelas</th>
                                <th class="px-4 py-3.5 text-center">No. Absen</th>
                                <th class="px-4 py-3.5 text-center">Benar</th>
                                <th class="px-4 py-3.5 text-center">Salah</th>
                                <th class="px-4 py-3.5 text-center">Nilai</th>
                                <th class="px-4 py-3.5 text-center">Durasi</th>
                                <th class="px-4 py-3.5 text-center">Status</th>
                            </tr>
                        </thead>
                        <tbody id="student-table-body" class="divide-y divide-slate-100 font-medium">
                            <!-- Dynamic Table Rows -->
                        </tbody>
                    </table>
                </div>
            </div>

        </div>

    </main>

    <!-- CUSTOM MODAL DIALOG (Replaces alert and confirm) -->
    <div id="custom-modal" class="hidden fixed inset-0 z-[70] bg-slate-900/50 backdrop-blur-sm flex items-center justify-center p-4">
        <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-xl space-y-4 animate-in fade-in zoom-in duration-150">
            <div class="flex items-center gap-3 text-slate-800">
                <div id="modal-icon-container" class="w-10 h-10 rounded-xl bg-amber-100 text-amber-600 flex items-center justify-center text-lg font-bold">
                    <i id="modal-icon" class="fa-solid fa-triangle-exclamation"></i>
                </div>
                <div>
                    <h3 id="modal-title" class="font-bold text-base">Pemberitahuan</h3>
                    <p id="modal-subtitle" class="text-xs text-slate-500">InforKids SD</p>
                </div>
            </div>
            
            <p id="modal-message" class="text-xs sm:text-sm text-slate-600 leading-relaxed font-medium">
                Pesan modal akan muncul di sini...
            </p>

            <div id="modal-actions" class="flex justify-end gap-2 pt-2 border-t border-slate-100">
                <button id="modal-btn-cancel" onclick="closeModal()" class="hidden px-4 py-2 rounded-xl border border-slate-300 text-slate-700 text-xs font-bold hover:bg-slate-50">
                    Batal
                </button>
                <button id="modal-btn-confirm" onclick="closeModal()" class="px-5 py-2 rounded-xl bg-blue-600 hover:bg-blue-700 text-white text-xs font-bold shadow-sm">
                    OK
                </button>
            </div>
        </div>
    </div>

    <!-- TEACHER LOGIN MODAL -->
    <div id="teacher-modal" class="hidden fixed inset-0 z-50 bg-slate-900/50 backdrop-blur-sm flex items-center justify-center p-4">
        <div class="bg-white rounded-2xl max-w-sm w-full p-6 shadow-xl space-y-4">
            <div class="text-center space-y-1">
                <div class="w-12 h-12 rounded-2xl bg-indigo-100 text-indigo-600 mx-auto flex items-center justify-center text-xl">
                    <i class="fa-solid fa-lock"></i>
                </div>
                <h3 class="font-bold text-base text-slate-800">Akses Dashboard Guru</h3>
                <p class="text-xs text-slate-500">Masukkan Password untuk melanjutkan.</p>
            </div>

            <div class="space-y-2">
                <input type="password" id="teacher-password" placeholder="Password Guru (Default: guru123)" class="w-full px-4 py-2.5 rounded-xl border border-slate-300 text-xs focus:ring-2 focus:ring-indigo-500 focus:outline-none">
            </div>

            <div class="flex gap-2">
                <button onclick="closeTeacherModal()" class="w-1/2 py-2.5 rounded-xl border border-slate-300 text-slate-700 text-xs font-bold hover:bg-slate-50">
                    Batal
                </button>
                <button onclick="verifyTeacherLogin()" class="w-1/2 py-2.5 rounded-xl bg-indigo-600 hover:bg-indigo-700 text-white text-xs font-bold shadow-sm">
                    Masuk
                </button>
            </div>
        </div>
    </div>

    <!-- PRINT PREVIEW & CERTIFICATE MODAL (FALLBACK & EASY PRINT) -->
    <div id="print-modal" class="hidden fixed inset-0 z-50 bg-slate-900/60 backdrop-blur-sm flex items-center justify-center p-4 overflow-y-auto">
        <div class="bg-white rounded-3xl max-w-lg w-full p-6 sm:p-8 shadow-2xl space-y-6 relative border border-slate-200 my-auto">
            <button type="button" onclick="closePrintModal()" class="no-print absolute top-4 right-4 w-9 h-9 rounded-full bg-slate-100 hover:bg-slate-200 text-slate-600 flex items-center justify-center text-sm font-bold transition-all cursor-pointer z-10">
                <i class="fa-solid fa-xmark"></i>
            </button>

            <!-- Printable Certificate Box -->
            <div id="printable-certificate" class="text-center space-y-4 border-2 border-blue-600 rounded-2xl p-6 bg-blue-50/30">
                <div class="inline-flex items-center gap-2 bg-blue-600 text-white px-3 py-1 rounded-full text-[10px] font-bold tracking-wider uppercase">
                    <i class="fa-solid fa-award"></i> SERTIFIKAT HASIL ASESMEN
                </div>
                <div>
                    <h3 class="text-xl font-black text-slate-800" id="print-modal-name">Nama Siswa</h3>
                    <p class="text-xs text-slate-600 font-semibold mt-0.5" id="print-modal-meta">Kelas • No. Absen</p>
                </div>
                <div class="bg-white rounded-xl p-4 border border-blue-200 shadow-sm max-w-xs mx-auto">
                    <p class="text-[11px] font-bold text-slate-400 uppercase tracking-wider">NILAI AKHIR</p>
                    <p class="text-4xl font-black text-blue-600 my-1" id="print-modal-score">0</p>
                    <p class="text-xs font-bold text-emerald-600" id="print-modal-grade">Predikat: -</p>
                </div>
                <div class="grid grid-cols-3 gap-2 text-left text-xs font-medium text-slate-700 bg-white p-3 rounded-xl border border-slate-200">
                    <div>
                        <span class="text-[10px] text-slate-400 block font-semibold">BENAR</span>
                        <span class="font-bold text-emerald-600" id="print-modal-correct">0 / 30</span>
                    </div>
                    <div>
                        <span class="text-[10px] text-slate-400 block font-semibold">SALAH</span>
                        <span class="font-bold text-rose-600" id="print-modal-wrong">0</span>
                    </div>
                    <div>
                        <span class="text-[10px] text-slate-400 block font-semibold">DURASI</span>
                        <span class="font-bold text-slate-800" id="print-modal-duration">00:00</span>
                    </div>
                </div>
                <p class="text-[11px] text-slate-500 font-medium italic">
                    Asesmen Informatika SD — Perangkat Keras (Hardware)
                </p>
            </div>
        </div>
    </div>

    <!-- FOOTER -->
    <footer class="bg-white border-t border-slate-200 py-4 mt-8 no-print">
        <div class="max-w-7xl mx-auto px-4 text-center text-xs text-slate-500">
            <p>© 2026 InforKids SD — Asesmen Informatika Kurikulum Merdeka Sekolah Dasar.</p>
        </div>
    </footer>

    <!-- JAVASCRIPT LOGIC -->
    <script>
        // BANK SOAL 30 PERANGKAT KERAS (HARDWARE)
        const questionBank = [
            {
                id: 1,
                question: "Alat yang digunakan untuk mengetik huruf, angka, dan simbol pada komputer disebut ....",
                options: ["Monitor", "Keyboard", "Speaker", "Printer"],
                correctAnswer: 1
            },
            {
                id: 2,
                question: "Perangkat keras yang berfungsi untuk menampilkan gambar, teks, dan video di layar komputer adalah ....",
                options: ["Monitor", "Mouse", "Scanner", "Microphone"],
                correctAnswer: 0
            },
            {
                id: 3,
                question: "Perangkat kecil berbentuk genggaman tangan yang digunakan untuk menggerakkan kursor dan melakukan klik dinamakan ....",
                options: ["Keyboard", "CPU", "Mouse", "Speaker"],
                correctAnswer: 2
            },
            {
                id: 4,
                question: "Otak dari komputer yang bertugas memproses semua data dan perintah sistem adalah ....",
                options: ["Monitor", "CPU / System Unit", "Harddisk", "Printer"],
                correctAnswer: 1
            },
            {
                id: 5,
                question: "Alat yang digunakan untuk mencetak tulisan atau gambar dari komputer ke atas kertas dinamakan ....",
                options: ["Scanner", "Printer", "Speaker", "Webcam"],
                correctAnswer: 1
            },
            {
                id: 6,
                question: "Perangkat keras yang menghasilkan suara atau musik dari dalam komputer adalah ....",
                options: ["Microphone", "Speaker", "Monitor", "Webcam"],
                correctAnswer: 1
            },
            {
                id: 7,
                question: "Saat belajar online, alat yang kita gunakan untuk merekam atau memasukkan suara kita ke komputer adalah ....",
                options: ["Speaker", "Microphone", "Headphone", "Projector"],
                correctAnswer: 1
            },
            {
                id: 8,
                question: "Kamera kecil yang dipasang di atas monitor komputer untuk menangkap gambar video siswa saat PJJ adalah ....",
                options: ["Webcam", "Scanner", "Printer", "Monitor"],
                correctAnswer: 0
            },
            {
                id: 9,
                question: "Alat yang digunakan untuk memindai lembar foto atau dokumen cetak menjadi file gambar di komputer adalah ....",
                options: ["Printer", "Scanner", "Speaker", "Mouse"],
                correctAnswer: 1
            },
            {
                id: 10,
                question: "Perangkat yang memancarkan tampilan layar komputer ke dinding atau layar lebar saat presentasi di kelas dinamakan ....",
                options: ["Monitor", "Projector / Proyektor", "TV", "Webcam"],
                correctAnswer: 1
            },
            {
                id: 11,
                question: "Komputer lipat ringkas yang mudah dibawa ke sekolah karena memiliki layar, keyboard, dan baterai sekaligus dinamakan ....",
                options: ["Komputer Desktop", "Laptop", "Mainframe", "Server"],
                correctAnswer: 1
            },
            {
                id: 12,
                question: "Manakah di bawah ini yang termasuk ke dalam kelompok Perangkat Masukan (Input Device)?",
                options: ["Printer", "Speaker", "Keyboard", "Monitor"],
                correctAnswer: 2
            },
            {
                id: 13,
                question: "Manakah di bawah ini yang termasuk ke dalam kelompok Perangkat Keluaran (Output Device)?",
                options: ["Mouse", "Microphone", "Scanner", "Printer"],
                correctAnswer: 3
            },
            {
                id: 14,
                question: "Komponen di dalam CPU yang berfungsi untuk menyimpan file foto, dokumen, dan lagu secara permanen adalah ....",
                options: ["RAM", "Harddisk / SSD", "Processor", "Power Supply"],
                correctAnswer: 1
            },
            {
                id: 15,
                question: "Flashdisk merupakan salah satu contoh dari perangkat keras jenis ....",
                options: ["Perangkat Masukan", "Perangkat Penyimpanan (Storage)", "Perangkat Keluaran", "Perangkat Pemproses"],
                correctAnswer: 1
            },
            {
                id: 16,
                question: "Komputer meja yang terdiri dari layar monitor, CPU terpisah, keyboard, dan mouse yang biasanya diam di atas meja disebut ....",
                options: ["Laptop", "Tablet", "Komputer Desktop", "Smartphone"],
                correctAnswer: 2
            },
            {
                id: 17,
                question: "Perangkat layar sentuh tanpa keyboard fisik yang ukurannya lebih besar dari smartphone adalah ....",
                options: ["Tablet", "Desktop", "Scanner", "Printer"],
                correctAnswer: 0
            },
            {
                id: 18,
                question: "Ani ingin memasukkan foto cetaknya ke dalam laptop untuk tugas sekolah. Alat yang harus digunakan Ani adalah ....",
                options: ["Printer", "Scanner", "Speaker", "Flashdisk"],
                correctAnswer: 1
            },
            {
                id: 19,
                question: "Budi ingin mendengarkan penjelasan guru tanpa mengganggu temannya di dalam ruangan. Alat audio yang dipasang di telinga Budi adalah ....",
                options: ["Headphone / Earphone", "Speaker Aktif", "Microphone", "Webcam"],
                correctAnswer: 0
            },
            {
                id: 20,
                question: "Tombol pada keyboard yang digunakan untuk menghapus satu karakter huruf di sebelah kiri kursor adalah ....",
                options: ["Spacebar", "Enter", "Backspace", "Shift"],
                correctAnswer: 2
            },
            {
                id: 21,
                question: "Tombol paling panjang pada keyboard yang digunakan untuk memberikan jarak spasi antar kata adalah ....",
                options: ["Enter", "Spacebar", "Caps Lock", "Delete"],
                correctAnswer: 1
            },
            {
                id: 22,
                question: "Tindakan mengklik tombol sebelah kanan pada mouse biasanya digunakan untuk ....",
                options: ["Membuka menu opsi / pilihan", "Mematikan komputer", "Menghapus file", "Mengetik huruf"],
                correctAnswer: 0
            },
            {
                id: 23,
                question: "Langkah merawat perangkat keras komputer yang benar agar tidak cepat rusak adalah ....",
                options: ["Menyemprotkan air ke monitor", "Bersihkan dengan kain halus dan tidak makan dekat komputer", "Memukul keyboard saat gemas", "Mencabut kabel paksa"],
                correctAnswer: 1
            },
            {
                id: 24,
                question: "Semua komponen fisik komputer yang dapat kita sentuh dan kita lihat bentuknya secara langsung dinamakan ....",
                options: ["Software", "Hardware (Perangkat Keras)", "Brainware", "Malware"],
                correctAnswer: 1
            },
            {
                id: 25,
                question: "Program atau aplikasi seperti Microsoft Word, Game, dan Chrome masuk ke dalam kelompok ....",
                options: ["Hardware", "Software (Perangkat Lunak)", "Storage Device", "Input Device"],
                correctAnswer: 1
            },
            {
                id: 26,
                question: "Komponen fisik di dalam CPU yang menyalurkan daya listrik ke seluruh bagian komputer adalah ....",
                options: ["RAM", "Power Supply", "Processor", "Harddisk"],
                correctAnswer: 1
            },
            {
                id: 27,
                question: "Papan sirkuit utama di dalam CPU tempat semua komponen seperti prosesor dan memori terhubung dinamakan ....",
                options: ["Motherboard", "Keyboard", "Soundcard", "Harddisk"],
                correctAnswer: 0
            },
            {
                id: 28,
                question: "Perangkat keras yang berguna untuk menghubungkan komputer ke jaringan internet menggunakan kabel atau WiFi adalah ....",
                options: ["Kartu Jaringan (WiFi / Network Card)", "VGA Card", "Sound Card", "Printer"],
                correctAnswer: 0
            },
            {
                id: 29,
                question: "Jika layar monitor komputer kamu redup atau mati karena kabel terlepas, hal pertama yang harus dilakukan adalah ....",
                options: ["Memukul monitor", "Memeriksa colokan kabel daya listrik", "Membuang monitor", "Mengganti keyboard"],
                correctAnswer: 1
            },
            {
                id: 30,
                question: "Sebelum mencabut arus listrik komputer desktop, langkah yang benar untuk mematikan sistem adalah melalui prosedur ....",
                options: ["Langsung cabut stopkontak", "Melakukan perintah Shut Down", "Menekan tombol monitor", "Menutup layar desktop"],
                correctAnswer: 1
            }
        ];

        let currentQuestionIdx = 0;
        let userAnswers = {}; // { qIdx: answerIndex }
        let timerInterval = null;
        let timeLeftSeconds = 1800; // 30 minutes
        let startTime = null;

        // Mock database for teacher view
        let mockStudents = [
            { name: 'Ahmad Raihan', class: '4 Al Halim', absen: '02', correct: 28, wrong: 2, score: 93, duration: '22:15', status: '🟢 Selesai' },
            { name: 'Siti Aisyah', class: '4 Al Latief', absen: '15', correct: 25, wrong: 5, score: 83, duration: '25:10', status: '🟢 Selesai' },
            { name: 'Budi Santoso', class: '5 As Shobur', absen: '08', correct: 22, wrong: 8, score: 73, duration: '28:40', status: '🟢 Selesai' }
        ];

        function showAlert(title, message, icon = 'fa-triangle-exclamation', iconColor = 'bg-amber-100 text-amber-600') {
            document.getElementById('modal-title').innerText = title;
            document.getElementById('modal-message').innerText = message;
            document.getElementById('modal-icon-container').className = `w-10 h-10 rounded-xl flex items-center justify-center text-lg font-bold ${iconColor}`;
            document.getElementById('modal-icon').className = `fa-solid ${icon}`;
            document.getElementById('modal-btn-cancel').classList.add('hidden');
            document.getElementById('modal-btn-confirm').onclick = closeModal;
            document.getElementById('custom-modal').classList.remove('hidden');
        }

        function showConfirm(title, message, onConfirmCallback) {
            document.getElementById('modal-title').innerText = title;
            document.getElementById('modal-message').innerText = message;
            document.getElementById('modal-icon-container').className = "w-10 h-10 rounded-xl bg-blue-100 text-blue-600 flex items-center justify-center text-lg font-bold";
            document.getElementById('modal-icon').className = "fa-solid fa-circle-question";
            
            const cancelBtn = document.getElementById('modal-btn-cancel');
            cancelBtn.classList.remove('hidden');
            
            const confirmBtn = document.getElementById('modal-btn-confirm');
            confirmBtn.onclick = function() {
                closeModal();
                if (onConfirmCallback) onConfirmCallback();
            };

            document.getElementById('custom-modal').classList.remove('hidden');
        }

        function closeModal() {
            document.getElementById('custom-modal').classList.add('hidden');
        }

        function selectClassButton(classVal, btnElement) {
            document.getElementById('student-class').value = classVal;
            
            // Highlight selected class button
            const buttons = document.querySelectorAll('.class-select-btn');
            buttons.forEach(btn => {
                btn.classList.remove('bg-blue-600', 'text-white', 'border-blue-600', 'shadow-md');
                btn.classList.add('bg-white', 'text-slate-700', 'border-slate-200', 'hover:bg-slate-100');
            });

            btnElement.classList.remove('bg-white', 'text-slate-700', 'border-slate-200', 'hover:bg-slate-100');
            btnElement.classList.add('bg-blue-600', 'text-white', 'border-blue-600', 'shadow-md');
        }

        function toggleStartButton() {
            const isChecked = document.getElementById('check-ready').checked;
            document.getElementById('btn-start-quiz').disabled = !isChecked;
        }

        function startQuiz() {
            const studentName = document.getElementById('student-name').value.trim();
            const studentClass = document.getElementById('student-class').value.trim();
            const studentAbsen = document.getElementById('student-absen').value.trim();
            const isReady = document.getElementById('check-ready').checked;

            if (!studentName) {
                showAlert('Nama Belum Diisi', 'Silakan isi Nama Lengkap kamu terlebih dahulu.');
                return;
            }
            if (!studentAbsen) {
                showAlert('Absen Belum Diisi', 'Silakan isi Nomor Absen kamu terlebih dahulu.');
                return;
            }
            if (!studentClass) {
                showAlert('Kelas Belum Dipilih', 'Silakan pilih salah satu tombol kelas terlebih dahulu.');
                return;
            }
            if (!isReady) {
                showAlert('Persetujuan Petunjuk', 'Silakan centang kotak persetujuan petunjuk terlebih dahulu.');
                return;
            }

            currentQuestionIdx = 0;
            userAnswers = {};

            document.getElementById('setup-panel').classList.add('hidden');
            document.getElementById('result-panel').classList.add('hidden');
            document.getElementById('quiz-panel').classList.remove('hidden');
            
            const headerTimer = document.getElementById('quiz-header-timer');
            headerTimer.classList.remove('hidden');
            headerTimer.classList.add('flex');

            startTimer();
            renderQuestionGrid();
            renderQuestion();
        }

        function startTimer() {
            timeLeftSeconds = 1800; // 30 minutes
            startTime = new Date();
            updateTimerDisplay();
            clearInterval(timerInterval);
            
            timerInterval = setInterval(() => {
                timeLeftSeconds--;
                updateTimerDisplay();

                if (timeLeftSeconds <= 0) {
                    clearInterval(timerInterval);
                    showAlert('Waktu Habis!', 'Waktu pengerjaan telah habis (00:00). Jawaban kamu sedang dikumpulkan secara otomatis.', 'fa-clock', 'bg-rose-100 text-rose-600');
                    setTimeout(() => finishQuiz(), 1500);
                }
            }, 1000);
        }

        function updateTimerDisplay() {
            const mins = Math.floor(timeLeftSeconds / 60);
            const secs = timeLeftSeconds % 60;
            const timerText = `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
            
            const timerDisplay = document.getElementById('timer-display');
            const timerContainer = document.getElementById('quiz-header-timer');
            timerDisplay.innerText = timerText;

            // Timer visual warnings
            if (timeLeftSeconds <= 60) {
                // Last 1 minute
                timerContainer.className = "flex items-center gap-2 bg-rose-600 text-white px-3 py-1.5 rounded-xl text-xs font-bold animate-timer-warning shadow-md";
            } else if (timeLeftSeconds <= 300) {
                // Less than 5 mins
                timerContainer.className = "flex items-center gap-2 bg-rose-50 border border-rose-200 text-rose-700 px-3 py-1.5 rounded-xl text-xs font-bold";
            } else if (timeLeftSeconds <= 600) {
                // 5-10 mins
                timerContainer.className = "flex items-center gap-2 bg-amber-50 border border-amber-200 text-amber-700 px-3 py-1.5 rounded-xl text-xs font-bold";
            } else {
                // Normal
                timerContainer.className = "flex items-center gap-2 bg-blue-50 border border-blue-200 text-blue-700 px-3 py-1.5 rounded-xl text-xs font-bold";
            }
        }

        function renderQuestionGrid() {
            const grid = document.getElementById('question-grid');
            grid.innerHTML = '';

            questionBank.forEach((q, idx) => {
                const btn = document.createElement('button');
                btn.onclick = () => jumpToQuestion(idx);
                btn.innerText = idx + 1;
                btn.className = getGridBtnClass(idx);
                grid.appendChild(btn);
            });
        }

        function getGridBtnClass(idx) {
            let base = 'h-9 rounded-lg font-bold text-xs flex items-center justify-center transition-all border ';
            
            if (idx === currentQuestionIdx) {
                base += 'ring-2 ring-blue-600 ring-offset-1 ';
            }

            if (userAnswers[idx] !== undefined && userAnswers[idx] !== null) {
                return base + 'bg-blue-600 text-white border-blue-600 shadow-sm';
            } else {
                return base + 'bg-slate-50 text-slate-700 border-slate-200 hover:bg-slate-100';
            }
        }

        function renderQuestion() {
            const q = questionBank[currentQuestionIdx];
            
            document.getElementById('question-badge').innerText = `Soal ${currentQuestionIdx + 1} dari ${questionBank.length}`;
            
            const progressPct = Math.round(((currentQuestionIdx + 1) / questionBank.length) * 100);
            document.getElementById('progress-bar').style.width = `${progressPct}%`;
            document.getElementById('progress-percent').innerText = `${progressPct}%`;

            document.getElementById('question-text').innerText = `${q.id}. ${q.question}`;

            renderOptions(q);

            document.getElementById('btn-prev').disabled = currentQuestionIdx === 0;

            if (currentQuestionIdx === questionBank.length - 1) {
                document.getElementById('btn-next').classList.add('hidden');
                document.getElementById('btn-finish').classList.remove('hidden');
            } else {
                document.getElementById('btn-next').classList.remove('hidden');
                document.getElementById('btn-finish').classList.add('hidden');
            }

            renderQuestionGrid();
        }

        function renderOptions(q) {
            const container = document.getElementById('options-container');
            container.innerHTML = '';

            const selectedOpt = userAnswers[currentQuestionIdx];

            q.options.forEach((optText, optIdx) => {
                const isSelected = selectedOpt === optIdx;
                const card = document.createElement('div');
                card.onclick = () => selectOption(optIdx);
                card.className = `option-card cursor-pointer border-2 rounded-2xl p-4 flex items-center justify-between transition-all ${
                    isSelected ? 'border-blue-600 bg-blue-50/40 text-blue-900 font-semibold shadow-sm' : 'border-slate-200 hover:border-slate-300 bg-white text-slate-700'
                }`;

                card.innerHTML = `
                    <div class="flex items-center gap-3">
                        <span class="w-8 h-8 rounded-xl border text-xs font-bold flex items-center justify-center ${
                            isSelected ? 'bg-blue-600 text-white border-blue-600' : 'bg-slate-100 text-slate-600 border-slate-200'
                        }">
                            ${String.fromCharCode(65 + optIdx)}
                        </span>
                        <span class="text-xs sm:text-sm font-medium">${optText}</span>
                    </div>
                    <div class="w-5 h-5 rounded-full border flex items-center justify-center ${
                        isSelected ? 'border-blue-600 bg-blue-600 text-white' : 'border-slate-300'
                    }">
                        ${isSelected ? '<i class="fa-solid fa-check text-[10px]"></i>' : ''}
                    </div>
                `;
                container.appendChild(card);
            });
        }

        function selectOption(optIdx) {
            userAnswers[currentQuestionIdx] = optIdx;
            renderQuestion();
        }

        function navigateQuestion(direction) {
            const newIdx = currentQuestionIdx + direction;
            if (newIdx >= 0 && newIdx < questionBank.length) {
                currentQuestionIdx = newIdx;
                renderQuestion();
            }
        }

        function jumpToQuestion(idx) {
            currentQuestionIdx = idx;
            renderQuestion();
        }

        function confirmFinishQuiz() {
            const totalQ = questionBank.length;
            const answeredCount = Object.keys(userAnswers).length;
            const unansweredCount = totalQ - answeredCount;

            const mins = Math.floor(timeLeftSeconds / 60);
            const secs = timeLeftSeconds % 60;
            const remainingTime = `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;

            let msg = `Apakah kamu yakin ingin mengumpulkan jawaban?\n\n• Sudah dijawab: ${answeredCount}/${totalQ}\n• Belum dijawab: ${unansweredCount}/${totalQ}\n• Waktu tersisa: ${remainingTime}`;
            
            if (unansweredCount > 0) {
                msg += `\n\nMasih ada ${unansweredCount} soal yang belum dijawab!`;
            }

            showConfirm('Konfirmasi Pengumpulan', msg, () => finishQuiz());
        }

        function finishQuiz() {
            clearInterval(timerInterval);
            document.getElementById('quiz-header-timer').classList.add('hidden');
            document.getElementById('quiz-panel').classList.add('hidden');
            document.getElementById('result-panel').classList.remove('hidden');

            let correctCount = 0;
            questionBank.forEach((q, idx) => {
                if (userAnswers[idx] === q.correctAnswer) {
                    correctCount++;
                }
            });

            const totalQ = questionBank.length;
            const wrongCount = totalQ - correctCount;
            const scoreVal = Math.round((correctCount / totalQ) * 100);

            // Time spent
            const endTime = new Date();
            const timeDiffSec = Math.round((endTime - startTime) / 1000);
            const spentMins = Math.floor(timeDiffSec / 60);
            const spentSecs = timeDiffSec % 60;
            const durationStr = `${spentMins.toString().padStart(2, '0')}:${spentSecs.toString().padStart(2, '0')}`;

            document.getElementById('time-spent-display').innerText = `${spentMins}m ${spentSecs}s`;

            const sName = document.getElementById('student-name').value.trim() || 'Siswa SD';
            const sClass = document.getElementById('student-class').value.trim() || '-';
            const sAbsen = document.getElementById('student-absen').value.trim() || '-';

            document.getElementById('result-student-name').innerText = sName;
            document.getElementById('result-meta').innerText = `Kelas: ${sClass} • No. Absen: ${sAbsen} • Perangkat Keras Komputer`;
            document.getElementById('score-val').innerText = scoreVal;
            document.getElementById('correct-count').innerText = `${correctCount} / ${totalQ}`;
            document.getElementById('wrong-count').innerText = wrongCount;

            let predikat = 'Cukup';
            if (scoreVal >= 85) predikat = 'Sangat Baik';
            else if (scoreVal >= 70) predikat = 'Baik';

            document.getElementById('score-grade').innerText = `Predikat: ${predikat}`;

            // Trigger Confetti
            if (scoreVal >= 70 && typeof confetti === 'function') {
                confetti({ particleCount: 100, spread: 70, origin: { y: 0.6 } });
            }

            // Push to teacher mock records
            mockStudents.unshift({
                name: sName,
                class: sClass,
                absen: sAbsen,
                correct: correctCount,
                wrong: wrongCount,
                score: scoreVal,
                duration: durationStr,
                status: '🟢 Selesai'
            });
        }

        function printResult() {
            // Populate data to print modal
            const sName = document.getElementById('result-student-name').innerText;
            const sMeta = document.getElementById('result-meta').innerText;
            const score = document.getElementById('score-val').innerText;
            const grade = document.getElementById('score-grade').innerText;
            const correct = document.getElementById('correct-count').innerText;
            const wrong = document.getElementById('wrong-count').innerText;
            const duration = document.getElementById('time-spent-display').innerText;

            document.getElementById('print-modal-name').innerText = sName;
            document.getElementById('print-modal-meta').innerText = sMeta;
            document.getElementById('print-modal-score').innerText = score;
            document.getElementById('print-modal-grade').innerText = grade;
            document.getElementById('print-modal-correct').innerText = correct;
            document.getElementById('print-modal-wrong').innerText = wrong;
            document.getElementById('print-modal-duration').innerText = duration;

            // Open Print Modal directly
            document.getElementById('print-modal').classList.remove('hidden');
        }

        function closePrintModal() {
            document.getElementById('print-modal').classList.add('hidden');
        }

        function triggerBrowserPrint() {
            try {
                window.print();
            } catch (e) {
                console.warn("Direct window.print failed, using fallback:", e);
                openPrintWindowFallback();
            }
        }

        function openPrintWindowFallback() {
            const sName = document.getElementById('print-modal-name').innerText;
            const sMeta = document.getElementById('print-modal-meta').innerText;
            const score = document.getElementById('print-modal-score').innerText;
            const correct = document.getElementById('print-modal-correct').innerText;
            const wrong = document.getElementById('print-modal-wrong').innerText;
            const duration = document.getElementById('print-modal-duration').innerText;

            const printWin = window.open('', '_blank');
            if (printWin) {
                printWin.document.write(`
                    <!DOCTYPE html>
                    <html lang="id">
                    <head>
                        <meta charset="UTF-8">
                        <title>Hasil Asesmen - ${sName}</title>
                        <style>
                            body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; padding: 40px; color: #1e293b; background: #fff; }
                            .card { border: 2px solid #2563eb; border-radius: 20px; padding: 30px; max-width: 600px; margin: 0 auto; text-align: center; }
                            h1 { color: #1d4ed8; margin-bottom: 5px; font-size: 24px; }
                            p { color: #64748b; margin: 5px 0; font-size: 14px; }
                            .score-box { background: #eff6ff; border: 1px solid #bfdbfe; border-radius: 16px; padding: 20px; margin: 20px 0; }
                            .score-num { font-size: 48px; font-weight: 800; color: #2563eb; }
                            .details { text-align: left; margin-top: 20px; font-size: 14px; line-height: 1.8; border-top: 1px solid #e2e8f0; padding-top: 15px; }
                            .footer { margin-top: 25px; font-size: 11px; color: #94a3b8; }
                        </style>
                    </head>
                    <body>
                        <div class="card">
                            <h1>HASIL ASESMEN INFORMATIKA SD</h1>
                            <p>Sistem Komputer – Perangkat Keras (Hardware)</p>
                            
                            <div class="score-box">
                                <h2 style="margin:0; font-size: 20px; color: #1e293b;">${sName}</h2>
                                <p style="margin-bottom:10px;">${sMeta}</p>
                                <div class="score-num">${score}</div>
                            </div>

                            <div class="details">
                                <p><strong>• Jawaban Benar:</strong> ${correct}</p>
                                <p><strong>• Jawaban Salah:</strong> ${wrong}</p>
                                <p><strong>• Durasi Pengerjaan:</strong> ${duration}</p>
                            </div>

                            <div class="footer">
                                Dicetak otomatis dari Aplikasi Asesmen Informatika SD
                            </div>
                        </div>
                        <script>
                            window.onload = function() {
                                window.print();
                            }
                        <\/script>
                    </body>
                    </html>
                `);
                printWin.document.close();
            } else {
                showAlert('Cetak Hasil', 'Silakan gunakan pintasan keyboard Ctrl + P (atau Cmd + P di Mac) untuk mencetak atau menyimpan dokumen sebagai PDF.');
            }
        }

        function openTeacherLogin() {
            document.getElementById('teacher-modal').classList.remove('hidden');
        }

        function closeTeacherModal() {
            document.getElementById('teacher-modal').classList.add('hidden');
        }

        function verifyTeacherLogin() {
            const pass = document.getElementById('teacher-password').value;
            if (pass === 'guru123' || pass === 'admin') {
                closeTeacherModal();
                switchView('teacher');
            } else {
                showAlert('Password Salah', 'Password yang kamu masukkan tidak sesuai (Default: guru123).');
            }
        }

        function switchView(viewMode) {
            if (viewMode === 'teacher') {
                document.getElementById('student-view').classList.add('hidden');
                document.getElementById('teacher-view').classList.remove('hidden');
                renderTeacherStats();
            } else {
                document.getElementById('teacher-view').classList.add('hidden');
                document.getElementById('student-view').classList.remove('hidden');
            }
        }

        function renderTeacherStats() {
            const filterClass = document.getElementById('filter-class').value;
            let filtered = mockStudents;
            if (filterClass !== 'all') {
                filtered = mockStudents.filter(s => s.class === filterClass);
            }

            document.getElementById('t-total-students').innerText = filtered.length;

            if (filtered.length > 0) {
                const avg = Math.round(filtered.reduce((acc, curr) => acc + curr.score, 0) / filtered.length);
                const max = Math.max(...filtered.map(s => s.score));
                const min = Math.min(...filtered.map(s => s.score));

                document.getElementById('t-avg-score').innerText = avg;
                document.getElementById('t-high-score').innerText = max;
                document.getElementById('t-low-score').innerText = min;
            } else {
                document.getElementById('t-avg-score').innerText = '0';
                document.getElementById('t-high-score').innerText = '0';
                document.getElementById('t-low-score').innerText = '0';
            }

            const tbody = document.getElementById('student-table-body');
            tbody.innerHTML = '';

            filtered.forEach((s, idx) => {
                const tr = document.createElement('tr');
                tr.className = "hover:bg-slate-50 transition-all";
                tr.innerHTML = `
                    <td class="px-4 py-3 font-semibold text-slate-500">${idx + 1}</td>
                    <td class="px-4 py-3 font-bold text-slate-800">${s.name}</td>
                    <td class="px-4 py-3 font-semibold text-slate-600">${s.class}</td>
                    <td class="px-4 py-3 text-center font-medium">${s.absen}</td>
                    <td class="px-4 py-3 text-center font-bold text-emerald-600">${s.correct}</td>
                    <td class="px-4 py-3 text-center font-bold text-rose-600">${s.wrong}</td>
                    <td class="px-4 py-3 text-center font-black text-blue-600 text-sm">${s.score}</td>
                    <td class="px-4 py-3 text-center font-medium">${s.duration}</td>
                    <td class="px-4 py-3 text-center font-semibold">${s.status}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function filterStudentTable() {
            const query = document.getElementById('search-student').value.toLowerCase();
            const rows = document.querySelectorAll('#student-table-body tr');
            rows.forEach(row => {
                const nameText = row.children[1].innerText.toLowerCase();
                row.style.display = nameText.includes(query) ? '' : 'none';
            });
        }

        function exportExcel() {
            if (typeof XLSX === 'undefined') {
                showAlert('Modul Excel Error', 'Modul export Excel belum siap. Silakan periksa koneksi jaringan.');
                return;
            }

            const dataToExport = mockStudents.map((s, idx) => ({
                'No': idx + 1,
                'Nama Siswa': s.name,
                'Kelas': s.class,
                'No. Absen': s.absen,
                'Benar': s.correct,
                'Salah': s.wrong,
                'Nilai': s.score,
                'Durasi': s.duration,
                'Status': s.status
            }));

            const worksheet = XLSX.utils.json_to_sheet(dataToExport);
            const workbook = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(workbook, worksheet, "Hasil Asesmen");
            XLSX.writeFile(workbook, "Hasil_Asesmen_Informatika_Sistem_Komputer.xlsx");
        }

        // INITIAL ONLOAD
        window.onload = function() {
            toggleStartButton();
        };
    </script>
</body>
</html>
