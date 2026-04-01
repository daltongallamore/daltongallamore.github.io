<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TECHNO NEWS LIVE // 130 BPM</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.8.49/Tone.js"></script>
    <style>
        :root {
            --neon-green: #39ff14;
            --dark-bg: #050505;
        }
        body {
            background-color: var(--dark-bg);
            color: var(--neon-green);
            font-family: 'Courier New', Courier, monospace;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            overflow: hidden;
            text-transform: uppercase;
        }
        #club-visualizer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -1;
            opacity: 0.2;
            transition: background 0.1s;
        }
        .container {
            text-align: center;
            z-index: 10;
            background: rgba(0,0,0,0.8);
            padding: 2rem;
            border: 2px solid var(--neon-green);
            box-shadow: 0 0 20px var(--neon-green);
        }
        h1 { font-size: 3rem; margin-bottom: 0; letter-spacing: 5px; }
        #status { font-size: 0.8rem; margin-bottom: 2rem; }
        
        #news-ticker {
            width: 80%;
            border-top: 1px solid var(--neon-green);
            padding: 10px;
            margin-top: 20px;
            overflow: hidden;
            white-space: nowrap;
        }
        #ticker-text {
            display: inline-block;
            padding-left: 100%;
            animation: scroll-left 20s linear infinite;
        }
        @keyframes scroll-left {
            0% { transform: translateX(0); }
            100% { transform: translateX(-100%); }
        }
        button {
            background: transparent;
            color: var(--neon-green);
            border: 2px solid var(--neon-green);
            padding: 15px 30px;
            font-size: 1.5rem;
            cursor: pointer;
            transition: all 0.3s;
        }
        button:hover {
            background: var(--neon-green);
            color: black;
        }
        .pulse { animation: pulse-border 0.5s infinite; }
        @keyframes pulse-border {
            0% { box-shadow: 0 0 10px var(--neon-green); }
            50% { box-shadow: 0 0 40px var(--neon-green); }
            100% { box-shadow: 0 0 10px var(--neon-green); }
        }
    </style>
</head>
<body>

    <div id="club-visualizer"></div>

    <div class="container" id="main-ui">
        <h1>TECHNO NEWS</h1>
        <div id="status">OFFLINE // NO INPUT DETECTED</div>
        <button onclick="startMix()">ENTER THE CLUB</button>
        
        <div id="news-ticker">
            <span id="ticker-text">CONNECTING TO GLOBAL MUSIC SATELLITES...</span>
        </div>
    </div>

    <script>
        let isPlaying = false;
        const visualizer = document.getElementById('club-visualizer');
        const ticker = document.getElementById('ticker-text');
        const status = document.getElementById('status');
        
        // --- TECHNO MIX ENGINE ---
        const kick = new Tone.MembraneSynth({
            envelope: { sustain: 0, attack: 0.02, decay: 0.8 },
            octaves: 10
        }).toDestination();

        const hihat = new Tone.NoiseSynth({
            volume: -10,
            envelope: { attack: 0.001, decay: 0.1, sustain: 0 }
        }).toDestination();

        const bass = new Tone.MonoSynth({
            volume: -12,
            oscillator: { type: "sawtooth" },
            filter: { Q: 6, type: "lowpass", rolloff: -24 },
            envelope: { attack: 0.01, decay: 0.1, sustain: 0.2, release: 0.1 }
        }).toDestination();

        // 4/4 Techno Beat
        const loop = new Tone.Loop(time => {
            kick.triggerAttackRelease("C1", "8n", time);
            
            // Visual pulse on kick
            Tone.Draw.schedule(() => {
                visualizer.style.background = "var(--neon-green)";
                setTimeout(() => visualizer.style.background = "transparent", 50);
            }, time);

            // Hats on the off-beat
            hihat.triggerAttackRelease("16n", time + Tone.Time("8n"));
            
            // Acid Bass Pattern
            if (Math.random() > 0.3) {
                bass.triggerAttackRelease("C2", "16n", time + Tone.Time("16n"));
            }
        }, "4n");

        Tone.Transport.bpm.value = 130;

        // --- NEWS ENGINE ---
        let newsData = ["Welcome to the underground news network.", "Refreshing headlines every 120 seconds."];

        async function fetchMusicNews() {
            console.log("Fetching new headlines...");
            status.innerText = "UPDATING DATA STREAM...";
            
            try {
                // Using a CORS-proxied RSS feed from a music news site (EDM.com)
                const RSS_URL = `https://edm.com/.rss/full/`;
                const PROXY_URL = `https://api.allorigins.win/get?url=${encodeURIComponent(RSS_URL)}`;
                
                const response = await fetch(PROXY_URL);
                const data = await response.json();
                
                const parser = new DOMParser();
                const xml = parser.parseFromString(data.contents, "text/xml");
                const items = xml.querySelectorAll("item");
                
                newsData = Array.from(items).slice(0, 5).map(item => item.querySelector("title").textContent);
                ticker.innerText = newsData.join(" // ");
                
                status.innerText = "LIVE // 130 BPM // NEWS SYNCED";
                announceHeadlines();
            } catch (e) {
                console.error("News fetch failed", e);
                status.innerText = "SIGNAL LOST // RECONNECTING...";
            }
        }

        function announceHeadlines() {
            if (!isPlaying) return;
            
            // "Duck" the music volume slightly
            Tone.Destination.volume.rampTo(-10, 1);
            
            const synthVoice = window.speechSynthesis;
            const intro = new SpeechSynthesisUtterance("Interrupting the mix for the latest headlines.");
            intro.pitch = 0.5;
            intro.rate = 0.9;
            synthVoice.speak(intro);

            newsData.forEach(text => {
                const utterance = new SpeechSynthesisUtterance(text);
                utterance.pitch = 0.1; // Deep "techno" voice
                utterance.rate = 1.1;
                synthVoice.speak(utterance);
            });

            // Restore volume after headlines (estimate duration)
            setTimeout(() => {
                Tone.Destination.volume.rampTo(0, 2);
            }, 15000);
        }

        // --- MAIN CONTROL ---
        async function startMix() {
            if (isPlaying) return;
            
            await Tone.start();
            Tone.Transport.start();
            loop.start(0);
            
            isPlaying = true;
            document.getElementById('main-ui').classList.add('pulse');
            document.querySelector('button').style.display = 'none';
            status.innerText = "LIVE // 130 BPM";
            
            // Initial fetch
            fetchMusicNews();
            
            // Refresh every 2 minutes
            setInterval(fetchMusicNews, 120000);
        }
    </script>
</body>
</html>