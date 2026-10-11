
<html lang="pt-BR" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SROS - Sistema de Resposta Orbital e Solar | Defesa Cósmica</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        space: {
                            900: '#030712',
                            800: '#0b0f19',
                            700: '#111827',
                            600: '#1f2937',
                        },
                        neon: {
                            cyan: '#00f3ff',
                            emerald: '#10b981',
                            magenta: '#ec4899',
                            amber: '#f59e0b',
                            purple: '#8b5cf6'
                        }
                    },
                    fontFamily: {
                        mono: ['JetBrains Mono', 'Courier New', monospace],
                    }
                }
            }
        }
    </script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&family=Orbitron:wght@500;700;900&display=swap" rel="stylesheet">
    <!-- FontAwesome for Sci-Fi Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body {
            font-family: 'JetBrains Mono', monospace;
            background-color: #030712;
            color: #f3f4f6;
            overflow-x: hidden;
        }
        h1, h2, h3, .font-orbitron {
            font-family: 'Orbitron', sans-serif;
        }
        .glow-cyan {
            box-shadow: 0 0 20px rgba(0, 243, 255, 0.3);
        }
        .glow-magenta {
            box-shadow: 0 0 20px rgba(236, 72, 153, 0.3);
        }
        .glow-emerald {
            box-shadow: 0 0 20px rgba(16, 185, 129, 0.3);
        }
        .text-glow-cyan {
            text-shadow: 0 0 10px rgba(0, 243, 255, 0.7);
        }
        .text-glow-magenta {
            text-shadow: 0 0 10px rgba(236, 72, 153, 0.7);
        }
        /* Custom scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #0b0f19;
        }
        ::-webkit-scrollbar-thumb {
            background: #1f2937;
            border-radius: 3px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #00f3ff;
        }
        .scanline {
            background: linear-gradient(
                to bottom,
                rgba(255,255,255,0),
                rgba(255,255,255,0) 50%,
                rgba(0, 243, 255, 0.05) 50%,
                rgba(0, 243, 255, 0.05)
            );
            background-size: 100% 4px;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col bg-space-900 scanline">

    <header class="border-b border-cyan-500/30 bg-space-800/80 backdrop-blur-md sticky top-0 z-50 px-4 py-3">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between gap-4">
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 rounded-lg bg-cyan-950 border border-cyan-400 flex items-center justify-center glow-cyan">
                    <i class="fa-solid fa-satellite text-cyan-400 text-xl animate-pulse"></i>
                </div>
                <div>
                    <h1 class="text-xl font-black tracking-wider text-cyan-400 text-glow-cyan">S.R.O.S.</h1>
                    <p class="text-xs text-cyan-200/60 uppercase tracking-widest">Orbital & Solar Response System</p>
                </div>
            </div>

            <!-- Navigation Tabs -->
            <nav class="flex flex-wrap items-center gap-2 bg-space-900/80 p-1.5 rounded-xl border border-space-600">
                <button onclick="switchTab('macro')" id="tab-macro" class="px-4 py-2 rounded-lg text-xs font-bold transition-all flex items-center gap-2 bg-cyan-500/20 text-cyan-300 border border-cyan-500/50 glow-cyan">
                    <i class="fa-solid fa-globe"></i> Visão Macro (Sistema Solar)
                </button>
                <button onclick="switchTab('micro')" id="tab-micro" class="px-4 py-2 rounded-lg text-xs font-bold transition-all flex items-center gap-2 text-gray-400 hover:text-cyan-300 hover:bg-space-700/50 border border-transparent">
                    <i class="fa-solid fa-crosshairs"></i> Visão Micro (Ablação & Lasers)
                </button>
                <button onclick="switchTab('control')" id="tab-control" class="px-4 py-2 rounded-lg text-xs font-bold transition-all flex items-center gap-2 text-gray-400 hover:text-cyan-300 hover:bg-space-700/50 border border-transparent">
                    <i class="fa-solid fa-sliders"></i> Painel SROS & Telemetria
                </button>
            </nav>

            <!-- Quick Status Indicators -->
            <div class="flex items-center space-x-4 text-xs">
                <div class="flex items-center gap-2 bg-space-900 px-3 py-1.5 rounded-lg border border-emerald-500/40">
                    <span class="w-2.5 h-2.5 rounded-full bg-emerald-500 animate-ping"></span>
                    <span class="text-emerald-400 font-bold">AUTONAV: ATIVO</span>
                </div>
                <div id="status-threat" class="flex items-center gap-2 bg-space-900 px-3 py-1.5 rounded-lg border border-amber-500/40">
                    <i class="fa-solid fa-triangle-exclamation text-amber-400"></i>
                    <span class="text-amber-400 font-bold">AMEAÇA DETECTADA</span>
                </div>
            </div>
        </div>
    </header>

    <main class="flex-1 max-w-7xl w-full mx-auto p-4 grid grid-cols-1 lg:grid-cols-4 gap-4">
        
        <!-- Canvas & Visualization Area (3 Cols) -->
        <div class="lg:col-span-3 flex flex-col gap-4">
            
            <!-- Main Canvas Container -->
            <div class="relative w-full aspect-[16/10] bg-space-800 rounded-2xl border border-cyan-500/30 overflow-hidden glow-cyan flex items-center justify-center">
                
                <!-- Canvas Element -->
                <canvas id="simCanvas" class="w-full h-full block"></canvas>

                <!-- HUD Overlays inside Canvas -->
                <div class="absolute top-4 left-4 bg-space-900/80 backdrop-blur-md p-3 rounded-xl border border-cyan-500/30 text-xs space-y-1 pointer-events-none">
                    <div class="text-cyan-400 font-bold uppercase tracking-wider flex items-center gap-2">
                        <i class="fa-solid fa-radar animate-spin"></i> <span id="hud-mode-title">VISÃO MACRO: SISTEMA SOLAR</span>
                    </div>
                    <div class="text-gray-400">FPS: <span id="hud-fps" class="text-white">60</span> | ESCALA: <span id="hud-scale" class="text-white">1:10,000,000</span></div>
                    <div class="text-gray-400">ALVO: <span id="hud-target" class="text-magenta-400">Asteroide Apophis-X (ID-904)</span></div>
                </div>

                <!-- Simulation Controls Floating Bar -->
                <div class="absolute bottom-4 left-1/2 -translate-x-1/2 bg-space-900/90 backdrop-blur-md px-6 py-3 rounded-2xl border border-cyan-500/40 flex items-center gap-4 shadow-2xl">
                    <button onclick="togglePlayPause()" id="btn-play" class="w-10 h-10 rounded-xl bg-cyan-500 hover:bg-cyan-400 text-space-900 font-bold flex items-center justify-center transition-all glow-cyan">
                        <i class="fa-solid fa-pause" id="icon-play"></i>
                    </button>
                    <div class="h-6 w-[1px] bg-space-600"></div>
                    <div class="flex items-center gap-2">
                        <span class="text-xs text-gray-400 uppercase">Velocidade:</span>
                        <button onclick="setSpeed(1)" id="spd-1" class="px-2.5 py-1 rounded-lg text-xs font-bold bg-cyan-500/20 text-cyan-300 border border-cyan-500">1x</button>
                        <button onclick="setSpeed(5)" id="spd-5" class="px-2.5 py-1 rounded-lg text-xs font-bold bg-space-700 text-gray-400 border border-space-600">5x</button>
                        <button onclick="setSpeed(10)" id="spd-10" class="px-2.5 py-1 rounded-lg text-xs font-bold bg-space-700 text-gray-400 border border-space-600">10x</button>
                    </div>
                    <div class="h-6 w-[1px] bg-space-600"></div>
                    <button onclick="resetSimulation()" class="px-3 py-1.5 rounded-xl bg-space-700 hover:bg-space-600 text-xs text-gray-200 border border-space-600 flex items-center gap-2 transition-all">
                        <i class="fa-solid fa-rotate-right"></i> Reiniciar
                    </button>
                </div>
            </div>

            <!-- Tab Content View Container (Macro, Micro, Control) -->
            <div id="view-descriptions" class="bg-space-800/80 backdrop-blur-md p-4 rounded-2xl border border-space-600 text-sm">
                <div id="desc-macro" class="view-desc">
                    <h3 class="font-bold text-cyan-400 mb-1 flex items-center gap-2"><i class="fa-solid fa-circle-info"></i> Modo Macro: Dinâmica Orbital & Trajetória de Risco</h3>
                    <p class="text-gray-300">Visualização em escala do Sistema Solar interno. O Asteroide Apophis-X (ID-904) está em rota de interceptação com a Terra (linha tracejada vermelha). A rede de satélites SROS posicionados em pontos de Lagrange calcula e aplica impulsos contínuos de laser para desviar a órbita com segurança (linha verde).</p>
                </div>
                <div id="desc-micro" class="view-desc hidden">
                    <h3 class="font-bold text-cyan-400 mb-1 flex items-center gap-2"><i class="fa-solid fa-circle-info"></i> Modo Micro: Ablação a Laser & Reator MHD</h3>
                    <p class="text-gray-300">Zoom no asteroide rochoso irregular. A Constelação SROS focaliza múltiplos feixes laser de ultra-alta potência alimentados pelo Reator Magneto-Hidrodinâmico (MHD). O aquecimento extremo vaporiza a superfície do asteroide, criando uma pluma de gás e plasma de alta velocidade que gera empuxo por ação e reação (Segunda Lei de Newton).</p>
                </div>
                <div id="desc-control" class="view-desc hidden">
                    <h3 class="font-bold text-cyan-400 mb-1 flex items-center gap-2"><i class="fa-solid fa-circle-info"></i> Painel de Controle SROS & Calibração de Gimbals</h3>
                    <p class="text-gray-300">Painel de gerenciamento do sistema autônomo. Ajuste a potência dos emissores laser, regule a estabilidade térmica dos gimbals magnéticos e monitore os vetores de desvio em tempo real.</p>
                </div>
            </div>

        </div>

        <!-- Sidebar / Telemetry & Controls Panel (1 Col) -->
        <div class="lg:col-span-1 flex flex-col gap-4">
            
            <!-- SROS Telemetry Panel -->
            <div class="bg-space-800/90 backdrop-blur-md p-4 rounded-2xl border border-cyan-500/30 flex flex-col gap-4">
                <div class="flex items-center justify-between border-b border-space-600 pb-3">
                    <h2 class="font-orbitron font-bold text-sm text-cyan-400 flex items-center gap-2">
                        <i class="fa-solid fa-gauge-high"></i> Telemetria SROS
                    </h2>
                    <span class="text-[10px] bg-cyan-950 text-cyan-300 px-2 py-0.5 rounded border border-cyan-500/40">ONLINE</span>
                </div>

                <!-- Metrics -->
                <div class="space-y-3 text-xs">
                    <div>
                        <div class="flex justify-between mb-1">
                            <span class="text-gray-400">Potência Reator MHD</span>
                            <span id="val-reactor" class="text-cyan-400 font-bold">6.8 GW</span>
                        </div>
                        <div class="w-full bg-space-900 rounded-full h-2 overflow-hidden border border-space-600">
                            <div id="bar-reactor" class="bg-cyan-400 h-full transition-all duration-300" style="width: 68%;"></div>
                        </div>
                    </div>

                    <div>
                        <div class="flex justify-between mb-1">
                            <span class="text-gray-400">Temp. Gimbals Magnéticos</span>
                            <span id="val-temp" class="text-amber-400 font-bold">412 K</span>
                        </div>
                        <div class="w-full bg-space-900 rounded-full h-2 overflow-hidden border border-space-600">
                            <div id="bar-temp" class="bg-amber-400 h-full transition-all duration-300" style="width: 45%;"></div>
                        </div>
                    </div>

                    <div>
                        <div class="flex justify-between mb-1">
                            <span class="text-gray-400">Precisão AutoNav IA</span>
                            <span id="val-nav" class="text-emerald-400 font-bold">99.8%</span>
                        </div>
                        <div class="w-full bg-space-900 rounded-full h-2 overflow-hidden border border-space-600">
                            <div id="bar-nav" class="bg-emerald-400 h-full transition-all duration-300" style="width: 99.8%;"></div>
                        </div>
                    </div>

                    <div>
                        <div class="flex justify-between mb-1">
                            <span class="text-gray-400">Empuxo de Ablação</span>
                            <span id="val-thrust" class="text-magenta-400 font-bold">4.2 kN</span>
                        </div>
                        <div class="w-full bg-space-900 rounded-full h-2 overflow-hidden border border-space-600">
                            <div id="bar-thrust" class="bg-magenta-500 h-full transition-all duration-300" style="width: 70%;"></div>
                        </div>
                    </div>
                </div>

                <!-- Distance & Risk Stats -->
                <div class="grid grid-cols-2 gap-2 pt-2 border-t border-space-600 text-xs">
                    <div class="bg-space-900 p-2.5 rounded-xl border border-space-600">
                        <div class="text-gray-400 text-[10px]">Distância Terra-Ast.</div>
                        <div id="stat-dist" class="text-sm font-bold text-cyan-300 mt-0.5">342.1 mil km</div>
                    </div>
                    <div class="bg-space-900 p-2.5 rounded-xl border border-space-600">
                        <div class="text-gray-400 text-[10px]">Probabilidade Impacto</div>
                        <div id="stat-risk" class="text-sm font-bold text-emerald-400 mt-0.5">0.00% (Seguro)</div>
                    </div>
                </div>

                <!-- Interactive Controls Section -->
                <div class="space-y-3 pt-2 border-t border-space-600">
                    <h3 class="font-bold text-xs text-cyan-300 uppercase">Controles do Sistema</h3>
                    
                    <div class="space-y-1">
                        <div class="flex justify-between text-xs">
                            <span class="text-gray-400">Potência dos Lasers (MW)</span>
                            <span id="label-laser-slider" class="text-cyan-400 font-bold">10.0 GW</span>
                        </div>
                        <input type="range" id="laserSlider" min="1" max="15" step="0.5" value="10" 
                            class="w-full accent-cyan-400 bg-space-900 rounded-lg appearance-none cursor-pointer h-2 border border-space-600"
                            oninput="updateLaserPower(this.value)">
                    </div>

                    <div class="flex items-center justify-between bg-space-900 p-2.5 rounded-xl border border-space-600">
                        <span class="text-xs text-gray-300">Modo AutoNav AI</span>
                        <button onclick="toggleAutoNav()" id="btn-autonav" class="px-3 py-1 rounded-lg text-xs font-bold bg-emerald-500/20 text-emerald-400 border border-emerald-500/50 glow-emerald transition-all">
                            ATIVADO
                        </button>
                    </div>

                    <button onclick="triggerTerraformPulse()" class="w-full py-2 px-3 rounded-xl bg-gradient-to-r from-cyan-500 to-blue-600 hover:from-cyan-400 hover:to-blue-500 text-space-900 font-bold text-xs transition-all glow-cyan flex items-center justify-center gap-2">
                        <i class="fa-solid fa-atom"></i> Acionar Pulso de Terraformação (Marte)
                    </button>
                </div>

            </div>

            <!-- Real-time Console Log -->
            <div class="bg-space-800/90 backdrop-blur-md p-4 rounded-2xl border border-space-600 flex flex-col flex-1 min-h-[200px]">
                <div class="flex items-center justify-between border-b border-space-600 pb-2 mb-2">
                    <h3 class="font-orbitron font-bold text-xs text-cyan-400 flex items-center gap-2">
                        <i class="fa-solid fa-terminal"></i> Log de Diagnóstico SROS
                    </h3>
                    <button onclick="clearLogs()" class="text-[10px] text-gray-400 hover:text-cyan-400 underline">Limpar</button>
                </div>
                <div id="log-container" class="flex-1 overflow-y-auto space-y-1.5 font-mono text-[11px] text-gray-300 max-h-[180px]">
                    <div class="text-cyan-400">[08:11:55] SROS Core inicializado com sucesso.</div>
                    <div class="text-emerald-400">[08:11:56] IA AutoNav travada no alvo ID-904.</div>
                    <div class="text-gray-400">[08:11:57] Reator MHD operando em estabilidade nominal (6.8 GW).</div>
                </div>
            </div>

        </div>

    </main>

    <script>
        // --- GLOBAL SIMULATION STATE ---
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        let currentTab = 'macro'; // 'macro', 'micro', 'control'
        let isRunning = true;
        let timeScale = 1;
        let laserPowerGW = 10.0;
        let autoNavActive = true;
        let simulationTime = 0;

        // Metrics State
        let mhdReactorLoad = 68;
        let gimbalTemp = 412;
        let autoNavPrecision = 99.8;
        let ablationThrust = 4.2;
        let earthDistanceKM = 342100;
        let impactRisk = 0.00;

        // Asteroid deflecting progress (0 = heading for Earth, 1 = safely deflected)
        let deflectionProgress = 0.1;

        // Micro View Particles (Ablation plume)
        let particles = [];
        let asteroidRotation = 0;
        let asteroidVertices = [];

        // Initialize Asteroid Irregular Shape
        function initAsteroidShape() {
            asteroidVertices = [];
            const numPoints = 14;
            for (let i = 0; i < numPoints; i++) {
                const angle = (i / numPoints) * Math.PI * 2;
                const radius = 45 + Math.sin(i * 3) * 12 + Math.cos(i * 5) * 8;
                asteroidVertices.push({ angle, radius });
            }
        }
        initAsteroidShape();

        // Resize Canvas properly
        function resizeCanvas() {
            const rect = canvas.getBoundingClientRect();
            canvas.width = rect.width * window.devicePixelRatio;
            canvas.height = rect.height * window.devicePixelRatio;
            ctx.scale(window.devicePixelRatio, window.devicePixelRatio);
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // --- TAB SWITCHING ---
        function switchTab(tab) {
            currentTab = tab;
            ['macro', 'micro', 'control'].forEach(t => {
                const btn = document.getElementById(`tab-${t}`);
                const desc = document.getElementById(`desc-${t}`);
                if (t === tab) {
                    btn.className = "px-4 py-2 rounded-lg text-xs font-bold transition-all flex items-center gap-2 bg-cyan-500/20 text-cyan-300 border border-cyan-500/50 glow-cyan";
                    desc.classList.remove('hidden');
                } else {
                    btn.className = "px-4 py-2 rounded-lg text-xs font-bold transition-all flex items-center gap-2 text-gray-400 hover:text-cyan-300 hover:bg-space-700/50 border border-transparent";
                    desc.classList.add('hidden');
                }
            });

            // Update HUD Title
            const titles = {
                macro: "VISÃO MACRO: SISTEMA SOLAR",
                micro: "VISÃO MICRO: ABLAÇÃO & LASERS SROS",
                control: "PAINEL DE CONTROLE SROS & TELEMETRIA"
            };
            document.getElementById('hud-mode-title').innerText = titles[tab];
            addLog(`Alternado para o modo: ${titles[tab]}`);
        }

        // --- CONTROLS ---
        function togglePlayPause() {
            isRunning = !isRunning;
            const icon = document.getElementById('icon-play');
            const btn = document.getElementById('btn-play');
            if (isRunning) {
                icon.className = "fa-solid fa-pause";
                btn.classList.add('bg-cyan-500', 'glow-cyan');
                btn.classList.remove('bg-space-600');
                addLog("Simulação retomada.");
            } else {
                icon.className = "fa-solid fa-play";
                btn.classList.remove('bg-cyan-500', 'glow-cyan');
                btn.classList.add('bg-space-600');
                addLog("Simulação pausada.");
            }
        }

        function setSpeed(spd) {
            timeScale = spd;
            [1, 5, 10].forEach(s => {
                const b = document.getElementById(`spd-${s}`);
                if (s === spd) {
                    b.className = "px-2.5 py-1 rounded-lg text-xs font-bold bg-cyan-500/20 text-cyan-300 border border-cyan-500";
                } else {
                    b.className = "px-2.5 py-1 rounded-lg text-xs font-bold bg-space-700 text-gray-400 border border-space-600";
                }
            });
            addLog(`Velocidade da simulação ajustada para ${spd}x.`);
        }

        function resetSimulation() {
            simulationTime = 0;
            deflectionProgress = 0.1;
            particles = [];
            addLog("Simulação reiniciada para o estado inicial.");
        }

        function updateLaserPower(val) {
            laserPowerGW = parseFloat(val);
            document.getElementById('label-laser-slider').innerText = laserPowerGW.toFixed(1) + " GW";
            mhdReactorLoad = Math.min(100, Math.round((laserPowerGW / 15) * 100));
            gimbalTemp = Math.round(300 + (laserPowerGW / 15) * 200);
            ablationThrust = (laserPowerGW * 0.42).toFixed(1);
            
            document.getElementById('val-reactor').innerText = laserPowerGW.toFixed(1) + " GW";
            document.getElementById('bar-reactor').style.width = mhdReactorLoad + "%";
            document.getElementById('val-temp').innerText = gimbalTemp + " K";
            document.getElementById('bar-temp').style.width = (gimbalTemp / 600 * 100) + "%";
            document.getElementById('val-thrust').innerText = ablationThrust + " kN";
            document.getElementById('bar-thrust').style.width = (laserPowerGW / 15 * 100) + "%";

            addLog(`Reator MHD ajustado para ${laserPowerGW.toFixed(1)} GW. Empuxo de ablação: ${ablationThrust} kN.`);
        }

        function toggleAutoNav() {
            autoNavActive = !autoNavActive;
            const btn = document.getElementById('btn-autonav');
            if (autoNavActive) {
                btn.innerText = "ATIVADO";
                btn.className = "px-3 py-1 rounded-lg text-xs font-bold bg-emerald-500/20 text-emerald-400 border border-emerald-500/50 glow-emerald transition-all";
                addLog("IA AutoNav reativada. Correção orbital automática ligada.");
            } else {
                btn.innerText = "DESATIVADO";
                btn.className = "px-3 py-1 rounded-lg text-xs font-bold bg-space-700 text-gray-400 border border-space-600 transition-all";
                addLog("ALERTA: IA AutoNav desativada manualmente!");
            }
        }

        function triggerTerraformPulse() {
            addLog("Pulso de geoengenharia direcionado a Marte iniciado. Aumento temporal de radiação solar filtrada.");
            // Quick visual feedback effect
            canvas.style.boxShadow = '0 0 35px rgba(236, 72, 153, 0.8)';
            setTimeout(() => {
                canvas.style.boxShadow = '';
            }, 800);
        }

        function addLog(text) {
            const container = document.getElementById('log-container');
            const now = new Date().toTimeString().split(' ')[0];
            const div = document.createElement('div');
            div.className = "text-gray-300";
            div.innerHTML = `<span class="text-cyan-400">[${now}]</span> ${text}`;
            container.appendChild(div);
            container.scrollTop = container.scrollHeight;
        }

        // --- DRAWING FUNCTIONS ---

        // Draw Starfield Background
        function drawStarfield(w, h) {
            ctx.fillStyle = '#030712';
            ctx.fillRect(0, 0, w, h);

            // Static pseudo-random stars
            ctx.fillStyle = '#ffffff';
            for (let i = 0; i < 80; i++) {
                let x = (Math.sin(i * 99) * 10000) % w;
                let y = (Math.cos(i * 33) * 10000) % h;
                if (x < 0) x += w;
                if (y < 0) y += h;
                let size = (i % 3 === 0) ? 1.5 : 1;
                ctx.globalAlpha = 0.3 + (Math.sin(simulationTime * 0.05 + i) * 0.3);
                ctx.fillRect(x, y, size, size);
            }
            ctx.globalAlpha = 1.0;
        }

        // Macro View: Solar System
        function drawMacroView(w, h) {
            drawStarfield(w, h);
            const cx = w / 2;
            const cy = h / 2;

            // Draw Sun
            const sunGradient = ctx.createRadialGradient(cx - 120, cy, 10, cx - 120, cy, 120);
            sunGradient.addColorStop(0, '#fef08a');
            sunGradient.addColorStop(0.5, '#f59e0b');
            sunGradient.addColorStop(1, 'transparent');

            ctx.fillStyle = sunGradient;
            ctx.beginPath();
            ctx.arc(cx - 120, cy, 120, 0, Math.PI * 2);
            ctx.fill();

            ctx.fillStyle = '#fde047';
            ctx.beginPath();
            ctx.arc(cx - 120, cy, 28, 0, Math.PI * 2);
            ctx.fill();

            // Orbits
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.08)';
            ctx.lineWidth = 1;
            
            // Earth Orbit
            ctx.beginPath();
            ctx.arc(cx - 120, cy, 180, 0, Math.PI * 2);
            ctx.stroke();

            // Mars Orbit
            ctx.beginPath();
            ctx.arc(cx - 120, cy, 250, 0, Math.PI * 2);
            ctx.stroke();

            // Planet positions based on simulationTime
            let earthAngle = simulationTime * 0.008;
            let marsAngle = simulationTime * 0.005;

            let earthX = (cx - 120) + Math.cos(earthAngle) * 180;
            let earthY = cy + Math.sin(earthAngle) * 180;

            let marsX = (cx - 120) + Math.cos(marsAngle) * 250;
            let marsY = cy + Math.sin(marsAngle) * 250;

            // Draw Earth
            ctx.fillStyle = '#38bdf8';
            ctx.beginPath();
            ctx.arc(earthX, earthY, 10, 0, Math.PI * 2);
            ctx.fill();
            ctx.fillStyle = '#93c5fd';
            ctx.font = '10px JetBrains Mono';
            ctx.fillText('TERRA', earthX - 16, earthY + 22);

            // Draw Mars
            ctx.fillStyle = '#f87171';
            ctx.beginPath();
            ctx.arc(marsX, marsY, 8, 0, Math.PI * 2);
            ctx.fill();
            ctx.fillStyle = '#fca5a5';
            ctx.fillText('MARS (Terraformando)', marsX - 45, marsY + 20);

            // Asteroid Trajectory (Collision vs Deflected)
            // Progress of deflection increases with laser power and time
            if (autoNavActive && laserPowerGW > 2) {
                deflectionProgress = Math.min(1.0, deflectionProgress + 0.0005 * timeScale * (laserPowerGW / 10));
            }

            let astStartX = cx + 280;
            let astStartY = cy - 180;
            let astTargetX = earthX;
            let astTargetY = earthY;

            // Deflection curve bend
            let bendFactor = deflectionProgress * 140;
            let currentAstX = astStartX - (simulationTime * 0.6 * timeScale) % 360;
            let progressRatio = Math.max(0, Math.min(1, (cx + 280 - currentAstX) / 380));
            let currentAstY = astStartY + (astTargetY - astStartY) * progressRatio - Math.sin(progressRatio * Math.PI) * bendFactor;

            // Draw original collision path (red dotted)
            ctx.strokeStyle = 'rgba(239, 68, 68, 0.4)';
            ctx.setLineDash([4, 4]);
            ctx.beginPath();
            ctx.moveTo(astStartX, astStartY);
            ctx.lineTo(earthX + 50, earthY - 50);
            ctx.stroke();
            ctx.setLineDash([]);

            // Draw safe deflected path (green line)
            ctx.strokeStyle = 'rgba(16, 185, 129, 0.8)';
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.moveTo(astStartX, astStartY);
            ctx.quadraticCurveTo(cx, cy - 80, earthX + 120, earthY + 80);
            ctx.stroke();

            // Draw SROS Satellites network near Earth
            ctx.strokeStyle = 'rgba(0, 243, 255, 0.3)';
            ctx.lineWidth = 1;
            for (let i = 0; i < 3; i++) {
                let satAngle = earthAngle * 3 + (i * Math.PI * 2 / 3);
                let satX = earthX + Math.cos(satAngle) * 25;
                let satY = earthY + Math.sin(satAngle) * 25;
                
                ctx.fillStyle = '#00f3ff';
                ctx.fillRect(satX - 2, satY - 2, 4, 4);

                // Laser beam to asteroid if active and close enough
                if (progressRatio > 0.2 && progressRatio < 0.86) {
                    ctx.strokeStyle = 'rgba(0, 243, 255, 0.8)';
                    ctx.beginPath();
                    ctx.moveTo(satX, satY);
                    ctx.lineTo(currentAstX, currentAstY);
                    ctx.stroke();
                }
            }

            // Draw Asteroid
            ctx.fillStyle = '#f59e0b';
            ctx.beginPath();
            ctx.arc(currentAstX, currentAstY, 6, 0, Math.PI * 2);
            ctx.fill();
            ctx.fillStyle = '#fbbf24';
            ctx.fillText('Apophis-X (ID-904)', currentAstX + 10, currentAstY - 8);

            // Update Telemetry stats
            earthDistanceKM = Math.max(45000, Math.round(342100 - (progressRatio * 200000) + (deflectionProgress * 80000)));
            impactRisk = Math.max(0, (98.4 - (deflectionProgress * 98.4))).toFixed(2);
            
            document.getElementById('stat-dist').innerText = earthDistanceKM.toLocaleString() + " km";
            const riskElem = document.getElementById('stat-risk');
            if (impactRisk > 50) {
                riskElem.className = "text-sm font-bold text-red-500 mt-0.5";
                riskElem.innerText = impactRisk + "% (ALTO RISCO)";
                document.getElementById('status-threat').className = "flex items-center gap-2 bg-space-900 px-3 py-1.5 rounded-lg border border-red-500/80 animate-pulse";
            } else if (impactRisk > 5) {
                riskElem.className = "text-sm font-bold text-amber-400 mt-0.5";
                riskElem.innerText = impactRisk + "% (MONITORANDO)";
            } else {
                riskElem.className = "text-sm font-bold text-emerald-400 mt-0.5";
                riskElem.innerText = impactRisk + "% (Seguro)";
                document.getElementById('status-threat').className = "flex items-center gap-2 bg-space-900 px-3 py-1.5 rounded-lg border border-emerald-500/40";
            }
        }

        // Micro View: Asteroid Close-up & Laser Ablation
        function drawMicroView(w, h) {
            drawStarfield(w, h);
            const cx = w / 2;
            const cy = h / 2;

            asteroidRotation += 0.005 * timeScale;

            // Draw SROS Satellites on corners
            const sats = [
                { x: cx - 220, y: cy - 120 },
                { x: cx + 220, y: cy - 120 },
                { x: cx, y: cy + 150 }
            ];

            sats.forEach((sat, idx) => {
                ctx.fillStyle = '#1e293b';
                ctx.strokeStyle = '#00f3ff';
                ctx.lineWidth = 2;
                ctx.fillRect(sat.x - 15, sat.y - 10, 30, 20);
                ctx.strokeRect(sat.x - 15, sat.y - 10, 30, 20);
                
                ctx.fillStyle = '#00f3ff';
                ctx.font = '9px JetBrains Mono';
                ctx.fillText(`SROS-SAT 0${idx+1}`, sat.x - 26, sat.y - 14);

                // Draw Laser Beam converging to Asteroid
                if (laserPowerGW > 0) {
                    const gradient = ctx.createLinearGradient(sat.x, sat.y, cx, cy);
                    gradient.addColorStop(0, 'rgba(0, 243, 255, 0.2)');
                    gradient.addColorStop(0.8, 'rgba(0, 243, 255, 0.9)');
                    gradient.addColorStop(1, '#ffffff');

                    ctx.strokeStyle = gradient;
                    ctx.lineWidth = 2 + (laserPowerGW / 3);
                    ctx.beginPath();
                    ctx.moveTo(sat.x, sat.y);
                    ctx.lineTo(cx, cy);
                    ctx.stroke();
                }
            });

            // Draw Irregular Asteroid
            ctx.save();
            ctx.translate(cx, cy);
            ctx.rotate(asteroidRotation);

            ctx.fillStyle = '#475569';
            ctx.strokeStyle = '#94a3b8';
            ctx.lineWidth = 3;
            ctx.beginPath();
            asteroidVertices.forEach((v, i) => {
                let x = Math.cos(v.angle) * v.radius;
                let y = Math.sin(v.angle) * v.radius;
                if (i === 0) ctx.moveTo(x, y);
                else ctx.lineTo(x, y);
            });
            ctx.closePath();
            ctx.fill();
            ctx.stroke();

            // Crater / Laser Focal point glow
            ctx.fillStyle = '#f59e0b';
            ctx.beginPath();
            ctx.arc(0, 0, 12, 0, Math.PI * 2);
            ctx.fill();

            ctx.restore();

            // Emit ablation particles from center (plasma/gas plume opposite to laser action)
            if (laserPowerGW > 0 && isRunning) {
                for (let i = 0; i < Math.floor(laserPowerGW / 2); i++) {
                    particles.push({
                        x: cx,
                        y: cy,
                        vx: (Math.random() - 0.5) * 6 - 4,
                        vy: (Math.random() - 0.5) * 6 + 4,
                        life: 1.0,
                        color: Math.random() > 0.5 ? '#00f3ff' : '#ec4899'
                    });
                }
            }

            // Update & Draw Particles
            for (let i = particles.length - 1; i >= 0; i--) {
                let p = particles[i];
                p.x += p.vx * timeScale;
                p.y += p.vy * timeScale;
                p.life -= 0.02 * timeScale;

                if (p.life <= 0) {
                    particles.splice(i, 1);
                    continue;
                }

                ctx.fillStyle = p.color;
                ctx.globalAlpha = p.life;
                ctx.fillRect(p.x, p.y, 3, 3);
            }
            ctx.globalAlpha = 1.0;

            // Overlay text
            ctx.fillStyle = '#00f3ff';
            ctx.font = '11px JetBrains Mono';
            ctx.fillText(`ABLAÇÃO TÉRMICA: ${(laserPowerGW * 1.8).toFixed(1)} MW/m²`, cx - 100, cy + 110);
        }

        // Control Panel View (Abstract representation or status gauges)
        function drawControlView(w, h) {
            drawStarfield(w, h);
            const cx = w / 2;
            const cy = h / 2;

            // Draw futuristic HUD diagnostic matrix
            ctx.strokeStyle = 'rgba(0, 243, 255, 0.2)';
            ctx.lineWidth = 1;

            ctx.beginPath();
            ctx.arc(cx, cy, 140, 0, Math.PI * 2);
            ctx.stroke();

            ctx.beginPath();
            ctx.arc(cx, cy, 100, 0, Math.PI * 2);
            ctx.setLineDash([4, 6]);
            ctx.stroke();
            ctx.setLineDash([]);

            ctx.strokeStyle = 'rgba(236, 72, 153, 0.4)';
            ctx.beginPath();
            ctx.arc(cx, cy, 60, 0, Math.PI * 2);
            ctx.stroke();

            ctx.fillStyle = '#00f3ff';
            ctx.font = '12px Orbitron';
            ctx.textAlign = 'center';
            ctx.fillText("SROS AI AUTONAV ACTIVE", cx, cy - 10);
            ctx.font = '10px JetBrains Mono';
            ctx.fillStyle = '#10b981';
            ctx.fillText("GIMBALS LOCKED: 100%", cx, cy + 10);
            ctx.textAlign = 'left';
        }

        // --- MAIN ANIMATION LOOP ---
        function simulationLoop() {
            const w = canvas.clientWidth;
            const h = canvas.clientHeight;

            if (canvas.width !== w * window.devicePixelRatio || canvas.height !== h * window.devicePixelRatio) {
                canvas.width = w * window.devicePixelRatio;
                canvas.height = h * window.devicePixelRatio;
                ctx.scale(window.devicePixelRatio, window.devicePixelRatio);
            }

            ctx.clearRect(0, 0, w, h);

            if (isRunning) {
                simulationTime += 1 * timeScale;
            }

            if (currentTab === 'macro') {
                document.getElementById('hud-scale').innerText = "1:10,000,000";
                drawMacroView(w, h);
            } else if (currentTab === 'micro') {
                document.getElementById('hud-scale').innerText = "1:100 (Zoom Close-up)";
                drawMicroView(w, h);
            } else if (currentTab === 'control') {
                document.getElementById('hud-scale').innerText = "Diagnóstico SROS";
                drawControlView(w, h);
            }

            requestAnimationFrame(simulationLoop);
        }

        // Start simulation loop on load
        window.onload = function() {
            addLog("Módulos de rastreamento cósmico online.");
            requestAnimationFrame(simulationLoop);
        };
    </script>
</body>
</html>
