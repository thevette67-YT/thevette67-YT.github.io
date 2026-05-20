<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>New Tab</title>
    <style>
        :root {
            --neon-blue: #00d4ff;
            --neon-green: #39ff14;
            --neon-red: #ff003c;
            --bg: #020202;
            --panel: rgba(15, 15, 15, 0.9);
        }

        body::before {
            content: " ";
            display: block;
            position: fixed;
            top: 0; left: 0; bottom: 0; right: 0;
            background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.25) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.06), rgba(0, 255, 0, 0.02), rgba(0, 0, 255, 0.06));
            z-index: 9999;
            pointer-events: none;
            background-size: 100% 3px, 3px 100%;
        }

        body {
            background-color: var(--bg);
            color: #fff;
            font-family: 'Courier New', Courier, monospace;
            margin: 0;
            padding: 0 20px 50px;
            overflow-x: hidden;
        }

        /* Binary rain canvas sits behind everything */
        #binaryCanvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            display: block;
            z-index: 0;
            pointer-events: none;
        }

        /* All existing content stays above the canvas */
        .credits-bar, h1, h3, .search-container, .panel, .grid {
            position: relative;
            z-index: 1;
        }

        .credits-bar {
            background: #000;
            border-bottom: 2px solid var(--neon-blue);
            padding: 15px;
            text-align: center;
            position: sticky;
            top: 0;
            z-index: 1000;
            box-shadow: 0 0 20px rgba(0, 212, 255, 0.5);
            text-transform: uppercase;
            letter-spacing: 3px;
        }
        .credits-bar span { color: var(--neon-blue); text-shadow: 0 0 10px var(--neon-blue); }

        h1 {
            text-align: center;
            font-size: 4rem;
            margin: 50px 0;
            text-transform: uppercase;
            letter-spacing: 15px;
            text-shadow: 0 0 10px var(--neon-blue), 0 0 30px var(--neon-blue);
        }

        .search-container { max-width: 800px; margin: 0 auto 40px; }
        #search {
            width: 100%;
            background: #000;
            border: 2px solid #222;
            padding: 20px;
            color: var(--neon-green);
            font-size: 1.2em;
            outline: none;
        }
        #search:focus { border-color: var(--neon-green); box-shadow: 0 0 20px rgba(57, 255, 20, 0.4); }

        .panel {
            background: var(--panel);
            border: 1px solid #333;
            padding: 25px;
            margin: 0 auto 30px;
            max-width: 1100px;
            position: relative;
        }
        .report-panel { border-top: 4px solid var(--neon-red); }
        .server-panel { border-top: 4px solid var(--neon-green); }

        .server-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 10px; }
        .server-tag {
            background: #080808;
            padding: 12px;
            border: 1px solid #1a1a1a;
            color: var(--neon-green);
            font-size: 0.9em;
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
            gap: 20px;
            max-width: 1400px;
            margin: 0 auto;
        }
        .card {
            background: #0a0a0a;
            border: 1px solid #222;
            padding: 30px 15px;
            text-align: center;
            cursor: pointer;
            transition: 0.3s;
            display: flex;
            flex-direction: column;
            justify-content: center;
            height: 140px;
        }
        .card:hover {
            border-color: var(--neon-blue);
            transform: scale(1.05);
            box-shadow: -5px 5px 0px var(--neon-blue);
        }
        .card strong { color: #fff; text-transform: uppercase; letter-spacing: 2px; margin-bottom: 10px; }
        .card small { color: #444; font-size: 0.65em; word-break: break-all; }

        a { color: var(--neon-blue); text-decoration: none; font-weight: bold; }
    </style>
</head>
<body>

    <!-- Binary rain canvas (background layer) -->
    <canvas id="binaryCanvas"></canvas>

    <div class="credits-bar">
        <span>Team:</span>thevtte67,Jordy,Baby Whelan,Flynn,Vonkoko
    </div>

    <h1>WEBSITES</h1>
    <h3>some websites might not work please report if you find any</h3> 

    <div class="search-container">
        <input type="text" id="search" placeholder="Search" onkeyup="filterHub()">
    </div>

    <div class="panel report-panel">
        <h2 style="margin:0; font-size: 1.2em; color: var(--neon-red); text-align: center;">WHAT DO YOU DO IF YOU FIND GAMING WEBSITE OR A BUG</h2>
        <p style="text-align:center; font-size: 0.9em;">EMAIL: <b>f.haddock@bcc.vic.edu.au or j.whelan@bcc.vic.edu.au</b> OR USE <a href="https://forms.gle/83JRrs6Vg5EoizfW8" target="_blank">DATAFORM</a></p>
    </div>

    <div class="panel server-panel">
        <h2 style="margin:0; font-size: 1.2em; color: var(--neon-green);">> EAGLERCRAFT_NODES</h2>
        <div class="server-grid">
            <div class="server-tag">wss://mc.arch.lol (ArchMC - laggy)</div>
            <div class="server-tag">wss://aeon-network.net/aeon (Aeon - Realms)</div>
            <div class="server-tag">wss://ggsmp.net (GG.SMP)</div>
            <div class="server-tag">wss://pcsmp.net (PC.SMP)</div>
            <div class="server-tag">wss://sus.shhnowisnottheti.me (Anarchy)</div>
            <div class="server-tag">wss://zentic.cc (Untested)</div>
        </div>
    </div>

    <div class="grid" id="mainGrid"></div>

    <script>
        // ── Binary rain background ──────────────────────────────────────────
        const canvas = document.getElementById('binaryCanvas');
        const ctx = canvas.getContext('2d');
        const chars = ['0', '1'];
        const fontSize = 14;
        let columns = 0;
        let rainDrops = [];

        function setupCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
            columns = Math.floor(canvas.width / fontSize) + 1;
            rainDrops = [];
            for (let x = 0; x < columns; x++) {
                rainDrops[x] = Math.random() * -100;
            }
        }

        function draw() {
            ctx.fillStyle = 'rgba(0, 0, 0, 0.06)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = '#00ff66';
            ctx.font = 'bold ' + fontSize + 'px monospace';
            for (let i = 0; i < rainDrops.length; i++) {
                const text = chars[Math.floor(Math.random() * chars.length)];
                const x = i * fontSize;
                const y = rainDrops[i] * fontSize;
                ctx.fillText(text, x, y);
                if (y > canvas.height && Math.random() > 0.975) {
                    rainDrops[i] = 0;
                }
                rainDrops[i]++;
            }
        }

        setupCanvas();
        window.addEventListener('resize', setupCanvas);
        setInterval(draw, 30);

        // ── Games hub ───────────────────────────────────────────────────────
        const db = [
            "https://www.seniorsonline.vic.gov.au/services-information/games", "https://mathsframe.co.uk/en/resources/category/22/most-popular", "https://game.gys.cn/", "https://www.aarp.org/games/category/arcade/", "https://class-80x.github.io/#google_vignette", "https://arcade.makecode.com/S67341-39208-84128-32310", 
"https://classroom6x.me/", "https://hackingschoolwebsites.github.io/slowroads/", "https://coolmath-game.github.io/", "https://git-hub-games.github.io/", "https://unblocked-2026.github.io/", "https://skribbl.io/", "https://pisaucer.github.io/boredhtml/", "https://cookieclicker.bobjoerules.com/", "https://cookie-clicker1.web.app/login", "https://incrediblewebsite.github.io/", "https://io.google/2026/puzzle/play/", "https://ultimatemen.github.io/", "https://1-12-tuffnamewalker.pages.dev/", "https://andrei-paraschiv.github.io/project-laser-game/", "https://www.mbmg.mtech.edu/kids/doodlejump.asp", "https://eaglercraft-archive.github.io/Koneclient-1.8-web/",
"https://google.com", "https://dailygames.discover.google.com/games/tic-tac-go", "https://google.com", "https://www.google.com/logos/2010/pacman10-i.html",  "https://mathadventure1.github.io/sm64/sm64/index.html", "https://jonahkc.github.io/youtube-unblcked/", "https://cattn.github.io/gba/", "https://theooofficial.github.io/myRETROGAMES/", "https://gist.github.com/eddy-22/2cdfadfa539cdaccfd0a9e31bf98e6d6", "https://wavebreakerv19.netlify.app", "https://cardshooterv13.netlify.app", "https://smdgames.github.io/core-html/games.html", "https://project-scythe.vercel.app/classwork", "https://bidoofery.github.io/nativegames/", "https://lps7x.csb.app/", "https://incrediblewebsite.github.io", "https://www.mortgagecalculator.org/money-games/", "https://genarcy.github.io/", "https://thepizzaedition-games.github.io/", "https://mathclass.vercel.app/", "https://www.typinggames.zone/", "https://minecraftforbcc.netlify.app", "https://emojiletforbcc.netlify.app" ,
"https://irv77.github.io/AmplerLauncher/" ,
"https://nite.freetls.fastly.net/home.html" ,
"https://nite.freetls.fastly.net/home.html" ,
"https://huggingface.co/spaces/soiz1/Eaglercraft-1.12.2-forge" ,
"https://eaglercraft.tools/#google_vignette" ,
"https://www.boredbutton.com/random" ,
"https://ludorex.com/en/unblocked-games-76-77-66-67-premium#google_vignette" ,
"https://ludorex.com/en/game/shell-shockers" ,
"https://short.io/features/cloaking/" ,
"https://geometry-dashonline.vercel.app/" ,
"https://www.behance.net/search/projects/slope%20unblocked%20game" ,
"https://turisvpn.com/blog/unblocked-games-g-plus/" ,
"https://extpose.com/ext/gjbljldphhendkfnhkocmmgpnfhdhdmf" ,
"https://www.mathsisfun.com/games/index.html" ,
"stats.com/d/eabibcgfihajelmokbfcphekhnjmdilm" ,
"https://ubg-plus.github.io/" ,
"https://www.classtools.net/" ,
"https://www.classplusgames.com/" ,
"https://evilmonke.github.io" ,
"https://evilmonke.github.io/eaglercraft/" ,
"https://evilmonke.github.io/games/eaglercraftx/" ,
"https://evilmonke.github.io/games/duck-life-4/" ,
"https://evilmonke.github.io/games/yohoho/" ,
"https://evilmonke.github.io/games/flash/?game=creative-kill-chamber" ,
"https://the-pizza-editionfree.github.io/" ,
"https://www.merriam-webster.com/games/quordle/#/" ,
"https://cashcalculatoronline.com/blooket-calculator/" ,
"https://www.nytco.com/games/" ,
"https://listdle.com/" ,
"https://nerdlegame.com/" ,
"https://www.sports-reference.com/immaculate-grid/" ,
"https://www.artworksforchange.org/games/wordle/index.html#daily" ,
"https://www.google.com/logos/2017/cricket17/cricket17.html?hl=en&origin=www.google.com" ,
"https://www.google.com/logos/2019/july4th19/r6/july4th19.html?hl=en&origin=www.google.com" ,
"https://artsandculture.google.com/play" ,
"https://dailygames.discover.google.com/games/color-tiles" ,
"https://www.google.com/logos/fnbx/solitaire/standalone.4.html?hl=en&origin=www.google.com" ,
"https://www.google.com/logos/fnbx/solitaire/standalone.4.html?hl=en&origin=www.google.com" ,
"https://www.google.com/logos/2018/gnomes/gnomes18.html" ,
"https://www.google.com/logos/2020/kitsune/rc7/kitsune20.html?hl=en&origin=www.google.com" ,
"https://www.momopixel.com/google-doodle" ,
"https://www.google.com/logos/2022/lawson/r1201/lawson.html" ,
"https://www.google.com/logos/2015/ponyexpress/ponyexpress15.html" ,
"https://www.google.com/logos/2016/halloween16/halloween16.html" ,
"https://www.google.com/logos/2021/petanque/r041323/petanque.transparent.html?" ,
"https://immersive=1&hl=en&origin=www.google.com" ,
"https://www.google.com/logos/2023/panipuri/r3/panipuri.html" ,
"https://santatracker.google.com/matching.html" ,
"https://pbskids.org/games/matching" ,
"https://www.google.com/logos/2024/moon/moon_march-rc2/moon_march.html?" ,
"https://www.online-stopwatch.com/" ,
"https://rockpaperscissors-ai.vercel.app/" ,
"https://www.microsoft.com/en-us/edge/surf?form=MA13E3" ,
"https://surf.jackbuehner.com/" ,
"https://rbeesley.github.io/MicrosoftEdge-SURF/" ,
"https://blockblast-game.fr/" ,
"https://blockblast-unblocked.org/c/new" ,
"https://blockblastunblocked.github.io/" ,
"https://block-blasts.github.io/" ,
"https://www.google.com.au/search?q=google+doodles&safe=active&ssui=on" ,
"https://deadzonemultiplayer4bcc.netlify.app" ,
"https://deadzonemultiplayer.netlify.app/" ,
"https://binarybackground.netlify.app" ,
"https://omgilovethisweb.netlify.app" ,
"https://op1um.pages.dev/" ,
"https://coal-uranium-plutonium.vercel.app/" ,
"https://class-76.github.io/" ,
"https://conselhoeconomico.com/games/?utm_source=google&utm_medium=cpc&utm_campaign=games-ru&gad_source=5&gad_campaignid=23153387466&gclid=EAIaIQobChMI7pqM2Zm4lAMV2vVMAh00-yxREAEYASAAEgLbKfD_BwE#google_vignette" ,
"https://unblocked-games76s.github.io/" ,
"https://murdergame.github.io/" ,
"https://unblockedgame66ez.github.io/#google_vignette" ,
"https://66ez.pages.dev/" ,
"https://macmetalcraft.co.uk/games/flappybird/index.html" ,
"https://www.trex-game.skipser.com/" ,
"https://www.cbeebies.com/play/n" ,
"https://www.chess.com/" ,
"https://patorjk.com/games/snake/" ,
"https://nebez.github.io/floppybird/" ,
"https://duckhuntjs.com/" ,
"https://theuselessweb.com/" ,
"https://lines.chromeexperiments.com/" ,
"https://hczhcz.github.io/Flappy-2048/" ,
"https://www.yuanfudao.com/tutor-ybc-course-api/chair/flappy2048/" ,
"https://hczhcz.github.io/Another-Flappy-2048/" ,
"https://factoryofdamned.base44.app" ,
"https://blooket.com" ,
"https://kahoot.it" ,
"https://mountjustin.github.io/" ,
"https://blooket.fandom.com/" ,
"https://yohohoio-pirate-battle-royale.fandom.com/wiki/YoHoHo.io_-_pirate_battle_royale_Wiki" ,
"https://web-dashers.github.io/" ,
"https://spacewaves.ca/" ,
"https://gamesdoodle.org/slope-2/" ,
"https://slopenexus.github.io/games/" ,
"https://classroom8.github.io/slope-tunnel/" ,
"https://mathiasgredal.github.io/Slope-Game/" ,
"https://github.com/KeysCreator/slope" ,
"https://coderguytedd.github.io/Slope-Game-Test/" ,
"https://github.com/unblockedgamess/unblockedgamess.github.io#" ,
"https://gitallgames.github.io/#google_vignette" ,
"https://hub-pro.github.io/games" ,
"https://github.com/unblockedgamess/unblockedgamess.github.io#" ,
"https://gamesdoodle.org/slope-2/" ,
"https://slopenexus.github.io/games/" ,
"https://classroom8.github.io/slope-tunnel/" ,
"https://mathiasgredal.github.io/Slope-Game/" ,
"https://github.com/KeysCreator/slope" ,
"https://coderguytedd.github.io/Slope-Game-Test/" ,
"https://factory-of-damned.base44.app" ,
"https://galaxyshooter.netlify.app" ,
"https://galaxyshooterlanpvpnotworking.netlify.app" ,
"https://hub-pro.github.io/games" ,
"https://gitallgames.github.io/#google_vignette" ,
"https://gamesdoodle.org/" ,
"https://0288007.github.io/metalnet/index/index.html" ,
"https://ncase.me/" ,
"https://web-dashers.github.io/" ,
"https://pshenok.github.io/server-survival/ " ,
"https://virtualvacation.us/guess" , 
"https://www.xbitlabs.com/cps-test/" ,
"https://gameunblocked-2025.github.io/projects/wbwwb/index.html" ,
"https://masonicgit.github.io/pacman/" ,
"https://www.itoady.com/dev/mario/ " ,
"https://gamesdoodle.org/slope-2/" ,
"https://slopenexus.github.io/games/" ,
"https://classroom8.github.io/slope-tunnel/" ,
"https://mathiasgredal.github.io/Slope-Game/" , 
"https://github.com/KeysCreator/slope" ,
"https://coderguytedd.github.io/Slope-Game-Test/" ,
"https://crossyroad105.netlify.app/" ,
"https://cross-road-eight.vercel.app/" ,
"https://ibrahim-sall.github.io/crossyroad/" ,
"https://mathrushgame.vercel.app/" ,
"https://games.base44.app/login?from_url=https%3A%2F%2Fgames.base44.app%2F" ,
"https://meteor-mine-dash.base44.app" ,
"https://web-dashers.github.io/" ,
"https://webecomewhatwebehold.github.io/" ,
"https://fnae.net/" ,
"https://github.com/he-is-talha/html-css-javascript-games" ,
"https://github.com/kaigani/HTML5-games-list" ,
"https://github.com/michelpereira/awesome-open-source-games" ,
"https://github.com/HTML5GameArchive" ,
"https://mining-the-ores.base44.app"

            
        ];

        function openMask(url) {
            const win = window.open('about:blank', '_blank');
            if (win) {
                const doc = win.document;
                doc.body.style.margin = '0';
                doc.body.style.height = '100vh';
                doc.body.style.overflow = 'hidden';
                const iframe = doc.createElement('iframe');
                iframe.style.width = '100%'; iframe.style.height = '100%'; iframe.style.border = 'none';
                iframe.src = url;
                doc.body.appendChild(iframe);
            }
        }

        function filterHub() {
            let q = document.getElementById('search').value.toLowerCase();
            document.querySelectorAll('.card').forEach(c => {
                c.style.display = c.innerText.toLowerCase().includes(q) ? "flex" : "none";
            });
        }

        window.onload = () => {
            const grid = document.getElementById('mainGrid');
            [...new Set(db)].forEach(url => {
                const card = document.createElement('div');
                card.className = 'card';
                let name = url.replace('https://', '').replace('www.', '').split(/[./?]/).filter(x => x.length > 2);
                card.innerHTML = `<strong>> ${name[0].toUpperCase()}</strong><small>${url}</small>`;
                card.onclick = () => openMask(url);
                grid.appendChild(card);
            });
        };

        "this updates every week"
    </script>
</body>
</html>
