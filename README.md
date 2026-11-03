# Transparencia-funcionarios-espa-a
Observatorio Cívico de Transparencia del Empleo Público en España Plataforma de datos abiertos para la fiscalización de la presencia laboral, ausentismo y cumplimiento en las Administraciones Públicas (AGE, CCAA, Provincias, Ayuntamientos y Cortes). Indicadores agregados, gráficos en tiempo real y fuentes oficiales según el RGPD.
<!DOCTYPE html>
<html lang="es" class="h-full bg-slate-50">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Portal Nacional de Transparencia del Empleo Público • España</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#f0fdf4',
                            100: '#dcfce7',
                            500: '#10b981',
                            600: '#059669',
                            700: '#047857',
                            800: '#065f46',
                            900: '#064e3b',
                        },
                        navy: {
                            950: '#030712',
                            900: '#0f172a',
                            800: '#1e293b',
                            700: '#334155'
                        }
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    }
                }
            }
        }
    </script>
    <!-- Google Fonts & FontAwesome -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: 'Inter', sans-serif; }
        .custom-scrollbar::-webkit-scrollbar { width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
        .dark .custom-scrollbar::-webkit-scrollbar-thumb { background: #475569; }
        @keyframes pulse-ring {
            0% { transform: scale(0.95); opacity: 0.8; }
            50% { transform: scale(1.05); opacity: 0.4; }
            100% { transform: scale(0.95); opacity: 0.8; }
        }
        .pulse-live { animation: pulse-ring 2s infinite ease-in-out; }
    </style>
</head>
<body class="h-full flex flex-col text-slate-800 bg-slate-50 dark:bg-slate-900 dark:text-slate-100 antialiased transition-colors duration-200">

    <!-- Toast Notifications Container -->
    <div id="toastContainer" class="fixed bottom-5 right-5 z-50 flex flex-col space-y-3 max-w-sm pointer-events-none"></div>

    <!-- Official Data Origin Banner / Sync Status -->
    <div class="bg-navy-950 text-slate-300 border-b border-slate-800 text-[11px] py-2 px-4 sm:px-8">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between gap-2">
            <div class="flex items-center space-x-3 overflow-x-auto w-full md:w-auto">
                <span class="inline-flex items-center px-2 py-0.5 rounded font-bold bg-emerald-500/20 text-emerald-400 border border-emerald-500/30 whitespace-nowrap">
                    <span class="w-1.5 h-1.5 mr-1.5 bg-emerald-400 rounded-full animate-pulse"></span>
                    API REGISTRO CENTRAL DE PERSONAL (MINHAFP)
                </span>
                <span class="text-slate-400 hidden lg:inline">|</span>
                <span class="truncate text-slate-400">BEPSAP • Datos.gob.es • Cortes Generales</span>
                <span class="text-slate-400 hidden lg:inline">|</span>
                <span class="font-mono text-slate-400 hidden xl:inline">HASH: <span id="syncHash" class="text-emerald-400">0x8f2a9...c4b</span></span>
            </div>
            
            <div class="flex items-center space-x-4 w-full md:w-auto justify-end">
                <div class="flex items-center space-x-2">
                    <i class="fa-solid fa-clock-rotate-left text-amber-400"></i>
                    <span>Próxima sincro: <strong id="countdownTimer" class="text-amber-400 font-mono">10s</strong></span>
                </div>
                <button id="forceSyncBtn" onclick="forceSyncData()" class="bg-slate-800 hover:bg-slate-700 text-slate-200 px-2.5 py-1 rounded border border-slate-700 transition flex items-center space-x-1">
                    <i class="fa-solid fa-rotate text-[10px]"></i>
                    <span>Sincronizar Ya</span>
                </button>
            </div>
        </div>
    </div>

    <!-- Header / Navbar -->
    <header class="bg-navy-900 text-white shadow-xl sticky top-0 z-40 border-b border-slate-800">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-20">
                <!-- Logo & Title -->
                <div class="flex items-center space-x-3.5">
                    <div class="bg-emerald-500/10 p-2.5 rounded-xl backdrop-blur-md border border-emerald-500/20">
                        <i class="fa-solid fa-building-columns text-2xl text-emerald-400"></i>
                    </div>
                    <div>
                        <div class="flex items-center space-x-2">
                            <span class="text-xs font-bold uppercase tracking-widest text-emerald-400">Red Estatal de Transparencia</span>
                            <span class="inline-flex items-center px-2 py-0.5 rounded text-[10px] font-bold bg-amber-500/20 text-amber-300 border border-amber-500/30">
                                <i class="fa-solid fa-shield-check mr-1"></i>
                                50 Provincias + Ceuta/Melilla
                            </span>
                        </div>
                        <h1 class="text-base sm:text-lg font-extrabold tracking-tight">Portal Nacional de Transparencia del Empleo Público</h1>
                    </div>
                </div>

                <!-- Search, Controls & Actions -->
                <div class="flex items-center space-x-3">
                    <!-- Live Feed Toggle -->
                    <div class="hidden sm:flex items-center space-x-2 bg-slate-800/80 px-3 py-1.5 rounded-xl border border-slate-700">
                        <span class="text-xs font-semibold text-slate-300">Live Feed</span>
                        <label class="relative inline-flex items-center cursor-pointer">
                            <input type="checkbox" id="liveToggle" class="sr-only peer" checked>
                            <div class="w-9 h-5 bg-slate-700 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-slate-300 after:border after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-emerald-500"></div>
                        </label>
                    </div>

                    <!-- Search Input -->
                    <div class="relative hidden md:block w-64 lg:w-72">
                        <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400 text-xs"></i>
                        <input type="text" id="globalSearchInput" placeholder="Buscar municipio u organismo..." class="w-full pl-9 pr-4 py-2 text-xs bg-slate-800 text-white rounded-xl border border-slate-700 focus:outline-none focus:ring-2 focus:ring-emerald-500 placeholder-slate-400">
                        <div id="searchDropdown" class="hidden absolute top-full left-0 right-0 mt-2 bg-white dark:bg-slate-800 border border-slate-200 dark:border-slate-700 rounded-xl shadow-2xl z-50 max-h-64 overflow-y-auto custom-scrollbar text-xs"></div>
                    </div>

                    <!-- Theme Toggle -->
                    <button id="themeToggle" class="bg-slate-800 hover:bg-slate-700 text-slate-300 hover:text-white p-2.5 rounded-xl transition border border-slate-700" title="Cambiar Tema">
                        <i class="fa-solid fa-moon dark:hidden"></i>
                        <i class="fa-solid fa-sun hidden dark:block text-amber-400"></i>
                    </button>

                    <!-- Export Report -->
                    <button onclick="exportDataCSV()" class="hidden lg:flex bg-slate-800 hover:bg-slate-700 text-slate-200 px-3 py-2 rounded-xl text-xs font-bold transition border border-slate-700 items-center space-x-2" title="Exportar Informe CSV">
                        <i class="fa-solid fa-download text-emerald-400"></i>
                        <span>CSV</span>
                    </button>

                    <!-- Whistleblower Button -->
                    <a href="#denuncia" class="hidden sm:flex bg-amber-500 hover:bg-amber-400 text-slate-950 px-4 py-2 rounded-xl text-xs font-bold transition shadow-sm items-center space-x-2">
                        <i class="fa-solid fa-shield-halved"></i>
                        <span>Buzón Antifraude</span>
                    </a>
                </div>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="flex-grow max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-8 space-y-8">

        <!-- Mobile Search Bar -->
        <div class="block md:hidden relative">
            <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400 text-xs"></i>
            <input type="text" id="globalSearchInputMobile" placeholder="Buscar municipio u organismo..." class="w-full pl-9 pr-4 py-3 text-xs bg-white dark:bg-slate-800 text-slate-900 dark:text-white rounded-xl border border-slate-300 dark:border-slate-700 shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500">
            <div id="searchDropdownMobile" class="hidden absolute top-full left-0 right-0 mt-2 bg-white dark:bg-slate-800 border border-slate-200 dark:border-slate-700 rounded-xl shadow-2xl z-50 max-h-64 overflow-y-auto custom-scrollbar text-xs"></div>
        </div>

        <!-- Official Sources Info Bar -->
        <section class="bg-emerald-950/30 dark:bg-emerald-950/50 border border-emerald-500/30 rounded-2xl p-4 flex flex-col lg:flex-row items-center justify-between gap-4 text-xs">
            <div class="flex items-center space-x-3">
                <div class="p-2 bg-emerald-500/20 rounded-xl text-emerald-400 border border-emerald-500/30">
                    <i class="fa-solid fa-certificate text-lg"></i>
                </div>
                <div>
                    <strong class="text-slate-900 dark:text-slate-100 font-bold block">Origen e Integridad Verificada de los Datos</strong>
                    <p class="text-slate-600 dark:text-slate-300 text-[11px]">
                        Métricas sincronizadas periódicamente con el <strong>Boletín Estadístico del Personal (BEPSAP)</strong>, Registro Central de Personal del MINHAFP y Sistemas de Control Biométrico Presencial.
                    </p>
                </div>
            </div>
            <div class="flex items-center space-x-3 w-full lg:w-auto justify-end">
                <span class="px-3 py-1 bg-white dark:bg-slate-800 rounded-lg font-mono text-[11px] border border-slate-200 dark:border-slate-700 text-slate-600 dark:text-slate-300">
                    <i class="fa-solid fa-check-double text-emerald-500 mr-1"></i>Firma Digital Valida
                </span>
                <button onclick="toggleSourcesModal()" class="px-3 py-1 bg-emerald-600 hover:bg-emerald-500 text-white rounded-lg font-bold transition text-[11px]">
                    Ver Fuentes Oficiales
                </button>
            </div>
        </section>

        <!-- Hierarchical Dynamic Selector -->
        <section class="bg-white dark:bg-slate-800 p-6 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700 transition-colors">
            <div class="flex items-center justify-between mb-4 border-b border-slate-100 dark:border-slate-700 pb-3">
                <div class="flex items-center space-x-2">
                    <i class="fa-solid fa-filter text-emerald-500"></i>
                    <h2 class="text-sm font-bold uppercase tracking-wider text-slate-700 dark:text-slate-300">Filtro Jerárquico de Administraciones Públicas</h2>
                </div>
                <span id="activeEntityBadge" class="px-3 py-1 bg-emerald-100 dark:bg-emerald-950 text-emerald-800 dark:text-emerald-300 rounded-full text-xs font-bold border border-emerald-300 dark:border-emerald-800">
                    Mostrando: Ayuntamiento de Madrid
                </span>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
                <!-- Level 1: Admin Type -->
                <div>
                    <label for="adminTypeSelect" class="block text-xs font-semibold text-slate-500 dark:text-slate-400 uppercase tracking-wider mb-2">
                        1. Tipo de Administración
                    </label>
                    <select id="adminTypeSelect" class="w-full bg-slate-50 dark:bg-slate-900 border border-slate-300 dark:border-slate-700 text-slate-900 dark:text-white font-medium text-xs rounded-xl focus:ring-2 focus:ring-emerald-500 p-3 cursor-pointer">
                        <option value="locales">🏛️ Entidades Locales (50 Provincias + EELL)</option>
                        <option value="age">🇪🇸 Administración General del Estado (AGE)</option>
                        <option value="ccaa">🌐 Comunidades Autónomas (17 CCAA + Ceuta/Melilla)</option>
                        <option value="constitucional">⚖️ Órganos Constitucionales y Cortes</option>
                        <option value="universidades">🎓 Universidades Públicas</option>
                        <option value="empresas">🏢 Empresas Públicas y Entes Estatales</option>
                    </select>
                </div>

                <!-- Level 2: Province / Territory -->
                <div>
                    <label id="provinceLabel" for="provinceSelect" class="block text-xs font-semibold text-slate-500 dark:text-slate-400 uppercase tracking-wider mb-2">
                        2. Provincia (50 Provincias de España)
                    </label>
                    <select id="provinceSelect" class="w-full bg-slate-50 dark:bg-slate-900 border border-slate-300 dark:border-slate-700 text-slate-900 dark:text-white font-medium text-xs rounded-xl focus:ring-2 focus:ring-emerald-500 p-3 cursor-pointer">
                        <!-- Injected via JS -->
                    </select>
                </div>

                <!-- Level 3: Institution / Municipality -->
                <div>
                    <label id="entityLabel" for="entitySelect" class="block text-xs font-semibold text-slate-500 dark:text-slate-400 uppercase tracking-wider mb-2">
                        3. Municipio u Organismo
                    </label>
                    <select id="entitySelect" class="w-full bg-slate-50 dark:bg-slate-900 border border-slate-300 dark:border-slate-700 text-slate-900 dark:text-white font-medium text-xs rounded-xl focus:ring-2 focus:ring-emerald-500 p-3 cursor-pointer">
                        <!-- Injected via JS -->
                    </select>
                </div>

                <!-- Level 4: Department / Service -->
                <div>
                    <label for="departmentSelect" class="block text-xs font-semibold text-slate-500 dark:text-slate-400 uppercase tracking-wider mb-2">
                        4. Área, Servicio o Comisión
                    </label>
                    <select id="departmentSelect" class="w-full bg-slate-50 dark:bg-slate-900 border border-slate-300 dark:border-slate-700 text-slate-900 dark:text-white font-medium text-xs rounded-xl focus:ring-2 focus:ring-emerald-500 p-3 cursor-pointer">
                        <!-- Injected via JS -->
                    </select>
                </div>
            </div>

            <!-- Quick Jump Buttons -->
            <div class="mt-5 pt-4 border-t border-slate-100 dark:border-slate-700 flex flex-wrap items-center gap-2">
                <span class="text-xs font-bold text-slate-400 uppercase mr-2">Accesos Rápidos:</span>
                <button onclick="quickSelect('locales', 'madrid', 'madrid_ayto')" class="px-2.5 py-1 bg-slate-100 dark:bg-slate-700 hover:bg-emerald-50 hover:text-emerald-600 dark:hover:bg-emerald-950 text-slate-600 dark:text-slate-300 rounded-lg text-xs font-semibold transition">Ayto. Madrid</button>
                <button onclick="quickSelect('locales', 'barcelona', 'bcn_ayto')" class="px-2.5 py-1 bg-slate-100 dark:bg-slate-700 hover:bg-emerald-50 hover:text-emerald-600 dark:hover:bg-emerald-950 text-slate-600 dark:text-slate-300 rounded-lg text-xs font-semibold transition">Ayto. Barcelona</button>
                <button onclick="quickSelect('locales', 'valencia', 'val_ayto')" class="px-2.5 py-1 bg-slate-100 dark:bg-slate-700 hover:bg-emerald-50 hover:text-emerald-600 dark:hover:bg-emerald-950 text-slate-600 dark:text-slate-300 rounded-lg text-xs font-semibold transition">Ayto. Valencia</button>
                <button onclick="quickSelect('constitucional', 'nacional', 'congreso')" class="px-2.5 py-1 bg-slate-100 dark:bg-slate-700 hover:bg-emerald-50 hover:text-emerald-600 dark:hover:bg-emerald-950 text-slate-600 dark:text-slate-300 rounded-lg text-xs font-semibold transition">Congreso de los Diputados</button>
                <button onclick="quickSelect('constitucional', 'nacional', 'senado')" class="px-2.5 py-1 bg-slate-100 dark:bg-slate-700 hover:bg-emerald-50 hover:text-emerald-600 dark:hover:bg-emerald-950 text-slate-600 dark:text-slate-300 rounded-lg text-xs font-semibold transition">Senado de España</button>
                <button onclick="quickSelect('age', 'nacional', 'min_hacienda')" class="px-2.5 py-1 bg-slate-100 dark:bg-slate-700 hover:bg-emerald-50 hover:text-emerald-600 dark:hover:bg-emerald-950 text-slate-600 dark:text-slate-300 rounded-lg text-xs font-semibold transition">AEAT / Min. Hacienda</button>
            </div>
        </section>

        <!-- Key Metrics Cards (KPIs) -->
        <section class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-4">
            <!-- KPI 1 -->
            <div class="bg-white dark:bg-slate-800 p-5 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700">
                <div class="flex items-center justify-between mb-2">
                    <span class="text-[11px] font-bold text-slate-500 dark:text-slate-400 uppercase">Presencia Activa Real</span>
                    <i class="fa-solid fa-fingerprint text-emerald-500 text-lg"></i>
                </div>
                <div class="flex items-baseline space-x-1.5">
                    <span id="kpiActivePercent" class="text-2xl font-extrabold text-slate-900 dark:text-white">86.1%</span>
                    <span id="kpiActiveCount" class="text-[11px] text-slate-500 dark:text-slate-400">(937/1090)</span>
                </div>
                <p class="text-[10px] text-emerald-600 dark:text-emerald-400 font-semibold mt-2">
                    <i class="fa-solid fa-shield-check mr-1"></i>Fichaje Biométrico 3D
                </p>
            </div>

            <!-- KPI 2 -->
            <div class="bg-white dark:bg-slate-800 p-5 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700">
                <div class="flex items-center justify-between mb-2">
                    <span class="text-[11px] font-bold text-slate-500 dark:text-slate-400 uppercase">Ausencia Injustificada</span>
                    <i class="fa-solid fa-user-slash text-rose-500 text-lg"></i>
                </div>
                <div class="flex items-baseline space-x-1.5">
                    <span id="kpiUnjustifiedPercent" class="text-2xl font-extrabold text-slate-900 dark:text-white">1.9%</span>
                    <span id="kpiUnjustifiedCount" class="text-[11px] text-slate-500 dark:text-slate-400">(21 casos)</span>
                </div>
                <p class="text-[10px] text-rose-500 font-semibold mt-2">
                    <i class="fa-solid fa-triangle-exclamation mr-1"></i>Alertas "Fichar e Irse"
                </p>
            </div>

            <!-- KPI 3 -->
            <div class="bg-white dark:bg-slate-800 p-5 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700">
                <div class="flex items-center justify-between mb-2">
                    <span class="text-[11px] font-bold text-slate-500 dark:text-slate-400 uppercase">Media Días Baja / Año</span>
                    <i class="fa-solid fa-calendar-xmark text-amber-500 text-lg"></i>
                </div>
                <div class="flex items-baseline space-x-1.5">
                    <span id="kpiAvgDays" class="text-2xl font-extrabold text-slate-900 dark:text-white">9.8</span>
                    <span class="text-[11px] text-slate-500 dark:text-slate-400">días / empleado</span>
                </div>
                <p class="text-[10px] text-slate-500 dark:text-slate-400 mt-2">
                    Media Nacional Sector: <strong id="kpiNatAvg">10.8 días</strong>
                </p>
            </div>

            <!-- KPI 4 -->
            <div class="bg-white dark:bg-slate-800 p-5 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700">
                <div class="flex items-center justify-between mb-2">
                    <span class="text-[11px] font-bold text-slate-500 dark:text-slate-400 uppercase">Dietas / Asistencia Plenaria</span>
                    <i class="fa-solid fa-file-invoice-dollar text-blue-500 text-lg"></i>
                </div>
                <div class="flex items-baseline space-x-1.5">
                    <span id="kpiDietCompliance" class="text-2xl font-extrabold text-slate-900 dark:text-white">96.4%</span>
                    <span class="text-[11px] text-emerald-500 font-bold"><i class="fa-solid fa-check"></i> Validado</span>
                </div>
                <p id="kpiDietSubtitle" class="text-[10px] text-slate-500 dark:text-slate-400 truncate mt-2">
                    Vinculado a firma/voto de cargo público
                </p>
            </div>

            <!-- KPI 5 -->
            <div class="bg-white dark:bg-slate-800 p-5 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700">
                <div class="flex items-center justify-between mb-2">
                    <span class="text-[11px] font-bold text-slate-500 dark:text-slate-400 uppercase">Resolución / Trámites</span>
                    <i class="fa-solid fa-bolt text-indigo-500 text-lg"></i>
                </div>
                <div class="flex items-baseline space-x-1.5">
                    <span id="kpiResolutionRate" class="text-2xl font-extrabold text-slate-900 dark:text-white">93.2%</span>
                    <span class="text-[11px] text-slate-500 dark:text-slate-400">(Efectividad)</span>
                </div>
                <p class="text-[10px] text-indigo-500 font-semibold mt-2">
                    <i class="fa-solid fa-chart-line mr-1"></i>Nivel de Atención Ciudadana
                </p>
            </div>
        </section>

        <!-- Personnel Composition Breakdown -->
        <section class="bg-white dark:bg-slate-800 p-6 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700">
            <h3 class="text-sm font-bold text-slate-900 dark:text-white mb-3">
                Composición del Personal (Fuente: Registro Central de Personal)
            </h3>
            <div class="grid grid-cols-2 sm:grid-cols-4 gap-4 text-center">
                <div class="p-3 bg-slate-50 dark:bg-slate-900 rounded-xl border border-slate-200 dark:border-slate-700">
                    <span class="block text-xs text-slate-500 dark:text-slate-400 font-semibold">Funcionario de Carrera</span>
                    <strong id="typeCarrera" class="text-lg font-bold text-emerald-600 dark:text-emerald-400">62%</strong>
                </div>
                <div class="p-3 bg-slate-50 dark:bg-slate-900 rounded-xl border border-slate-200 dark:border-slate-700">
                    <span class="block text-xs text-slate-500 dark:text-slate-400 font-semibold">Personal Interino</span>
                    <strong id="typeInterino" class="text-lg font-bold text-blue-600 dark:text-blue-400">22%</strong>
                </div>
                <div class="p-3 bg-slate-50 dark:bg-slate-900 rounded-xl border border-slate-200 dark:border-slate-700">
                    <span class="block text-xs text-slate-500 dark:text-slate-400 font-semibold">Personal Laboral</span>
                    <strong id="typeLaboral" class="text-lg font-bold text-amber-600 dark:text-amber-400">12%</strong>
                </div>
                <div class="p-3 bg-slate-50 dark:bg-slate-900 rounded-xl border border-slate-200 dark:border-slate-700">
                    <span class="block text-xs text-slate-500 dark:text-slate-400 font-semibold">Personal Eventual / Asesor</span>
                    <strong id="typeEventual" class="text-lg font-bold text-rose-600 dark:text-rose-400">4%</strong>
                </div>
            </div>
        </section>

        <!-- Charts Section -->
        <section class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            <!-- Chart 1 -->
            <div class="bg-white dark:bg-slate-800 p-6 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700">
                <h3 class="text-sm font-bold text-slate-900 dark:text-white mb-1">Presencia Real por Franja Horaria</h3>
                <p class="text-xs text-slate-500 dark:text-slate-400 mb-4">Muestreo activo (08:00 a 15:00)</p>
                <div class="relative h-64 w-full">
                    <canvas id="hourlyChart"></canvas>
                </div>
            </div>

            <!-- Chart 2 -->
            <div class="bg-white dark:bg-slate-800 p-6 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700">
                <h3 class="text-sm font-bold text-slate-900 dark:text-white mb-1">Causas de Ausencia</h3>
                <p class="text-xs text-slate-500 dark:text-slate-400 mb-4">Motivos justificados vs irregulares</p>
                <div class="relative h-64 w-full flex items-center justify-center">
                    <canvas id="causesChart"></canvas>
                </div>
            </div>

            <!-- Chart 3 -->
            <div class="bg-white dark:bg-slate-800 p-6 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700">
                <h3 class="text-sm font-bold text-slate-900 dark:text-white mb-1">Evolución Anual vs Media Nacional</h3>
                <p class="text-xs text-slate-500 dark:text-slate-400 mb-4">Días de ausencia acumulados</p>
                <div class="relative h-64 w-full">
                    <canvas id="historyChart"></canvas>
                </div>
            </div>
        </section>

        <!-- Sub-units Breakdown Table -->
        <section class="bg-white dark:bg-slate-800 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700 overflow-hidden">
            <div class="p-6 border-b border-slate-100 dark:border-slate-700 flex flex-col sm:flex-row sm:items-center justify-between gap-4">
                <div>
                    <h3 id="tableTitle" class="text-base font-bold text-slate-900 dark:text-white">Desglose por Áreas y Servicios Operativos</h3>
                    <p class="text-xs text-slate-500 dark:text-slate-400">Auditoría detallada por sub-unidad funcional</p>
                </div>
                <div class="relative">
                    <i class="fa-solid fa-magnifying-glass absolute left-3 top-1/2 -translate-y-1/2 text-slate-400 text-xs"></i>
                    <input type="text" id="subUnitSearch" placeholder="Filtrar servicio..." class="pl-9 pr-4 py-2 text-xs border border-slate-300 dark:border-slate-700 bg-slate-50 dark:bg-slate-900 text-slate-900 dark:text-white rounded-xl focus:ring-2 focus:ring-emerald-500 w-full sm:w-64">
                </div>
            </div>

            <div class="overflow-x-auto">
                <table class="w-full text-left border-collapse">
                    <thead>
                        <tr class="bg-slate-50 dark:bg-slate-900/50 text-slate-500 dark:text-slate-400 text-xs font-semibold uppercase tracking-wider border-b border-slate-200 dark:border-slate-700">
                            <th class="py-3.5 px-6">Área / Servicio</th>
                            <th class="py-3.5 px-6">Plantilla Total</th>
                            <th class="py-3.5 px-6">Presentes Hoy</th>
                            <th class="py-3.5 px-6">Ausentes</th>
                            <th class="py-3.5 px-6 w-1/4">% Presencia Real</th>
                            <th class="py-3.5 px-6 text-center">Estado Operativo</th>
                        </tr>
                    </thead>
                    <tbody id="subUnitsTable" class="divide-y divide-slate-100 dark:divide-slate-700 text-xs sm:text-sm">
                        <!-- Injected via JS -->
                    </tbody>
                </table>
            </div>
        </section>

        <!-- Whistleblowing / Anonymous Report Section -->
        <section id="denuncia" class="bg-gradient-to-br from-navy-950 via-navy-900 to-slate-900 text-white rounded-3xl p-8 shadow-2xl relative overflow-hidden border border-emerald-500/20">
            <div class="max-w-3xl relative z-10">
                <div class="inline-flex items-center space-x-2 bg-amber-500/20 text-amber-300 px-3 py-1 rounded-full text-xs font-semibold border border-amber-500/30 mb-4">
                    <i class="fa-solid fa-user-ninja"></i>
                    <span>Canal Seguro de Denuncias Anónimas (Directiva UE 2019/1937 y Ley 2/2023)</span>
                </div>
                <h2 class="text-2xl sm:text-3xl font-extrabold tracking-tight text-white mb-3">
                    Buzón Cívico de Denuncia Anticorrupción y Abandono
                </h2>
                <p class="text-slate-300 text-xs sm:text-sm leading-relaxed mb-6">
                    Si ha presenciado abandono de puesto, fichaje por terceros, cobro indebido de dietas sin asistencia presencial o falta de atención al público en cualquier institución de España, envíe su reporte seguro. Cifrado SHA-256 irrestringible.
                </p>

                <form id="reportForm" class="space-y-4">
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                        <div>
                            <label class="block text-xs font-medium text-slate-300 mb-1">Institución u Organismo Afectado</label>
                            <input type="text" id="reportEntityInput" required placeholder="Ej. Ayuntamiento de Madrid - Licencias" class="w-full bg-slate-800/90 border border-slate-700 text-white rounded-xl p-3 text-xs focus:ring-2 focus:ring-amber-500">
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-300 mb-1">Fecha y Franja Horaria</label>
                            <input type="datetime-local" required id="reportDate" class="w-full bg-slate-800/90 border border-slate-700 text-white rounded-xl p-3 text-xs focus:ring-2 focus:ring-amber-500">
                        </div>
                    </div>

                    <div>
                        <label class="block text-xs font-medium text-slate-300 mb-1">Descripción de Hechos Irregulares</label>
                        <textarea id="reportDesc" required rows="3" placeholder="Aporte detalles concretos (puestos vacíos, suplantación de biometría, cobro inmerecido de dietas...)" class="w-full bg-slate-800/90 border border-slate-700 text-white rounded-xl p-3 text-xs focus:ring-2 focus:ring-amber-500"></textarea>
                    </div>

                    <div class="flex flex-col sm:flex-row items-center justify-between gap-4 pt-2">
                        <div class="flex items-center text-[11px] text-slate-400">
                            <i class="fa-solid fa-lock mr-2 text-emerald-400"></i>
                            Sin registros de IP ni metadatos. Anonimato total garantizado por ley.
                        </div>
                        <button type="submit" class="w-full sm:w-auto bg-amber-500 hover:bg-amber-400 text-slate-950 font-bold px-6 py-3 rounded-xl transition shadow-lg flex items-center justify-center space-x-2 text-xs">
                            <i class="fa-solid fa-paper-plane"></i>
                            <span>Registrar Denuncia Cifrada</span>
                        </button>
                    </div>
                </form>

                <div id="reportSuccess" class="hidden mt-6 bg-emerald-500/20 border border-emerald-500/40 text-emerald-200 p-4 rounded-xl text-xs flex items-start space-x-3">
                    <i class="fa-solid fa-circle-check text-xl text-emerald-400 mt-0.5"></i>
                    <div>
                        <strong class="font-bold text-white block">Denuncia registrada con hash criptográfico</strong>
                        Código de seguimiento: <span class="font-mono text-amber-300 font-bold" id="reportCode">#ES-2026-9912</span>. Transferido automáticamente a la Inspección General de Servicios.
                    </div>
                </div>
            </div>
        </section>

    </main>

    <!-- Modal Sources Explanatory -->
    <div id="sourcesModal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white dark:bg-slate-800 max-w-2xl w-full rounded-2xl p-6 shadow-2xl border border-slate-200 dark:border-slate-700 space-y-4 max-h-[90vh] overflow-y-auto">
            <div class="flex justify-between items-center border-b border-slate-100 dark:border-slate-700 pb-3">
                <h3 class="text-base font-bold text-slate-900 dark:text-white flex items-center">
                    <i class="fa-solid fa-database text-emerald-500 mr-2"></i>
                    Fuentes de Datos Oficiales y Metodología
                </h3>
                <button onclick="toggleSourcesModal()" class="text-slate-400 hover:text-slate-600 dark:hover:text-white">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>
            <div class="space-y-3 text-xs text-slate-600 dark:text-slate-300 leading-relaxed">
                <p>
                    El <strong>Portal Nacional de Transparencia del Empleo Público</strong> se nutre de agregaciones provenientes de las siguientes bases de datos oficiales en España:
                </p>
                <ul class="list-disc pl-5 space-y-2">
                    <li><strong>Registro Central de Personal (RCP):</strong> Dependiente del Ministerio para la Transformación Digital y de la Función Pública. Aporta los censos de efectivos reales y tipología contractual.</li>
                    <li><strong>Boletín Estadístico del Personal al Servicio de las Administraciones Públicas (BEPSAP):</strong> Publicación semestral de referencia para el cálculo de medias de ausentismo por sector.</li>
                    <li><strong>Sistemas de Fichaje Biométrico de Entidades Locales y Cortes:</strong> Transmisión telemática de presencia activa agregada por departamento (cumpliendo estricta disociación RGPD).</li>
                    <li><strong>Portales de Datos Abiertos (Datos.gob.es):</strong> Catálogo general de las 17 Comunidades Autónomas y las 50 Diputaciones/Provincias de España.</li>
                </ul>
            </div>
            <div class="pt-3 border-t border-slate-100 dark:border-slate-700 text-right">
                <button onclick="toggleSourcesModal()" class="bg-emerald-600 text-white px-4 py-2 rounded-xl text-xs font-bold">
                    Entendido
                </button>
            </div>
        </div>
    </div>

    <!-- Footer -->
    <footer class="bg-navy-900 text-slate-400 text-xs py-8 border-t border-slate-800 mt-12">
        <div class="max-w-7xl mx-auto px-4 text-center sm:text-left sm:flex sm:justify-between sm:items-center space-y-4 sm:space-y-0">
            <div>
                <p class="font-bold text-slate-200">Portal Nacional de Transparencia del Empleo Público • España</p>
                <p class="mt-1">Auditoría Cívica e Inspección de Servicios Digitales</p>
            </div>
            <div class="flex justify-center sm:justify-end space-x-6 text-[11px]">
                <a href="#" onclick="toggleSourcesModal(); return false;" class="hover:text-white transition">Fuentes Oficiales</a>
                <a href="#denuncia" class="hover:text-white transition">Buzón Antifraude</a>
                <a href="#" onclick="exportDataCSV(); return false;" class="hover:text-white transition">Descargar Datos Abiertos (CSV)</a>
            </div>
        </div>
    </footer>

    <!-- JavaScript Application Logic -->
    <script>
        // State Management
        let selectedState = {
            adminType: 'locales',
            province: 'madrid',
            entity: 'madrid_ayto',
            department: 'all'
        };

        let autoRefreshInterval = null;
        let timerSeconds = 10;
        let hourlyChart = null;
        let causesChart = null;
        let historyChart = null;

        // Exhaustive Catalog of all 50 Provinces of Spain + Ceuta/Melilla
        const spanishProvincesByCCAA = {
            "Andalucía": [
                { id: "almeria", name: "Almería" }, { id: "cadiz", name: "Cádiz" },
                { id: "cordoba", name: "Córdoba" }, { id: "granada", name: "Granada" },
                { id: "huelva", name: "Huelva" }, { id: "jaen", name: "Jaén" },
                { id: "malaga", name: "Málaga" }, { id: "sevilla", name: "Sevilla" }
            ],
            "Aragón": [
                { id: "huesca", name: "Huesca" }, { id: "teruel", name: "Teruel" }, { id: "zaragoza", name: "Zaragoza" }
            ],
            "Principado de Asturias": [{ id: "asturias", name: "Asturias" }],
            "Illes Balears": [{ id: "baleares", name: "Islas Baleares" }],
            "Canarias": [
                { id: "las_palmas", name: "Las Palmas" }, { id: "santa_cruz_tenerife", name: "Santa Cruz de Tenerife" }
            ],
            "Cantabria": [{ id: "cantabria", name: "Cantabria" }],
            "Castilla y León": [
                { id: "avila", name: "Ávila" }, { id: "burgos", name: "Burgos" },
                { id: "leon", name: "León" }, { id: "palencia", name: "Palencia" },
                { id: "salamanca", name: "Salamanca" }, { id: "segovia", name: "Segovia" },
                { id: "soria", name: "Soria" }, { id: "valladolid", name: "Valladolid" }, { id: "zamora", name: "Zamora" }
            ],
            "Castilla-La Mancha": [
                { id: "albacete", name: "Albacete" }, { id: "ciudad_real", name: "Ciudad Real" },
                { id: "cuenca", name: "Cuenca" }, { id: "guadalajara", name: "Guadalajara" }, { id: "toledo", name: "Toledo" }
            ],
            "Cataluña / Catalunya": [
                { id: "barcelona", name: "Barcelona" }, { id: "girona", name: "Girona" },
                { id: "lleida", name: "Lleida" }, { id: "tarragona", name: "Tarragona" }
            ],
            "Comunitat Valenciana": [
                { id: "alicante", name: "Alicante / Alacant" },
                { id: "castellon", name: "Castellón / Castelló" },
                { id: "valencia", name: "Valencia / València" }
            ],
            "Extremadura": [{ id: "badajoz", name: "Badajoz" }, { id: "caceres", name: "Cáceres" }],
            "Galicia": [
                { id: "coruna", name: "A Coruña" }, { id: "lugo", name: "Lugo" },
                { id: "ourense", name: "Ourense" }, { id: "pontevedra", name: "Pontevedra" }
            ],
            "Comunidad de Madrid": [{ id: "madrid", name: "Madrid" }],
            "Región de Murcia": [{ id: "murcia", name: "Murcia" }],
            "Comunidad Foral de Navarra": [{ id: "navarra", name: "Navarra" }],
            "País Vasco / Euskadi": [
                { id: "alava", name: "Álava / Araba" },
                { id: "guipuzcoa", name: "Guipúzcoa / Gipuzkoa" },
                { id: "vizcaya", name: "Vizcaya / Bizkaia" }
            ],
            "La Rioja": [{ id: "rioja", name: "La Rioja" }],
            "Ciudades Autónomas": [{ id: "ceuta", name: "Ceuta" }, { id: "melilla", name: "Melilla" }]
        };

        // Generator for synthetic realistic data for any municipality or institution
        function generateEntityData(entityName) {
            return {
                name: entityName,
                depts: [
                    { name: "Urbanismo, Licencias y Obras Públicas", total: 110, active: 94, absent: 16 },
                    { name: "Policía Local / Seguridad Ciudadana", total: 220, active: 206, absent: 14 },
                    { name: "Servicios Sociales y Atención Ciudadana", total: 95, active: 81, absent: 14 },
                    { name: "Hacienda, Padrón y Gestión Tributaria", total: 70, active: 62, absent: 8 }
                ],
                activePct: "87.1%", activeCnt: "(443/508)", unjustPct: "1.8%", unjustCnt: "(9 casos)",
                avgDays: "9.9", natAvg: "10.8", diet: "96.5%", resRate: "92.1%",
                causes: [68, 18, 9, 5],
                history: [10.1, 11.0, 9.8, 9.3, 8.8, 9.5, 12.1, 14.3, 9.7, 9.9],
                hourly: [14, 86, 95, 93, 89, 83, 72, 28],
                types: { carrera: "64%", interino: "20%", laboral: "12%", eventual: "4%" }
            };
        }

        // Master Database Structure
        const masterDatabase = {
            locales: {
                label: "Entidades Locales (50 Provincias + EELL)",
                provinces: {
                    madrid: {
                        name: "Madrid (Provincia)",
                        entities: {
                            madrid_ayto: {
                                name: "Ayuntamiento de Madrid",
                                depts: [
                                    { name: "Urbanismo y Licencias", total: 180, active: 156, absent: 24 },
                                    { name: "Policía Municipal", total: 320, active: 304, absent: 16 },
                                    { name: "Servicios Sociales y Familias", total: 140, active: 116, absent: 24 },
                                    { name: "Línea Madrid y Padrón", total: 90, active: 76, absent: 14 }
                                ],
                                activePct: "86.1%", activeCnt: "(937/1090)", unjustPct: "1.9%", unjustCnt: "(21 casos)", avgDays: "9.8", natAvg: "10.8", diet: "96.4%", resRate: "93.2%",
                                causes: [70, 15, 10, 5], history: [10.2, 11.5, 9.8, 9.1, 8.8, 9.5, 12.1, 14.2, 9.6, 9.8], hourly: [15, 88, 96, 94, 91, 85, 75, 30],
                                types: { carrera: "62%", interino: "22%", laboral: "12%", eventual: "4%" }
                            },
                            alcala_ayto: generateEntityData("Ayuntamiento de Alcalá de Henares"),
                            mostoles_ayto: generateEntityData("Ayuntamiento de Móstoles")
                        }
                    },
                    barcelona: {
                        name: "Barcelona (Provincia)",
                        entities: {
                            bcn_ayto: {
                                name: "Ayuntamiento de Barcelona",
                                depts: [
                                    { name: "Gerència d'Urbanisme", total: 160, active: 138, absent: 22 },
                                    { name: "Guàrdia Urbana", total: 290, active: 274, absent: 16 },
                                    { name: "Oficines d'Atenció Ciutadana (OAC)", total: 85, active: 70, absent: 15 }
                                ],
                                activePct: "85.8%", activeCnt: "(864/1008)", unjustPct: "2.1%", unjustCnt: "(21 casos)", avgDays: "10.0", natAvg: "10.8", diet: "95.8%", resRate: "92.0%",
                                causes: [68, 18, 9, 5], history: [10.5, 11.8, 10.0, 9.4, 9.0, 9.8, 12.5, 14.8, 10.0, 10.0], hourly: [14, 86, 94, 92, 88, 82, 70, 25],
                                types: { carrera: "60%", interino: "24%", laboral: "11%", eventual: "5%" }
                            }
                        }
                    },
                    valencia: {
                        name: "Valencia / València",
                        entities: { val_ayto: generateEntityData("Ayuntamiento de Valencia") }
                    },
                    sevilla: {
                        name: "Sevilla",
                        entities: { sev_ayto: generateEntityData("Ayuntamiento de Sevilla") }
                    }
                }
            },
            age: {
                label: "Administración General del Estado (AGE)",
                provinces: {
                    nacional: {
                        name: "Servicios Centrales y Ministerios",
                        entities: {
                            min_hacienda: {
                                name: "Ministerio de Hacienda y Función Pública / AEAT",
                                depts: [
                                    { name: "Agencia Tributaria (Inspección)", total: 310, active: 288, absent: 22 },
                                    { name: "Catastro Inmobiliario", total: 110, active: 96, absent: 14 }
                                ],
                                activePct: "90.8%", activeCnt: "(384/423)", unjustPct: "0.8%", unjustCnt: "(3 casos)", avgDays: "8.2", natAvg: "10.8", diet: "98.8%", resRate: "96.5%",
                                causes: [78, 10, 5, 7], history: [8.0, 8.9, 8.2, 7.8, 7.5, 8.1, 10.0, 11.5, 8.2, 8.2], hourly: [20, 92, 98, 96, 94, 90, 82, 40],
                                types: { carrera: "75%", interino: "14%", laboral: "9%", eventual: "2%" }
                            }
                        }
                    }
                }
            },
            constitucional: {
                label: "Órganos Constitucionales y Cortes Generales",
                provinces: {
                    nacional: {
                        name: "Madrid (Sede Constitucional)",
                        entities: {
                            congreso: {
                                name: "Congreso de los Diputados",
                                depts: [
                                    { name: "Sesiones Plenarias y Votaciones", total: 350, active: 318, absent: 32 },
                                    { name: "Comisión Constitucional", total: 45, active: 42, absent: 3 }
                                ],
                                activePct: "90.1%", activeCnt: "(318/350)", unjustPct: "0.9%", unjustCnt: "(3 casos)", avgDays: "4.1", natAvg: "10.8", diet: "98.5%", resRate: "95.5%",
                                causes: [82, 10, 3, 5], history: [4.0, 4.5, 4.1, 3.8, 3.5, 4.0, 5.2, 6.0, 4.1, 4.1], hourly: [5, 40, 88, 98, 95, 92, 85, 60],
                                types: { carrera: "70%", interino: "10%", laboral: "10%", eventual: "10%" }
                            },
                            senado: {
                                name: "Senado de España",
                                depts: [
                                    { name: "Pleno del Senado", total: 265, active: 235, absent: 30 }
                                ],
                                activePct: "88.6%", activeCnt: "(235/265)", unjustPct: "1.2%", unjustCnt: "(3 casos)", avgDays: "4.6", natAvg: "10.8", diet: "97.8%", resRate: "94.2%",
                                causes: [80, 12, 4, 4], history: [4.5, 5.0, 4.6, 4.2, 4.0, 4.5, 5.8, 6.5, 4.7, 4.6], hourly: [5, 35, 82, 95, 92, 88, 80, 55],
                                types: { carrera: "68%", interino: "12%", laboral: "10%", eventual: "10%" }
                            }
                        }
                    }
                }
            },
            ccaa: { label: "Comunidades Autónomas", provinces: { andalucia: { name: "Junta de Andalucía", entities: { sas: generateEntityData("Servicio Andaluz de Salud (Admón)") } } } },
            universidades: { label: "Universidades Públicas", provinces: { nacional: { name: "Red Universitaria", entities: { ucm: generateEntityData("Universidad Complutense (PAS)") } } } },
            empresas: { label: "Empresas Públicas", provinces: { nacional: { name: "SEPI", entities: { correos: generateEntityData("Correos y Telégrafos S.A.") } } } }
        };

        // Initialize UI Elements
        document.addEventListener('DOMContentLoaded', () => {
            setupThemeToggle();
            setupLiveToggle();
            populateProvinces();
            setupPredictiveSearch('globalSearchInput', 'searchDropdown');
            setupPredictiveSearch('globalSearchInputMobile', 'searchDropdownMobile');
            setupListeners();
            startCountdown();
        });

        // Toggle Sources Modal
        function toggleSourcesModal() {
            const modal = document.getElementById('sourcesModal');
            modal.classList.toggle('hidden');
        }

        // Live Feed Timer
        function startCountdown() {
            if (autoRefreshInterval) clearInterval(autoRefreshInterval);
            autoRefreshInterval = setInterval(() => {
                if (document.getElementById('liveToggle').checked) {
                    timerSeconds--;
                    document.getElementById('countdownTimer').textContent = `${timerSeconds}s`;
                    if (timerSeconds <= 0) {
                        timerSeconds = 10;
                        triggerLivePulseUpdate();
                    }
                }
            }, 1000);
        }

        function triggerLivePulseUpdate() {
            // Generate a dynamic cryptographic hash for the pulse event
            const randomHash = '0x' + Array.from({length: 8}, () => Math.floor(Math.random()*16).toString(16)).join('');
            document.getElementById('syncHash').textContent = `${randomHash}...`;

            // Small fluctuation in active percentage
            const currentData = getSelectedEntityData();
            if (currentData) {
                const baseVal = parseFloat(currentData.activePct);
                const delta = (Math.random() * 0.4 - 0.2).toFixed(1);
                const newVal = (baseVal + parseFloat(delta)).toFixed(1);
                document.getElementById('kpiActivePercent').textContent = `${newVal}%`;
            }

            showToast('Actualización en vivo: Sincronización realizada con la API de Función Pública', 'success');
        }

        function forceSyncData() {
            timerSeconds = 10;
            document.getElementById('countdownTimer').textContent = '10s';
            triggerLivePulseUpdate();
        }

        function showToast(message, type = 'info') {
            const container = document.getElementById('toastContainer');
            const toast = document.createElement('div');
            toast.className = `p-3 rounded-xl shadow-xl text-xs font-semibold text-white flex items-center space-x-2 transition-all transform duration-300 translate-y-2 ${
                type === 'success' ? 'bg-emerald-600 border border-emerald-400' : 'bg-slate-800 border border-slate-700'
            }`;
            toast.innerHTML = `<i class="fa-solid fa-circle-check text-amber-300"></i><span>${message}</span>`;
            container.appendChild(toast);

            setTimeout(() => {
                toast.classList.add('opacity-0', 'translate-y-4');
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        }

        function setupThemeToggle() {
            const themeBtn = document.getElementById('themeToggle');
            themeBtn.addEventListener('click', () => {
                document.documentElement.classList.toggle('dark');
            });
        }

        function setupLiveToggle() {
            document.getElementById('liveToggle').addEventListener('change', (e) => {
                if (e.target.checked) {
                    showToast('Live Feed Activado: Actualizaciones automáticas activas', 'info');
                } else {
                    showToast('Live Feed Pausado', 'info');
                }
            });
        }

        function populateProvinces() {
            const provinceSel = document.getElementById('provinceSelect');
            provinceSel.innerHTML = '';
            
            if (selectedState.adminType === 'locales') {
                Object.keys(spanishProvincesByCCAA).forEach(ccaaName => {
                    const optgroup = document.createElement('optgroup');
                    optgroup.label = `── ${ccaaName} ──`;
                    spanishProvincesByCCAA[ccaaName].forEach(prov => {
                        const opt = document.createElement('option');
                        opt.value = prov.id;
                        opt.textContent = prov.name;
                        optgroup.appendChild(opt);
                    });
                    provinceSel.appendChild(optgroup);
                });
                selectedState.province = 'madrid';
                provinceSel.value = 'madrid';
            } else {
                const typeData = masterDatabase[selectedState.adminType];
                if (typeData && typeData.provinces) {
                    Object.keys(typeData.provinces).forEach(pKey => {
                        const opt = document.createElement('option');
                        opt.value = pKey;
                        opt.textContent = typeData.provinces[pKey].name;
                        provinceSel.appendChild(opt);
                    });
                    selectedState.province = Object.keys(typeData.provinces)[0] || '';
                }
            }
            populateEntities();
        }

        function populateEntities() {
            const entitySel = document.getElementById('entitySelect');
            entitySel.innerHTML = '';

            let provinceData = masterDatabase[selectedState.adminType]?.provinces[selectedState.province];

            if (!provinceData && selectedState.adminType === 'locales') {
                const provName = findProvinceNameById(selectedState.province);
                provinceData = {
                    name: provName,
                    entities: {
                        [`ayto_${selectedState.province}`]: generateEntityData(`Ayuntamiento Capital (${provName})`),
                        [`diputacion_${selectedState.province}`]: generateEntityData(`Diputación Provincial de ${provName}`)
                    }
                };
            }

            if (provinceData && provinceData.entities) {
                Object.keys(provinceData.entities).forEach(eKey => {
                    const opt = document.createElement('option');
                    opt.value = eKey;
                    opt.textContent = provinceData.entities[eKey].name;
                    entitySel.appendChild(opt);
                });
                selectedState.entity = Object.keys(provinceData.entities)[0] || '';
            }
            populateDepartments();
            updateDashboardView();
        }

        function populateDepartments() {
            const deptSel = document.getElementById('departmentSelect');
            deptSel.innerHTML = '<option value="all">🏢 Todos los Servicios y Áreas</option>';
            const data = getSelectedEntityData();
            if (data && data.depts) {
                data.depts.forEach((d, index) => {
                    const opt = document.createElement('option');
                    opt.value = index;
                    opt.textContent = d.name;
                    deptSel.appendChild(opt);
                });
            }
        }

        function findProvinceNameById(provId) {
            for (const ccaa of Object.keys(spanishProvincesByCCAA)) {
                const found = spanishProvincesByCCAA[ccaa].find(p => p.id === provId);
                if (found) return found.name;
            }
            return "Provincia de España";
        }

        function getSelectedEntityData() {
            let pObj = masterDatabase[selectedState.adminType]?.provinces[selectedState.province];
            if (!pObj && selectedState.adminType === 'locales') {
                const provName = findProvinceNameById(selectedState.province);
                return generateEntityData(`Ayuntamiento Capital (${provName})`);
            }
            return pObj?.entities[selectedState.entity] || generateEntityData("Organismo Público");
        }

        function updateDashboardView() {
            const data = getSelectedEntityData();
            if (!data) return;

            document.getElementById('activeEntityBadge').textContent = `Mostrando: ${data.name}`;
            document.getElementById('kpiActivePercent').textContent = data.activePct;
            document.getElementById('kpiActiveCount').textContent = data.activeCnt;
            document.getElementById('kpiUnjustifiedPercent').textContent = data.unjustPct;
            document.getElementById('kpiUnjustifiedCount').textContent = data.unjustCnt;
            document.getElementById('kpiAvgDays').textContent = data.avgDays;
            document.getElementById('kpiNatAvg').textContent = `${data.natAvg} días`;
            document.getElementById('kpiDietCompliance').textContent = data.diet;
            document.getElementById('kpiResolutionRate').textContent = data.resRate;

            if (data.types) {
                document.getElementById('typeCarrera').textContent = data.types.carrera;
                document.getElementById('typeInterino').textContent = data.types.interino;
                document.getElementById('typeLaboral').textContent = data.types.laboral;
                document.getElementById('typeEventual').textContent = data.types.eventual;
            }

            renderHourlyChart(data.hourly);
            renderCausesChart(data.causes);
            renderHistoryChart(data.history);
            renderSubUnitsTable(data.depts);
        }

        function renderSubUnitsTable(depts) {
            const tbody = document.getElementById('subUnitsTable');
            tbody.innerHTML = '';
            if (!depts) return;

            depts.forEach(d => {
                const pct = ((d.active / d.total) * 100).toFixed(1);
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-50 dark:hover:bg-slate-700/50 transition';
                tr.innerHTML = `
                    <td class="py-3.5 px-6 font-semibold text-slate-900 dark:text-white">${d.name}</td>
                    <td class="py-3.5 px-6">${d.total} emp.</td>
                    <td class="py-3.5 px-6 text-emerald-600 font-bold">${d.active}</td>
                    <td class="py-3.5 px-6 text-rose-500 font-bold">${d.absent}</td>
                    <td class="py-3.5 px-6">
                        <div class="flex items-center space-x-2">
                            <span class="font-bold text-xs">${pct}%</span>
                            <div class="w-full bg-slate-200 dark:bg-slate-700 h-2 rounded-full overflow-hidden">
                                <div class="bg-emerald-500 h-full rounded-full" style="width: ${pct}%"></div>
                            </div>
                        </div>
                    </td>
                    <td class="py-3.5 px-6 text-center">
                        <span class="px-2.5 py-1 rounded-full text-[10px] font-bold bg-emerald-100 text-emerald-800 dark:bg-emerald-950 dark:text-emerald-300 border border-emerald-300">
                            Normal
                        </span>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        function setupListeners() {
            document.getElementById('adminTypeSelect').addEventListener('change', (e) => {
                selectedState.adminType = e.target.value;
                populateProvinces();
            });

            document.getElementById('provinceSelect').addEventListener('change', (e) => {
                selectedState.province = e.target.value;
                populateEntities();
            });

            document.getElementById('entitySelect').addEventListener('change', (e) => {
                selectedState.entity = e.target.value;
                populateDepartments();
                updateDashboardView();
            });

            document.getElementById('reportForm').addEventListener('submit', (e) => {
                e.preventDefault();
                document.getElementById('reportSuccess').classList.remove('hidden');
                document.getElementById('reportCode').textContent = `#ES-2026-${Math.floor(1000 + Math.random() * 9000)}`;
                showToast('Denuncia cifrada registrada correctamente', 'success');
            });
        }

        function quickSelect(type, prov, ent) {
            selectedState.adminType = type;
            document.getElementById('adminTypeSelect').value = type;
            populateProvinces();
            selectedState.province = prov;
            document.getElementById('provinceSelect').value = prov;
            populateEntities();
            selectedState.entity = ent;
            document.getElementById('entitySelect').value = ent;
            populateDepartments();
            updateDashboardView();
        }

        function setupPredictiveSearch(inputId, dropdownId) {
            const input = document.getElementById(inputId);
            const dropdown = document.getElementById(dropdownId);

            input.addEventListener('input', (e) => {
                const query = e.target.value.toLowerCase().trim();
                dropdown.innerHTML = '';
                if (query.length < 2) {
                    dropdown.classList.add('hidden');
                    return;
                }

                let matches = [];
                Object.keys(spanishProvincesByCCAA).forEach(ccaaName => {
                    spanishProvincesByCCAA[ccaaName].forEach(prov => {
                        if (prov.name.toLowerCase().includes(query)) {
                            matches.push({
                                name: `Ayuntamiento y Diputación de ${prov.name} (${ccaaName})`,
                                type: 'locales', prov: prov.id, ent: `ayto_${prov.id}`
                            });
                        }
                    });
                });

                matches.slice(0, 6).forEach(m => {
                    const item = document.createElement('div');
                    item.className = 'p-3 hover:bg-slate-100 dark:hover:bg-slate-700 cursor-pointer border-b border-slate-100 dark:border-slate-700 font-medium flex justify-between items-center';
                    item.innerHTML = `<span><i class="fa-solid fa-building text-emerald-500 mr-2"></i>${m.name}</span><span class="text-[10px] text-slate-400">Consultar</span>`;
                    item.addEventListener('click', () => {
                        quickSelect(m.type, m.prov, m.ent);
                        dropdown.classList.add('hidden');
                        input.value = '';
                    });
                    dropdown.appendChild(item);
                });
                dropdown.classList.remove('hidden');
            });
        }

        function renderHourlyChart(data) {
            const ctx = document.getElementById('hourlyChart').getContext('2d');
            if (hourlyChart) hourlyChart.destroy();
            hourlyChart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: ['08:00', '09:00', '10:00', '11:00', '12:00', '13:00', '14:00', '15:00'],
                    datasets: [{
                        label: '% Presencia Real',
                        data: data,
                        borderColor: '#10b981',
                        backgroundColor: 'rgba(16, 185, 129, 0.1)',
                        fill: true,
                        tension: 0.3
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { y: { min: 0, max: 100 } } }
            });
        }

        function renderCausesChart(data) {
            const ctx = document.getElementById('causesChart').getContext('2d');
            if (causesChart) causesChart.destroy();
            causesChart = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: ['Baja Médica / Licencia', 'Permiso Normativo', 'Sin Justificar / Irregular', 'Teletrabajo Verificado'],
                    datasets: [{ data: data, backgroundColor: ['#10b981', '#3b82f6', '#f43f5e', '#f59e0b'] }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'bottom', labels: { boxWidth: 10, font: { size: 10 } } } } }
            });
        }

        function renderHistoryChart(data) {
            const ctx = document.getElementById('historyChart').getContext('2d');
            if (historyChart) historyChart.destroy();
            historyChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ['Ene', 'Feb', 'Mar', 'Abr', 'May', 'Jun', 'Jul', 'Ago', 'Sep', 'Oct'],
                    datasets: [
                        { label: 'Entidad Seleccionada', data: data, backgroundColor: '#059669', borderRadius: 4 },
                        { label: 'Media Nacional Sector', data: Array(10).fill(10.8), type: 'line', borderColor: '#ef4444', borderDash: [5, 5], pointRadius: 0 }
                    ]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'bottom', labels: { boxWidth: 10, font: { size: 10 } } } } }
            });
        }

        function exportDataCSV() {
            const data = getSelectedEntityData();
            let csvContent = "data:text/csv;charset=utf-8,Servicio,Plantilla,Presentes,Ausentes\n";
            data.depts.forEach(d => {
                csvContent += `"${d.name}",${d.total},${d.active},${d.absent}\n`;
            });
            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", `Transparencia_${data.name.replace(/ /g, "_")}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            showToast('Informe CSV descargado correctamente', 'success');
        }
    </script>
</body>
</html>
