<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CryptoBeat AI - Solana Validator Node</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&family=JetBrains+Mono:wght@400;700&display=swap');
        
        :root {
            --solana-green: #14f195;
            --solana-purple: #9945ff;
            --bg-deep: #020408;
        }

        body {
            background-color: var(--bg-deep);
            color: #ffffff;
            font-family: 'Plus Jakarta Sans', sans-serif;
            overflow: hidden;
            height: 100vh;
        }

        .glass-panel {
            background: rgba(10, 15, 25, 0.7);
            backdrop-filter: blur(25px);
            border: 1px solid rgba(255, 255, 255, 0.03);
        }

        .solana-gradient {
            background: linear-gradient(90deg, var(--solana-green), var(--solana-purple));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .visual-bar {
            width: 3px;
            background: var(--solana-green);
            border-radius: 10px;
            transition: height 0.15s ease;
            box-shadow: 0 0 10px rgba(20, 241, 149, 0.3);
        }

        #boot-screen {
            position: fixed;
            inset: 0;
            background: #010204;
            z-index: 9999;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            transition: opacity 0.5s ease;
        }

        .scan-line {
            width: 100%;
            height: 2px;
            background: var(--solana-green);
            position: absolute;
            top: 0;
            left: 0;
            animation: scan 3s linear infinite;
            opacity: 0.2;
        }

        @keyframes scan {
            0% { top: 0; }
            100% { top: 100%; }
        }

        .modal {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.9);
            z-index: 10000;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        input {
            background: rgba(255,255,255,0.05);
            border: 1px solid rgba(255,255,255,0.1);
            color: white;
            padding: 14px 18px;
            border-radius: 14px;
            width: 100%;
            font-family: 'JetBrains Mono', monospace;
            font-size: 13px;
            transition: all 0.3s;
        }

        input:focus {
            outline: none;
            border-color: var(--solana-green);
            background: rgba(20, 241, 149, 0.05);
            box-shadow: 0 0 15px rgba(20, 241, 149, 0.1);
        }

        .success-glow {
            animation: success-pulse 2s infinite;
        }

        @keyframes success-pulse {
            0% { box-shadow: 0 0 0 0 rgba(20, 241, 149, 0.4); }
            70% { box-shadow: 0 0 0 20px rgba(20, 241, 149, 0); }
            100% { box-shadow: 0 0 0 0 rgba(20, 241, 149, 0); }
        }
    </style>
</head>
<body>

    <audio id="audio-player" loop></audio>

    <!-- Modal Penarikan AI Pintar -->
    <div id="wd-modal" class="modal">
        <div class="glass-panel p-8 rounded-[2.5rem] w-full max-w-md border border-white/10 shadow-2xl relative overflow-hidden">
            <div class="absolute top-0 left-0 w-full h-1 bg-gradient-to-r from-solana-green to-solana-purple"></div>
            
            <div class="flex items-center gap-4 mb-6">
                <div class="w-10 h-10 bg-purple-500/20 rounded-xl flex items-center justify-center">
                    <i class="fas fa-brain text-purple-400"></i>
                </div>
                <div>
                    <h3 class="font-black text-sm uppercase tracking-widest">AI Payout Engine</h3>
                    <p class="text-[9px] text-slate-500 font-bold uppercase">Solana Mainnet Bridge</p>
                </div>
            </div>
            
            <!-- Tahap 1: Input -->
            <div id="wd-step-1" class="space-y-5">
                <div class="bg-black/30 p-4 rounded-xl border border-white/5">
                    <p class="text-[10px] text-slate-400 leading-relaxed">Masukkan alamat dompet Solana Anda di bawah ini. AI akan memproses pembayaran langsung ke dompet tersebut melalui jalur sponsor prioritas.</p>
                </div>
                
                <div class="space-y-2">
                    <label class="text-[9px] font-black uppercase text-slate-500 ml-1">Dompet Tujuan (Solana Address)</label>
                    <input type="text" id="target-wallet" placeholder="Contoh: 7xKX... atau H6Rh..." spellcheck="false" />
                </div>

                <div class="flex justify-between items-center px-1">
                    <div class="flex flex-col">
                        <span class="text-[9px] text-slate-500 uppercase font-bold">Saldo Tersedia</span>
                        <span id="modal-bal" class="text-sm font-mono font-bold text-solana-green">0.00 SOL</span>
                    </div>
                    <div class="text-right">
                        <span class="text-[9px] text-slate-500 uppercase font-bold">Biaya Gas</span>
                        <span class="text-[10px] font-mono font-bold text-purple-400 block">DITANGGUNG AI</span>
                    </div>
                </div>

                <button onclick="processAIWithdrawal()" class="w-full py-4 bg-white text-black font-black rounded-2xl text-xs uppercase tracking-widest hover:bg-solana-green transition-all shadow-lg active:scale-95">
                    Konfirmasi Penarikan
                </button>
                <button onclick="closeModal()" class="w-full py-2 text-slate-500 text-[10px] uppercase font-bold hover:text-white transition-colors">Batalkan</button>
            </div>

            <!-- Tahap 2: Proses & Berhasil -->
            <div id="wd-step-2" class="hidden space-y-8 text-center py-6">
                <div id="wd-loader" class="relative w-20 h-20 mx-auto">
                    <div class="absolute inset-0 border-4 border-purple-500/10 rounded-full"></div>
                    <div class="absolute inset-0 border-t-4 border-solana-green rounded-full animate-spin"></div>
                    <div class="absolute inset-0 flex items-center justify-center">
                        <i id="wd-icon" class="fas fa-satellite-dish text-2xl text-solana-green"></i>
                    </div>
                </div>
                
                <div class="space-y-3">
                    <div id="wd-status" class="font-mono text-[11px] text-cyan-400 leading-relaxed min-h-[40px]">
                        AI: Memverifikasi dompet di jaringan...
                    </div>
                    
                    <div id="tx-info" class="hidden animate-in fade-in duration-700">
                        <p class="text-[10px] text-slate-500 font-bold uppercase mb-2">Status Transaksi Berhasil</p>
                        <a id="explorer-link" href="#" target="_blank" class="inline-flex items-center gap-2 px-4 py-2 bg-solana-green/10 border border-solana-green/20 rounded-lg text-solana-green hover:bg-solana-green/20 transition-all">
                            <i class="fas fa-external-link-alt text-[10px]"></i>
                            <span class="font-mono text-[10px] font-bold">LIHAT DI SOLSCAN</span>
                        </a>
                    </div>
                </div>

                <button id="btn-done" onclick="closeModal()" class="hidden w-full py-4 bg-white/5 hover:bg-white/10 text-white font-black rounded-2xl text-[10px] uppercase tracking-widest border border-white/10 transition-all">
                    Selesai & Tutup
                </button>
            </div>
        </div>
    </div>

    <!-- Booting -->
    <div id="boot-screen">
        <div class="scan-line"></div>
        <div class="relative w-28 h-28 mb-10">
            <div class="absolute inset-0 border-2 border-solana-green/20 rounded-full animate-ping"></div>
            <div class="absolute inset-0 border-t-2 border-solana-green rounded-full animate-spin"></div>
            <div class="absolute inset-0 flex items-center justify-center">
                <i class="fas fa-microchip text-4xl text-solana-green"></i>
            </div>
        </div>
        <h2 class="text-xl font-black tracking-[0.6em] solana-gradient mb-6 uppercase">Solana AI Node</h2>
        <div id="logs" class="w-72 h-24 text-[11px] font-mono text-cyan-400/40 overflow-hidden text-center">
            > INITIALIZING NEURAL RPC...
        </div>
        <button id="btn-launch" class="hidden mt-8 px-12 py-4 bg-white text-black font-black rounded-full hover:shadow-[0_0_25px_rgba(20,241,149,0.4)] transition-all text-xs tracking-[0.2em]">
            AKTIFKAN NODE
        </button>
    </div>

    <!-- UI Utama -->
    <div class="h-full flex flex-col relative">
        <div class="absolute inset-0 bg-[radial-gradient(circle_at_50%_0%,#1a202c_0%,#020408_70%)] -z-10"></div>

        <!-- Navigasi -->
        <nav class="px-8 py-5 flex justify-between items-center glass-panel border-b border-white/5">
            <div class="flex items-center gap-4">
                <div class="p-2.5 bg-gradient-to-br from-solana-green to-solana-purple rounded-xl shadow-lg shadow-purple-900/20">
                    <i class="fas fa-server text-white"></i>
                </div>
                <div>
                    <h1 class="font-extrabold text-xl tracking-tight">AI<span class="text-solana-green">NODE</span></h1>
                    <p class="text-[8px] text-slate-500 font-black uppercase tracking-[0.3em]">Validator Jaringan Solana</p>
                </div>
            </div>
            <div class="flex items-center gap-5">
                <div class="hidden md:flex flex-col text-right">
                    <span class="text-[8px] text-slate-500 font-bold uppercase tracking-widest">Kapasitas Jaringan</span>
                    <span class="text-xs font-mono text-solana-green">65,482 TPS</span>
                </div>
                <div class="bg-white/5 border border-white/10 rounded-2xl px-5 py-2.5 flex items-center gap-4">
                    <div class="text-right">
                        <p class="text-[8px] text-slate-500 font-bold uppercase">Saldo Reward</p>
                        <p id="balance" class="text-md font-mono font-bold text-solana-green">0.00000000 SOL</p>
                    </div>
                    <i class="fas fa-bolt text-solana-green"></i>
                </div>
            </div>
        </nav>

        <!-- Konten Utama -->
        <main class="flex-grow overflow-y-auto p-4 md:p-10 max-w-7xl mx-auto w-full grid grid-cols-1 lg:grid-cols-12 gap-8 pb-40">
            
            <!-- Validator Core -->
            <div class="lg:col-span-8 space-y-8">
                <div class="glass-panel p-10 rounded-[3rem] flex flex-col items-center relative overflow-hidden group">
                    <div id="mining-status" class="absolute top-8 left-8 flex items-center gap-3 px-4 py-2 rounded-full bg-solana-green/10 border border-solana-green/20 text-[10px] font-black text-solana-green opacity-0 transition-all">
                        <span class="flex h-2 w-2 relative">
                            <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-solana-green opacity-75"></span>
                            <span class="relative inline-flex rounded-full h-2 w-2 bg-solana-green"></span>
                        </span>
                        VALIDASI BLOK AKTIF
                    </div>
                    
                    <div class="relative w-56 h-56 md:w-72 md:h-72 mb-10 flex items-center justify-center">
                        <div class="absolute inset-0 bg-solana-purple/20 blur-[100px] -z-10 rounded-full"></div>
                        <img id="track-art" src="" class="w-full h-full object-cover rounded-full border-[8px] border-black/50 shadow-2xl z-10 transition-all duration-700">
                    </div>

                    <div class="text-center z-10">
                        <h2 id="track-name" class="text-2xl md:text-3xl font-black mb-2 tracking-tight uppercase">Menunggu Aktivasi</h2>
                        <p id="track-artist" class="text-[10px] text-slate-500 font-bold uppercase tracking-[0.3em]">AI Node validator siap beroperasi</p>
                    </div>

                    <div id="bars-container" class="mt-12 flex items-end gap-1.5 h-16">
                        <!-- Bars generated by JS -->
                    </div>
                </div>

                <div class="grid grid-cols-2 md:grid-cols-4 gap-4" id="stats-grid">
                    <!-- Stats Card -->
                </div>
            </div>

            <!-- Management & Logs -->
            <div class="lg:col-span-4 space-y-6">
                <div class="glass-panel p-8 rounded-[2.5rem] border-t-4 border-solana-purple/50 shadow-xl">
                    <div class="flex items-center justify-between mb-8">
                        <h3 class="text-[10px] font-black uppercase tracking-widest text-slate-400">Node Manajemen</h3>
                        <i class="fas fa-shield-halved text-purple-500"></i>
                    </div>
                    <div class="space-y-4">
                        <div class="bg-black/40 p-5 rounded-2xl border border-white/5">
                            <p class="text-[8px] text-slate-500 font-black uppercase mb-2">ID Node Internal</p>
                            <p id="wallet-addr" class="font-mono text-[10px] text-solana-green break-all leading-relaxed">GENERATING...</p>
                        </div>
                        <button onclick="openWDModal()" class="group relative w-full py-5 bg-gradient-to-r from-solana-green to-solana-purple text-black font-black rounded-2xl text-[11px] uppercase tracking-widest transition-all overflow-hidden shadow-2xl shadow-solana-green/20">
                            <span class="relative z-10 flex items-center justify-center gap-2">
                                <i class="fas fa-paper-plane"></i> Penarikan via AI
                            </span>
                            <div class="absolute inset-0 bg-white opacity-0 group-hover:opacity-20 transition-opacity"></div>
                        </button>
                    </div>
                </div>

                <div class="glass-panel p-8 rounded-[2.5rem] h-64 overflow-hidden relative border border-white/5">
                    <h3 class="text-[10px] font-black uppercase tracking-widest text-slate-500 mb-6 flex items-center gap-2">
                        <i class="fas fa-brain text-[8px]"></i> Aliran Pikiran Node
                    </h3>
                    <div id="ai-logs" class="space-y-4 font-mono text-[9px] text-cyan-300/30">
                        <!-- AI Logs -->
                    </div>
                </div>
            </div>
        </main>

        <!-- Player Bar -->
        <div class="fixed bottom-8 inset-x-6 z-50 flex justify-center">
            <div class="w-full max-w-xl glass-panel px-8 py-5 rounded-full border border-white/10 shadow-2xl flex items-center justify-between">
                <div class="flex items-center gap-10">
                    <button onclick="changeTrack(-1)" class="text-slate-500 hover:text-white transition"><i class="fas fa-backward-step text-lg"></i></button>
                    <button id="play-btn" onclick="togglePlay()" class="w-16 h-16 bg-white text-black rounded-full flex items-center justify-center hover:scale-105 active:scale-95 transition-all shadow-xl">
                        <i id="play-icon" class="fas fa-play text-2xl ml-1"></i>
                    </button>
                    <button onclick="changeTrack(1)" class="text-slate-500 hover:text-white transition"><i class="fas fa-forward-step text-lg"></i></button>
                </div>
                <div class="hidden sm:block text-right pr-4">
                    <div id="bar-track-name" class="text-[11px] font-black tracking-tight mb-1">System Node</div>
                    <div class="flex items-center gap-2 justify-end">
                        <span class="w-1.5 h-1.5 rounded-full bg-solana-green animate-pulse"></span>
                        <span class="text-[8px] text-solana-green font-black uppercase tracking-widest">Live RPC</span>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const library = [
            { 
                title: 'Solana Genesis Block', 
                artist: 'Mainnet Neural', 
                art: 'https://images.unsplash.com/photo-1639762681485-074b7f938ba0?q=80&w=500',
                src: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3' 
            },
            { 
                title: 'Validator Nightshift', 
                artist: 'RPC Collective', 
                art: 'https://images.unsplash.com/photo-1642104704074-907c0698cbd9?q=80&w=500',
                src: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3'
            }
        ];

        let currentTrack = 0;
        let isPlaying = false;
        let balance = parseFloat(localStorage.getItem('cb_v4_balance') || '0');
        let wallet = localStorage.getItem('cb_v4_wallet') || '';
        let earningInt;
        
        const audio = document.getElementById('audio-player');

        // Render Stats
        function renderStats() {
            const container = document.getElementById('stats-grid');
            const stats = [
                { label: 'AI Multiplier', value: 'x4.25', color: 'text-purple-400' },
                { label: 'Block Time', value: '400ms', color: 'text-solana-green' },
                { label: 'Status Epoch', value: '#721', color: 'text-white' },
                { label: 'Kualitas Node', value: '99.9%', color: 'text-cyan-400' }
            ];
            container.innerHTML = stats.map(s => `
                <div class="glass-panel p-5 rounded-3xl group hover:border-solana-green/20 transition-all">
                    <p class="text-[8px] text-slate-500 font-bold uppercase tracking-widest mb-1">${s.label}</p>
                    <p class="font-mono font-bold ${s.color}">${s.value}</p>
                </div>
            `).join('');
        }

        window.onload = () => {
            renderStats();
            const logBox = document.getElementById('logs');
            const bootLogs = [
                "> INITIALIZING NEURAL ENGINE...",
                "> CONNECTING TO SOLANA CLUSTER...",
                "> GENERATING VALIDATOR KEYPAIR...",
                "> HANDSHAKE SUCCESSFUL."
            ];
            let i = 0;
            const interval = setInterval(() => {
                if(i < bootLogs.length) {
                    logBox.innerHTML += `<br>${bootLogs[i]}`;
                    i++;
                } else {
                    clearInterval(interval);
                    document.getElementById('btn-launch').classList.remove('hidden');
                }
            }, 700);

            const container = document.getElementById('bars-container');
            for(let i=0; i<35; i++) {
                const bar = document.createElement('div');
                bar.className = 'visual-bar';
                bar.style.height = '6px';
                container.appendChild(bar);
            }
            updateUI();
        };

        document.getElementById('btn-launch').onclick = () => {
            const boot = document.getElementById('boot-screen');
            boot.style.opacity = '0';
            setTimeout(() => boot.style.display = 'none', 500);
            
            if(!wallet) {
                wallet = 'VLD-' + Math.random().toString(36).substring(2, 14).toUpperCase();
                localStorage.setItem('cb_v4_wallet', wallet);
            }
            updateUI();
            addAiLog("Node diaktifkan. Berpartisipasi dalam konsensus Solana.");
        };

        function updateUI() {
            const track = library[currentTrack];
            document.getElementById('track-name').innerText = track.title;
            document.getElementById('track-artist').innerText = track.artist;
            document.getElementById('track-art').src = track.art;
            document.getElementById('bar-track-name').innerText = track.title;
            document.getElementById('balance').innerText = balance.toFixed(8) + ' SOL';
            document.getElementById('wallet-addr').innerText = wallet || 'GENERATING...';
            audio.src = track.src;
        }

        function togglePlay() {
            const icon = document.getElementById('play-icon');
            const status = document.getElementById('mining-status');
            const art = document.getElementById('track-art');

            if(!isPlaying) {
                audio.play().then(() => {
                    isPlaying = true;
                    icon.className = 'fas fa-pause text-2xl';
                    status.style.opacity = '1';
                    art.style.transform = 'scale(1.1)';
                    art.style.borderColor = '#14f195';
                    startMining();
                    addAiLog("Memproses transaksi jaringan Solana...");
                }).catch(e => addAiLog("System: Gagal memutar audio. Mohon interaksi pengguna diperlukan."));
            } else {
                audio.pause();
                isPlaying = false;
                icon.className = 'fas fa-play text-2xl ml-1';
                status.style.opacity = '0';
                art.style.transform = 'scale(1)';
                art.style.borderColor = 'transparent';
                stopMining();
                addAiLog("Node masuk mode idle.");
            }
        }

        function startMining() {
            earningInt = setInterval(() => {
                balance += 0.00000450;
                localStorage.setItem('cb_v4_balance', balance.toString());
                document.getElementById('balance').innerText = balance.toFixed(8) + ' SOL';
                
                document.querySelectorAll('.visual-bar').forEach(bar => {
                    bar.style.height = (Math.random() * 100) + '%';
                });

                if(Math.random() > 0.92) addAiLog("Block #" + Math.floor(Math.random()*1000000) + " tervalidasi. Reward diterima.");
            }, 1000);
        }

        function stopMining() {
            clearInterval(earningInt);
            document.querySelectorAll('.visual-bar').forEach(bar => bar.style.height = '6px');
        }

        function addAiLog(msg) {
            const container = document.getElementById('ai-logs');
            const log = document.createElement('div');
            log.className = "border-l border-white/10 pl-3 py-1 animate-in slide-in-from-left duration-300";
            log.innerHTML = `<span class="text-slate-600">[${new Date().toLocaleTimeString()}]</span> <span class="text-solana-green">></span> ${msg}`;
            container.prepend(log);
        }

        function changeTrack(dir) {
            currentTrack = (currentTrack + dir + library.length) % library.length;
            const wasPlaying = isPlaying;
            if(wasPlaying) togglePlay();
            updateUI();
            if(wasPlaying) setTimeout(togglePlay, 150);
        }

        // WD LOGIC
        function openWDModal() {
            if (balance <= 0) {
                alert("AI: Saldo tidak mencukupi untuk melakukan penarikan.");
                return;
            }
            document.getElementById('modal-bal').innerText = balance.toFixed(8) + ' SOL';
            document.getElementById('wd-modal').style.display = 'flex';
        }

        function closeModal() {
            document.getElementById('wd-modal').style.display = 'none';
            document.getElementById('wd-step-1').classList.remove('hidden');
            document.getElementById('wd-step-2').classList.add('hidden');
            document.getElementById('tx-info').classList.add('hidden');
            document.getElementById('btn-done').classList.add('hidden');
            document.getElementById('wd-icon').className = 'fas fa-satellite-dish text-2xl text-solana-green';
            document.getElementById('wd-loader').classList.remove('success-glow');
        }

        function processAIWithdrawal() {
            const addr = document.getElementById('target-wallet').value.trim();
            
            // Validasi Alamat Solana Sederhana
            if(addr.length < 32) {
                addAiLog("AI Error: Alamat dompet terlalu pendek.");
                alert("AI Error: Alamat Solana tidak valid. Mohon masukkan alamat yang benar.");
                return;
            }

            document.getElementById('wd-step-1').classList.add('hidden');
            document.getElementById('wd-step-2').classList.remove('hidden');
            
            const status = document.getElementById('wd-status');
            const steps = [
                "AI: Menganalisis keamanan dompet tujuan...",
                "AI: Menghubungkan ke Cluster Mainnet...",
                "AI: Menyiapkan instruksi transfer SPL...",
                "AI: Mengenkripsi tanda tangan transaksi...",
                "AI: Menandatangani paket data ke " + addr.substring(0,6) + "..."
            ];

            let i = 0;
            const stepInt = setInterval(() => {
                if(i < steps.length) {
                    status.innerText = steps[i];
                    i++;
                } else {
                    clearInterval(stepInt);
                    
                    // Generate Mock Transaction Signature
                    const sig = Math.random().toString(36).substring(2,15) + Math.random().toString(36).substring(2,15) + "AI";
                    
                    status.innerHTML = `<span class="text-solana-green font-black">TRANSAKSI BERHASIL</span><br>Dana sebesar ${balance.toFixed(4)} SOL telah dikirim ke dompet Anda.`;
                    
                    // Update UI Berhasil
                    document.getElementById('wd-icon').className = 'fas fa-check text-2xl text-solana-green';
                    document.getElementById('wd-loader').classList.add('success-glow');
                    document.getElementById('tx-info').classList.remove('hidden');
                    document.getElementById('btn-done').classList.remove('hidden');
                    
                    // Set Explorer Link (Mocking Solscan)
                    const link = document.getElementById('explorer-link');
                    link.href = `https://solscan.io/tx/${sig}`; 
                    
                    addAiLog(`WD Berhasil: ${balance.toFixed(4)} SOL terkirim ke ${addr.substring(0,8)}...`);
                    
                    // Reset Balance
                    balance = 0;
                    localStorage.setItem('cb_v4_balance', '0');
                    updateUI();
                }
            }, 1200);
        }
    </script>
</body>
</html>

