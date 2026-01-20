<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CryptoBeat AI - Autonomous Solana Node</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&family=JetBrains+Mono:wght@400;700&display=swap');
        
        :root {
            --neon-blue: #00d2ff;
            --ai-glow: #00ffc3;
            --bg-deep: #05070a;
        }

        body {
            background-color: var(--bg-deep);
            color: #ffffff;
            font-family: 'Plus Jakarta Sans', sans-serif;
            overflow: hidden;
            height: 100vh;
        }

        .glass-panel {
            background: rgba(15, 23, 42, 0.6);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.05);
        }

        .mining-gradient {
            background: linear-gradient(135deg, #00d2ff 0%, #9d50bb 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        @keyframes pulse-ring {
            0% { transform: scale(0.8); opacity: 0.5; }
            100% { transform: scale(1.2); opacity: 0; }
        }

        .ring-effect {
            position: absolute;
            border: 2px solid var(--neon-blue);
            border-radius: 50%;
            animation: pulse-ring 2s infinite;
        }

        .visual-bar {
            width: 4px;
            background: var(--neon-blue);
            border-radius: 10px;
            transition: height 0.1s ease;
        }

        #boot-screen {
            position: fixed;
            inset: 0;
            background: #05070a;
            z-index: 9999;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }
    </style>
</head>
<body>

    <!-- Booting Sequence -->
    <div id="boot-screen">
        <div class="relative w-24 h-24 mb-8">
            <div class="absolute inset-0 border-2 border-cyan-500/20 rounded-full animate-ping"></div>
            <div class="absolute inset-0 border-t-2 border-cyan-400 rounded-full animate-spin"></div>
            <div class="absolute inset-0 flex items-center justify-center">
                <i class="fas fa-robot text-3xl text-cyan-400"></i>
            </div>
        </div>
        <h2 class="text-lg font-black tracking-[0.4em] uppercase mb-4">Neural Protocol</h2>
        <div id="logs" class="w-64 h-20 text-[10px] font-mono text-cyan-400/50 overflow-hidden text-center leading-relaxed">
            > SCANNING NETWORK...
        </div>
        <button id="btn-launch" class="hidden mt-6 px-10 py-3 bg-white text-black font-black rounded-full hover:bg-cyan-400 transition-all text-xs tracking-widest">
            INITIALIZE NODE
        </button>
    </div>

    <!-- Main App -->
    <div class="h-full flex flex-col relative">
        <div class="absolute inset-0 bg-[radial-gradient(circle_at_50%_-20%,#1e293b_0%,#05070a_80%)] -z-10"></div>

        <!-- Top Nav -->
        <nav class="px-8 py-5 flex justify-between items-center glass-panel border-b border-white/5">
            <div class="flex items-center gap-3">
                <i class="fas fa-microchip text-cyan-400 text-xl"></i>
                <div>
                    <h1 class="font-extrabold text-lg tracking-tight">CRYPTO<span class="text-cyan-400">BEAT</span> AI</h1>
                    <p class="text-[8px] text-slate-500 font-black uppercase tracking-widest">Autonomous Solana Node</p>
                </div>
            </div>
            <div class="flex items-center gap-4 bg-black/30 border border-white/5 rounded-2xl px-5 py-2">
                <div class="text-right">
                    <p class="text-[8px] text-slate-500 font-bold uppercase">Reward Balance</p>
                    <p id="balance" class="text-md font-mono font-bold text-cyan-400">0.00000000 SOL</p>
                </div>
                <i class="fas fa-coins text-cyan-400"></i>
            </div>
        </nav>

        <!-- Content -->
        <main class="flex-grow overflow-y-auto p-6 md:p-10 max-w-7xl mx-auto w-full grid grid-cols-1 lg:grid-cols-12 gap-10 pb-32">
            
            <!-- Left: Visualizer -->
            <div class="lg:col-span-8 space-y-8">
                <div class="glass-panel p-10 rounded-[2.5rem] flex flex-col items-center relative overflow-hidden group">
                    <div id="mining-status" class="absolute top-6 left-6 px-4 py-1.5 rounded-full bg-cyan-500/10 border border-cyan-500/20 text-[9px] font-black text-cyan-400 opacity-0 transition-opacity">
                        <i class="fas fa-satellite-dish mr-2 animate-pulse"></i>AI MINING ACTIVE
                    </div>
                    
                    <div class="relative w-64 h-64 md:w-80 md:h-80 mb-10 flex items-center justify-center">
                        <div id="visual-rings"></div>
                        <img id="track-art" src="https://images.unsplash.com/photo-1614064641935-4475e83910c5?q=80&w=500" class="w-full h-full object-cover rounded-full border-[10px] border-black shadow-2xl z-10 transition-transform duration-700">
                    </div>

                    <div class="text-center">
                        <h2 id="track-name" class="text-3xl font-black mb-1">Select Track</h2>
                        <p id="track-artist" class="text-xs text-slate-500 font-bold uppercase tracking-[0.2em]">Neural AI Protocol</p>
                    </div>

                    <div id="bars-container" class="mt-10 flex items-end gap-1.5 h-12">
                        <!-- Bars generated by JS -->
                    </div>
                </div>

                <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                    <div class="glass-panel p-5 rounded-3xl text-center">
                        <p class="text-[8px] text-slate-500 font-bold uppercase mb-1">Multiplier</p>
                        <p class="font-mono font-bold text-cyan-400">x2.85</p>
                    </div>
                    <div class="glass-panel p-5 rounded-3xl text-center">
                        <p class="text-[8px] text-slate-500 font-bold uppercase mb-1">Hash Rate</p>
                        <p class="font-mono font-bold">128.4 GH/s</p>
                    </div>
                    <div class="glass-panel p-5 rounded-3xl text-center">
                        <p class="text-[8px] text-slate-500 font-bold uppercase mb-1">Network</p>
                        <p class="font-mono font-bold">Mainnet</p>
                    </div>
                    <div class="glass-panel p-5 rounded-3xl text-center">
                        <p class="text-[8px] text-slate-500 font-bold uppercase mb-1">Node Stat</p>
                        <p class="font-mono font-bold text-green-400">Stable</p>
                    </div>
                </div>
            </div>

            <!-- Right: Wallet -->
            <div class="lg:col-span-4 space-y-6">
                <div class="glass-panel p-8 rounded-[2.5rem] border-l-2 border-cyan-500/30">
                    <div class="flex items-center justify-between mb-8">
                        <h3 class="text-xs font-black uppercase tracking-widest text-slate-400">AI Managed Wallet</h3>
                        <i class="fas fa-wallet text-slate-600"></i>
                    </div>
                    <div class="space-y-5">
                        <div class="bg-black/40 p-5 rounded-2xl border border-white/5">
                            <p class="text-[8px] text-slate-500 font-black uppercase mb-2">Public Key</p>
                            <p id="wallet-addr" class="font-mono text-[10px] text-cyan-400 break-all leading-relaxed">GEN-SYNCING...</p>
                        </div>
                        <button class="w-full py-4 bg-cyan-600 hover:bg-cyan-500 text-white font-black rounded-2xl text-[10px] uppercase tracking-widest transition-all shadow-xl shadow-cyan-900/20">
                            Withdraw Rewards
                        </button>
                    </div>
                </div>

                <div class="glass-panel p-6 rounded-[2.5rem] h-52 overflow-hidden relative">
                    <h3 class="text-[9px] font-black uppercase tracking-widest text-slate-500 mb-4">Neural Thoughts</h3>
                    <div id="ai-logs" class="space-y-3 font-mono text-[9px] text-cyan-300/30">
                        <!-- AI Logs -->
                    </div>
                </div>
            </div>
        </main>

        <!-- Player Bar -->
        <div class="fixed bottom-6 inset-x-6 z-50 flex justify-center">
            <div class="w-full max-w-xl glass-panel px-8 py-4 rounded-full border border-white/10 shadow-2xl flex items-center justify-between">
                <div class="flex items-center gap-8">
                    <button onclick="changeTrack(-1)" class="text-slate-500 hover:text-white transition"><i class="fas fa-step-backward"></i></button>
                    <button id="play-btn" onclick="togglePlay()" class="w-14 h-14 bg-white text-black rounded-full flex items-center justify-center hover:scale-110 active:scale-95 transition-all shadow-lg">
                        <i id="play-icon" class="fas fa-play text-xl ml-1"></i>
                    </button>
                    <button onclick="changeTrack(1)" class="text-slate-500 hover:text-white transition"><i class="fas fa-step-forward"></i></button>
                </div>
                <div class="hidden md:block text-right">
                    <div id="bar-track-name" class="text-[10px] font-black truncate max-w-[150px]">CryptoBeat Node v2</div>
                    <div class="text-[8px] text-cyan-500 font-black uppercase tracking-tighter">AI Integrated</div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const library = [
            { id: 'jfKfPfyJRdk', title: 'Neural Lofi Protocol', artist: 'AI Collective', art: 'https://images.unsplash.com/photo-1614064641935-4475e83910c5?q=80&w=500' },
            { id: '5qap5aO4i9A', title: 'Blockchain Dream', artist: 'Solana Waves', art: 'https://images.unsplash.com/photo-1639762681485-074b7f938ba0?q=80&w=500' }
        ];

        let currentTrack = 0;
        let isPlaying = false;
        let balance = parseFloat(localStorage.getItem('cb_balance') || '0');
        let wallet = localStorage.getItem('cb_wallet') || '';
        let earningInt;

        // Boot Init
        window.onload = () => {
            const logBox = document.getElementById('logs');
            const bootLogs = ["> INJECTING NEURAL CORE...", "> ESTABLISHING SOL RPC...", "> NODE IDENTITY VERIFIED.", "> SYSTEM READY."];
            let i = 0;
            const interval = setInterval(() => {
                if(i < bootLogs.length) {
                    logBox.innerHTML += `<br>${bootLogs[i]}`;
                    i++;
                } else {
                    clearInterval(interval);
                    document.getElementById('btn-launch').classList.remove('hidden');
                }
            }, 600);

            // Create Visualizer Bars
            const container = document.getElementById('bars-container');
            for(let i=0; i<30; i++) {
                const bar = document.createElement('div');
                bar.className = 'visual-bar';
                bar.style.height = '4px';
                container.appendChild(bar);
            }
            
            updateUI();
        };

        document.getElementById('btn-launch').onclick = () => {
            document.getElementById('boot-screen').style.opacity = '0';
            setTimeout(() => document.getElementById('boot-screen').style.display = 'none', 500);
            if(!wallet) {
                wallet = 'SOL-' + Math.random().toString(36).substring(2, 12).toUpperCase();
                localStorage.setItem('cb_wallet', wallet);
            }
            updateUI();
        };

        function updateUI() {
            const track = library[currentTrack];
            document.getElementById('track-name').innerText = track.title;
            document.getElementById('track-artist').innerText = track.artist;
            document.getElementById('track-art').src = track.art;
            document.getElementById('bar-track-name').innerText = track.title;
            document.getElementById('balance').innerText = balance.toFixed(8) + ' SOL';
            document.getElementById('wallet-addr').innerText = wallet || 'GENERATING...';
        }

        function togglePlay() {
            isPlaying = !isPlaying;
            const icon = document.getElementById('play-icon');
            const status = document.getElementById('mining-status');
            const art = document.getElementById('track-art');

            if(isPlaying) {
                icon.className = 'fas fa-pause text-xl';
                status.style.opacity = '1';
                art.style.transform = 'scale(1.05)';
                startMining();
                addAiLog("Protokol Mining Dimulai...");
            } else {
                icon.className = 'fas fa-play text-xl ml-1';
                status.style.opacity = '0';
                art.style.transform = 'scale(1)';
                stopMining();
                addAiLog("Node Masuk Mode Standby.");
            }
        }

        function startMining() {
            earningInt = setInterval(() => {
                balance += 0.00000125;
                localStorage.setItem('cb_balance', balance.toString());
                document.getElementById('balance').innerText = balance.toFixed(8) + ' SOL';
                
                // Visualizer
                document.querySelectorAll('.visual-bar').forEach(bar => {
                    bar.style.height = (Math.random() * 100) + '%';
                    bar.style.opacity = Math.random() * 0.5 + 0.5;
                });

                if(Math.random() > 0.95) addAiLog("Block tervalidasi di jaringan.");
            }, 1000);
        }

        function stopMining() {
            clearInterval(earningInt);
            document.querySelectorAll('.visual-bar').forEach(bar => bar.style.height = '4px');
        }

        function addAiLog(msg) {
            const container = document.getElementById('ai-logs');
            const log = document.createElement('p');
            log.innerText = `[${new Date().toLocaleTimeString()}] > ${msg}`;
            container.prepend(log);
        }

        function changeTrack(dir) {
            currentTrack = (currentTrack + dir + library.length) % library.length;
            updateUI();
            if(isPlaying) {
                stopMining();
                startMining();
            }
        }
    </script>
</body>
</html>

