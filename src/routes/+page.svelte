<script lang="ts">
  import { onMount } from 'svelte';
  
  let visible: Set<string> = new Set();
  let activeSection = 'hero';
  let lightsOn = false;
  let line1 = 'BERNHARD';
  let line2 = 'RODE';
  let showCursor = true;
  let activeLine = 2;
  let lightCount = 0;
  let countdown = 0;
  let gameActive = false;
  let gameBlocked = false;
  let highscores: number[] = [];
  let typingAnimation: any = null;
  
  onMount(() => {
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          visible = new Set([...visible, entry.target.id]);
          activeSection = entry.target.id;
        }
      });
    }, { threshold: 0.1 });
    
    document.querySelectorAll('[id^="fade-"]').forEach(el => observer.observe(el));
    document.querySelectorAll('section[id]').forEach(el => observer.observe(el));

    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.code === 'Space') {
        e.preventDefault();
        if (!e.repeat && !lightsOn && !gameBlocked) {
          lightsOn = true;
          if (!gameActive) {
            startGame();
          } else {
            lightCount++;
          }
        }
      }
    };

    const startGame = () => {
      gameActive = true;
      gameBlocked = false;
      countdown = 10;
      
      const timer = setInterval(() => {
        countdown--;
        if (countdown <= 0) {
          clearInterval(timer);
          endGame();
        }
      }, 1000);
    };

    const endGame = async () => {
      gameActive = false;
      gameBlocked = true;
      const score = lightCount;
      highscores = [score, ...highscores].sort((a, b) => b - a).slice(0, 5);
      
      line1 = '';
      line2 = '';
      activeLine = 1;
      await typeText(`YOU GOT ${score}`, (val) => line1 = val, 1);
      await typeText('POINTS', (val) => line2 = val, 2);
      await sleep(3000);
      await deleteText(line2, (val) => line2 = val, 2);
      await deleteText(line1, (val) => line1 = val, 1);
      
      lightCount = 0;
      line1 = 'BERNHARD';
      line2 = 'RODE';
      activeLine = 2;
      gameBlocked = false;
      animate();
    };

    const handleKeyUp = (e: KeyboardEvent) => {
      if (e.code === 'Space') {
        e.preventDefault();
        lightsOn = false;
      }
    };

    window.addEventListener('keydown', handleKeyDown);
    window.addEventListener('keyup', handleKeyUp);

    // Typing animation
    const sleep = (ms: number) => new Promise(resolve => setTimeout(resolve, ms));
    const random = (min: number, max: number) => Math.floor(Math.random() * (max - min + 1)) + min;
    
    const typeText = async (text: string, setter: (val: string) => void, lineNum: number) => {
      activeLine = lineNum;
      for (let i = 0; i <= text.length; i++) {
        setter(text.substring(0, i));
        await sleep(random(100, 300));
      }
    };
    
    const deleteText = async (currentText: string, setter: (val: string) => void, lineNum: number) => {
      activeLine = lineNum;
      for (let i = currentText.length; i >= 0; i--) {
        setter(currentText.substring(0, i));
        await sleep(random(50, 150));
      }
    };
    
    const animate = async () => {
      if (gameActive) return;
      await sleep(3000);
      if (gameActive) return;
      await deleteText(line2, (val) => line2 = val, 2);
      if (gameActive) return;
      await deleteText(line1, (val) => line1 = val, 1);
      if (gameActive) return;
      await typeText('EBBO', (val) => line1 = val, 1);
      if (gameActive) return;
      await typeText('DEV', (val) => line2 = val, 2);
      if (gameActive) return;
      await sleep(3000);
      if (gameActive) return;
      await deleteText(line2, (val) => line2 = val, 2);
      if (gameActive) return;
      await deleteText(line1, (val) => line1 = val, 1);
      if (gameActive) return;
      await typeText('BERNHARD', (val) => line1 = val, 1);
      if (gameActive) return;
      await typeText('RODE', (val) => line2 = val, 2);
      if (gameActive) return;
      animate();
    };
    
    animate();
    
    setInterval(() => showCursor = !showCursor, 500);

    return () => {
      window.removeEventListener('keydown', handleKeyDown);
      window.removeEventListener('keyup', handleKeyUp);
    };
  });
</script>

<svelte:head>
  <title>Ebbo | Cloud Architect & Builder</title>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Rajdhani:wght@300;400;500;700&display=swap" rel="stylesheet">
</svelte:head>

<div class="scene">
  <div class="sky"></div>
  <div class="stars stars-1"></div>
  <div class="stars stars-2"></div>
  <div class="stars stars-3"></div>
  <div class="shooting-star"></div>
  <div class="shooting-star"></div>
  <div class="shooting-star"></div>
  <div class="sun"></div>
  <div class="mountain" style="--mountain-base:10vw;--mountain-height:5vw;--mountain-offset:10vw;--mountain-tilt:-20deg"></div>
  <div class="mountain" style="--mountain-base:10vw;--mountain-height:5vw;--mountain-tilt:59deg;--mountain-offset:20vw;"></div>
  <div class="mountain" style="--mountain-base:8vw;--mountain-height:4vw;--mountain-offset:20vw;--mountain-tilt:-20deg"></div>
  <div class="mountain" style="--mountain-base:5vw;--mountain-height:5vw;--mountain-offset:-40vw;--mountain-tilt:-20deg"></div>
  <div class="mountain" style="--mountain-base:5vw;--mountain-height:5vw;--mountain-tilt:33deg;--mountain-offset:-35vw;"></div>
  <div class="mountain" style="--mountain-base:10vw;--mountain-height:5vw;--mountain-tilt:-20deg"></div>
  <div class="mountain" style="--mountain-base:10vw;--mountain-height:5vw;--mountain-tilt:59deg;--mountain-offset:10vw;"></div>
  <div class="mountain" style="--mountain-base:10vw;--mountain-height:10vw;--mountain-tilt:-33deg;--mountain-offset:-30vw;"></div>
  <div class="fog"></div>
  <div class="horizon"></div>
  <div class="grid"></div>
  <div class="road-line"></div>
  <div class="car-lights" class:lights-on={lightsOn}>
    <div class="light-left"></div>
    <div class="light-right"></div>
  </div>
  <div class="car" class:lights-on={lightsOn}></div>
  <div class="palm palm-left"></div>
  <div class="palm palm-right"></div>
  <div class="scanlines"></div>
</div>

<div class="dot-nav">
  <a href="#hero" class:active={activeSection === 'hero'}></a>
  <a href="#about" class:active={activeSection === 'about'}></a>
  <a href="#experience" class:active={activeSection === 'experience'}></a>
  <a href="#skills" class:active={activeSection === 'skills'}></a>
  <a href="#contact" class:active={activeSection === 'contact'}></a>
</div>

<section id="hero">
  {#if !gameActive}
  <h1 class="hero-title">
    <span class="first chrome">{line1}<span class="cursor cursor-cyan" class:blink={showCursor} class:active={activeLine === 1}>|</span></span><br>
    <span class="last chrome">{line2}<span class="cursor cursor-orange" class:blink={showCursor} class:active={activeLine === 2}>|</span></span>
  </h1>
  {/if}
  <p class="hero-subtitle">Global Solutions Architect @ AWS</p>
  <p class="tagline">"The best way to predict the future is to implement it"</p>
  {#if !gameBlocked}
  <div class="space-hint">Press SPACE to turn on lights</div>
  {/if}
  {#if countdown > 0}
  <div class="countdown-timer">
    <span class="timer-value">{countdown}</span>
  </div>
  {/if}
  {#if lightCount > 0}
  <div class="light-counter">
    <span class="counter-label">LIGHTS:</span>
    <span class="counter-value">{String(lightCount).padStart(4, '0')}</span>
  </div>
  {/if}
  {#if highscores.length > 0}
  <div class="highscore-list">
    <div class="highscore-title">HIGHSCORES</div>
    {#each highscores as score, i}
    <div class="highscore-item">
      <span class="rank">{i + 1}.</span>
      <span class="score">{String(score).padStart(4, '0')}</span>
    </div>
    {/each}
  </div>
  {/if}
</section>

<section id="about">
  <h2>About</h2>
  <div class="about-grid">
    <div class="card" id="fade-1" class:visible={visible.has('fade-1')}>
      <p>I build scalable solutions that deliver real impact. My strength is turning complex problems into systems that work reliably at scale.</p>
      <br>
      <p>I bring an entrepreneurial mindset to leadership, questioning assumptions, mentoring teams, and thriving where precision meets creativity.</p>
    </div>
    <div class="card" id="fade-2" class:visible={visible.has('fade-2')}>
      <h3>What Drives Me</h3>
      <ul>
        <li>Lifelong learning and knowledge sharing</li>
        <li>Entrepreneurial mindset—questioning the status quo</li>
        <li>Thriving in interdisciplinary teams</li>
        <li>Embracing transparency, honesty, and humor</li>
      </ul>
    </div>
  </div>
</section>

<section id="experience">
  <h2>Experience</h2>
  
  <div class="card" id="fade-3" class:visible={visible.has('fade-3')}>
    <div class="date">03/2025 → Present</div>
    <h3>Global Solutions Architect</h3>
    <div class="company">Amazon Web Services EMEA SARL</div>
    <ul>
      <li>Trusted technical advisor to strategic customers</li>
      <li>Design scalable, secure, cost-effective solutions</li>
      <li>Drive service adoption through workshops and POCs</li>
    </ul>
  </div>

  <div class="card" id="fade-4" class:visible={visible.has('fade-4')}>
    <div class="date">08/2022 → 01/2025</div>
    <h3>Senior Cloud Solution Engineer / Tech Lead</h3>
    <div class="company">Bosch Engineering GmbH</div>
    <ul>
      <li>Led cloud solutions architecture</li>
      <li>Reduced deployment setup from days to minutes</li>
      <li>Cost optimization: $2.5k to $500/month</li>
    </ul>
  </div>
</section>

<section id="skills">
  <h2>Skills</h2>
  <div class="skills-grid">
    <div class="card" id="fade-5" class:visible={visible.has('fade-5')}>
      <h4>Cloud & DevOps</h4>
      <div class="skill-tags">
        <span class="skill-tag">AWS</span>
        <span class="skill-tag">Azure</span>
        <span class="skill-tag">OpenShift</span>
        <span class="skill-tag">GitHub Actions</span>
      </div>
    </div>
    <div class="card" id="fade-6" class:visible={visible.has('fade-6')}>
      <h4>Development</h4>
      <div class="skill-tags">
        <span class="skill-tag">TypeScript</span>
        <span class="skill-tag">React</span>
        <span class="skill-tag">Node.js</span>
        <span class="skill-tag">Python</span>
      </div>
    </div>
  </div>
</section>

<section id="contact">
  <h2>Contact</h2>
  <div class="contact-grid" id="fade-7" class:visible={visible.has('fade-7')}>
    <div class="contact-item">
      <span>📧</span>
      <a href="mailto:mail@bernhardrode.de">mail@bernhardrode.de</a>
    </div>
    <div class="contact-item">
      <span>📱</span>
      <a href="tel:+491715629409">+49 171 5629409</a>
    </div>
  </div>
  <div class="social-links">
    <a href="https://twitter.com/ebbo" target="_blank">Twitter</a>
    <a href="https://github.com/bernhardrode" target="_blank">GitHub</a>
    <a href="https://linkedin.com/in/bernhardrode" target="_blank">LinkedIn</a>
  </div>
</section>

<footer>
  <p>© 2025 Bernhard Rode • Built with 💜 and OutRun Vibes</p>
</footer>

<style>
  :global(body) {
    margin: 0;
    font-family: 'Rajdhani', sans-serif;
    background: #000;
    color: #fff;
    overflow-x: hidden;
    scroll-snap-type: y mandatory;
    overflow-y: scroll;
    height: 100vh;
  }

  .scene {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
    overflow: hidden;
    perspective: 120px;
  }

  .sky {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(180deg, #00d4ff 0%, #fe8019 40%, #ff6b35 70%, #ffd700 100%);
  }

  .stars {
    position: absolute;
    top: 0;
    left: 0;
    width: 1px;
    height: 1px;
  }

  .stars-1 {
    background: #fff;
    box-shadow: 
      10vw 5vh 1px 0.5px #fff, 20vw 15vh 1px 0.5px #fff, 30vw 8vh 1px 0.5px #fff, 40vw 25vh 1px 0.5px #fff,
      50vw 12vh 1px 0.5px #fff, 60vw 30vh 1px 0.5px #fff, 70vw 18vh 1px 0.5px #fff, 80vw 22vh 1px 0.5px #fff,
      90vw 10vh 1px 0.5px #fff, 15vw 35vh 1px 0.5px #fff, 25vw 28vh 1px 0.5px #fff, 35vw 20vh 1px 0.5px #fff,
      45vw 32vh 1px 0.5px #fff, 55vw 16vh 1px 0.5px #fff, 65vw 24vh 1px 0.5px #fff, 75vw 38vh 1px 0.5px #fff,
      85vw 14vh 1px 0.5px #fff, 95vw 26vh 1px 0.5px #fff, 5vw 33vh 1px 0.5px #fff, 12vw 19vh 1px 0.5px #fff,
      22vw 37vh 1px 0.5px #fff, 32vw 11vh 1px 0.5px #fff, 42vw 29vh 1px 0.5px #fff, 52vw 21vh 1px 0.5px #fff,
      62vw 36vh 1px 0.5px #fff;
    animation: blink1 3s ease-in-out infinite;
  }

  .stars-2 {
    background: #fff;
    box-shadow: 
      48vw 39vh 1.5px 0.5px #fff, 58vw 7vh 1.5px 0.5px #fff, 68vw 28vh 1.5px 0.5px #fff, 78vw 15vh 1.5px 0.5px #fff,
      88vw 35vh 1.5px 0.5px #fff, 3vw 20vh 1.5px 0.5px #fff, 13vw 12vh 1.5px 0.5px #fff, 23vw 30vh 1.5px 0.5px #fff,
      33vw 25vh 1.5px 0.5px #fff, 43vw 8vh 1.5px 0.5px #fff, 53vw 33vh 1.5px 0.5px #fff, 63vw 18vh 1.5px 0.5px #fff,
      73vw 27vh 1.5px 0.5px #fff, 83vw 11vh 1.5px 0.5px #fff, 93vw 22vh 1.5px 0.5px #fff, 7vw 36vh 1.5px 0.5px #fff,
      17vw 14vh 1.5px 0.5px #fff, 27vw 29vh 1.5px 0.5px #fff, 37vw 19vh 1.5px 0.5px #fff, 47vw 32vh 1.5px 0.5px #fff,
      57vw 10vh 1.5px 0.5px #fff, 67vw 24vh 1.5px 0.5px #fff;
    animation: blink2 2s ease-in-out infinite;
  }

  .stars-3 {
    background: #fff;
    box-shadow: 
      51vw 17vh 1px 0.5px #fff, 61vw 34vh 1px 0.5px #fff, 71vw 9vh 1px 0.5px #fff, 81vw 23vh 1px 0.5px #fff,
      91vw 28vh 1px 0.5px #fff, 6vw 15vh 1px 0.5px #fff, 16vw 32vh 1px 0.5px #fff, 26vw 11vh 1px 0.5px #fff,
      36vw 27vh 1px 0.5px #fff, 46vw 20vh 1px 0.5px #fff, 56vw 35vh 1px 0.5px #fff, 66vw 14vh 1px 0.5px #fff,
      76vw 29vh 1px 0.5px #fff, 86vw 19vh 1px 0.5px #fff, 96vw 25vh 1px 0.5px #fff, 4vw 37vh 1px 0.5px #fff,
      14vw 10vh 1px 0.5px #fff, 24vw 22vh 1px 0.5px #fff, 34vw 31vh 1px 0.5px #fff, 44vw 16vh 1px 0.5px #fff,
      54vw 26vh 1px 0.5px #fff, 64vw 12vh 1px 0.5px #fff, 74vw 33vh 1px 0.5px #fff, 84vw 21vh 1px 0.5px #fff,
      94vw 30vh 1px 0.5px #fff, 9vw 18vh 1px 0.5px #fff, 19vw 34vh 1px 0.5px #fff, 29vw 24vh 1px 0.5px #fff;
    animation: blink3 4s ease-in-out infinite;
  }

  @keyframes blink1 {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.5; }
  }

  @keyframes blink2 {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.6; }
  }

  @keyframes blink3 {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.4; }
  }

  .shooting-star {
    position: absolute;
    top: 0;
    left: 0;
    width: 2px;
    height: 2px;
    background: #fff;
    border-radius: 50%;
    box-shadow: 0 0 4px 2px #fff;
    opacity: 0;
  }

  .shooting-star:nth-child(4) {
    animation: shoot1 8s ease-in infinite;
  }

  .shooting-star:nth-child(5) {
    animation: shoot2 12s ease-in infinite;
  }

  .shooting-star:nth-child(6) {
    animation: shoot3 15s ease-in infinite;
  }

  @keyframes shoot1 {
    0% { top: 5vh; left: 80vw; opacity: 0; }
    5% { opacity: 1; }
    15% { top: 25vh; left: 20vw; opacity: 0; }
    100% { top: 25vh; left: 20vw; opacity: 0; }
  }

  @keyframes shoot2 {
    0% { top: 10vh; left: 90vw; opacity: 0; }
    5% { opacity: 1; }
    15% { top: 30vh; left: 30vw; opacity: 0; }
    100% { top: 30vh; left: 30vw; opacity: 0; }
  }

  @keyframes shoot3 {
    0% { top: 2vh; left: 70vw; opacity: 0; }
    5% { opacity: 1; }
    15% { top: 22vh; left: 10vw; opacity: 0; }
    100% { top: 22vh; left: 10vw; opacity: 0; }
  }

  .sun {
    position: absolute;
    bottom: 42%;
    left: 50%;
    transform: translateX(-50%);
    width: 300px;
    height: 300px;
    border-radius: 50%;
    background: linear-gradient(180deg, #fff9c4 0%, #ffeb3b 30%, #ffa726 70%, #ff9800 100%);
    box-shadow: 0 0 100px #ffa726;
  }

  .sun::after {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border-radius: 50%;
    background: repeating-linear-gradient(0deg, transparent 0%, transparent 8%, rgba(204,51,0,0.6) 8%, rgba(204,51,0,0.6) 12%);
  }

  .mountain {
    position: absolute;
    bottom: 35%;
    left: calc(50% + var(--mountain-offset, 0px));
    border-left: calc(var(--mountain-base) / 2) solid transparent;
    border-bottom: var(--mountain-height, 100px) solid #00ff41;
    border-top: 0px solid transparent;
    border-right: calc(var(--mountain-base, 100px) / 2) solid transparent;
    transform-origin: bottom;
    transform: skewX(var(--mountain-tilt, 0deg));
    opacity: 0.6;
  }

  .mountain:after {
    content: '';
    border-left: calc(var(--mountain-base) / 2) solid transparent;
    border-bottom: var(--mountain-height, 100px) solid #006400;
    border-top: 0px solid transparent;
    border-right: calc(var(--mountain-base, 100px) / 2) solid transparent;
    transform: translate(-50%) scale(0.98);
    position: absolute;
    left: 0;
    top: 0;
  }

  .fog {
    position: absolute;
    bottom: 30%;
    left: 0;
    width: 100%;
    height: 20%;
    background: linear-gradient(180deg, transparent 0%, rgba(0, 212, 255, 0.1) 50%, transparent 100%);
    pointer-events: none;
  }

  .scanlines {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-image: repeating-linear-gradient(rgba(0,0,0,0.3) 0, transparent 1px, transparent 2px, rgba(0,0,0,0.3) 3px);
    pointer-events: none;
    z-index: 9999;
  }

  .horizon {
    position: absolute;
    bottom: 40%;
    left: 0;
    width: 100%;
    height: 3px;
    background: #fe8019;
    box-shadow: 0 0 20px #fe8019;
    z-index: 1;
  }

  .horizon::after {
    content: '';
    position: absolute;
    top: 3px;
    left: 0;
    width: 100%;
    height: 100vh;
    background: linear-gradient(180deg, #b8bb26 0%, #fe8019 100%);
  }

  .grid {
    position: absolute;
    bottom: 0;
    left: 50%;
    transform: translateX(-50%) rotateX(70deg);
    width: 400px;
    height: 100%;
    max-width: 400px;
    z-index: 2;
  }

  .grid::before {
    content: '';
    position: absolute;
    width: 9999px;
    height: 100%;
    background: linear-gradient(180deg, #00ff41 0%, #006400 100%);
    left: 50%;
    transform: translateX(-50%);
    z-index: -1;
  }

  .grid::after {
    content: '';
    position: absolute;
    background: linear-gradient(180deg, #2a2a2a 0%, #000 100%);
    border: 0 solid #ffeb3b;
    border-left-width: 40px;
    border-right-width: 40px;
    box-sizing: border-box;
    width: 100%;
    height: 100%;
    z-index: 1;
    box-shadow: 0 0 40px rgba(255, 235, 59, 0.5);
  }

  .road-line {
    position: absolute;
    bottom: 0;
    left: 50%;
    transform: translateX(-50%) rotateX(70deg);
    width: 9999px;
    height: 100%;
    background: repeating-linear-gradient(0deg, rgba(0,212,255,0) 0px, rgba(0,212,255,0) 50px, rgba(0,212,255,.1) 50px, rgba(0,212,255,.1) 100px);
    background-size: 100% 100px;
    z-index: 2;
    animation: stripes 1s linear infinite;
  }

  @keyframes stripes {
    0% { background-position: 0 0; }
    100% { background-position: 0 100px; }
  }

  .car {
    position: absolute;
    bottom: 100px;
    left: calc(45% + 10px);
    transform: translateX(-50%) scale(4.8);
    background: url(ferrari_straight1.svg) no-repeat;
    height: 43px;
    width: 80px;
    filter: brightness(100%);
    image-rendering: pixelated;
    z-index: 1000;
    animation: car-move 30s linear infinite, bump 6s linear infinite;
  }

  .car.lights-on {
    filter: brightness(150%) drop-shadow(0 0 20px #ffeb3b);
  }

  .car-lights {
    position: absolute;
    bottom: 100px;
    left: calc(45% + 10px);
    transform: translateX(-50%) scale(4.8);
    height: 43px;
    width: 80px;
    z-index: 900;
    animation: car-move 30s linear infinite, bump 6s linear infinite;
    pointer-events: none;
  }

  .car-lights .light-left,
  .car-lights .light-right {
    position: absolute;
    left: -40px;
    top: -40px;
    width: 150px;
    height: 60px;
    background: linear-gradient(180deg, transparent 0%, rgba(255, 235, 59, 0.6) 100%);
    opacity: 0;
    transition: opacity 0.1s;
  }

  .car-lights.lights-on .light-left,
  .car-lights.lights-on .light-right {
    opacity: 1;
  }

  .car-lights .light-left {
    clip-path: polygon(35% 100%, 25% 0%, 45% 0%);
  }

  .car-lights .light-right {
    clip-path: polygon(65% 100%, 55% 0%, 75% 0%);
  }

  @keyframes car-move {
    0% {transform: translateX(-75%) scale(4.8);}
    40% {transform: translateX(28%) scale(4.8);}
    100% {transform: translateX(-75%) scale(4.8);}
  }

  @keyframes bump {
    0% {bottom: 100px;}
    98% {bottom: 100px;}
    99% {bottom: 102px;}
    100% {bottom: 100px;}
  }

  .palm {
    position: absolute;
    bottom: 10%;
    width: 80px;
    height: 200px;
    background: linear-gradient(180deg, transparent 0%, transparent 60%, #4caf50 60%, #2e7d32 100%);
  }

  .palm::before {
    content: '';
    position: absolute;
    top: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 100px;
    height: 100px;
    background: radial-gradient(ellipse, #8bc34a 0%, transparent 70%);
    clip-path: polygon(50% 50%, 0% 0%, 20% 40%, 50% 50%, 80% 40%, 100% 0%);
  }

  .palm-left {
    left: 5%;
    transform: rotate(-10deg);
  }

  .palm-right {
    right: 5%;
    transform: rotate(10deg);
  }

  .dot-nav {
    position: fixed;
    right: 2rem;
    top: 50%;
    transform: translateY(-50%);
    z-index: 1000;
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }

  .dot-nav a {
    width: 12px;
    height: 12px;
    border-radius: 50%;
    border: 2px solid #00d4ff;
    background: transparent;
    transition: all 0.3s;
    display: block;
  }

  .dot-nav a:hover {
    background: #fe8019;
    border-color: #fe8019;
    box-shadow: 0 0 20px #fe8019;
  }

  .dot-nav a.active {
    background: #00d4ff;
    box-shadow: 0 0 20px #00d4ff;
  }

  section {
    min-height: 100vh;
    padding: 6rem 2rem;
    max-width: 1200px;
    margin: 0 auto;
    scroll-snap-align: start;
  }

  h1, h2, h3 {
    font-family: 'Orbitron', monospace;
    text-transform: uppercase;
  }

  h2 {
    font-size: 2.5rem;
    margin-bottom: 2rem;
    color: #ffeb3b;
    text-shadow: 0 0 30px #ffeb3b;
  }

  #hero {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    padding-top: 7rem;
  }

  .hero-title {
    font-size: clamp(3rem, 10vw, 8rem);
    font-weight: 900;
    line-height: 1;
    margin-bottom: 1rem;
  }

  .first {
    font-family: 'Orbitron', monospace;
    font-weight: 900;
    background-image: linear-gradient(#00d4ff 0%, #00d4ff 50%, #00cc33 51%, #00ff41 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    -webkit-text-stroke-width: 1px;
    -webkit-text-stroke-color: rgba(255,255,255,0.4);
    position: relative;
    filter: drop-shadow(-1px -1px 1px #00d4ff) drop-shadow(-2px -2px 1px #00d4ff) drop-shadow(1px 1px 1px #000) drop-shadow(0 0 5px rgba(0, 212, 255, 1));
  }

  .last {
    font-family: 'Orbitron', monospace;
    font-weight: 900;
    background-image: linear-gradient(#ff9800 0%, #ffeb3b 50%, #ff9800 51%, #ffa726 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    -webkit-text-stroke-width: 1px;
    -webkit-text-stroke-color: rgba(255,255,255,0.4);
    position: relative;
    filter: drop-shadow(-1px -1px 1px #ff9800) drop-shadow(-2px -2px 1px #ff9800) drop-shadow(1px 1px 1px #000) drop-shadow(0 0 5px rgba(255, 152, 0, 1));
  }

  .cursor {
    opacity: 0;
    transition: opacity 0.1s;
  }

  .cursor.active {
    opacity: 1;
  }

  .cursor.active.blink {
    opacity: 0;
  }

  .cursor-cyan {
    color: #00d4ff;
  }

  .cursor-orange {
    color: #ffeb3b;
  }

  .hero-subtitle {
    font-size: 1.5rem;
    color: #ffeb3b;
    text-shadow: 0 0 20px #ffeb3b;
    margin-bottom: 2rem;
    letter-spacing: 4px;
  }

  .tagline {
    font-size: 1.2rem;
    max-width: 600px;
  }

  .space-hint {
    margin-top: 2rem;
    font-family: 'Orbitron', monospace;
    font-size: 0.9rem;
    color: #00d4ff;
    text-transform: uppercase;
    letter-spacing: 2px;
    animation: pulse 2s ease-in-out infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 0.6; }
    50% { opacity: 1; }
  }

  .light-counter {
    position: fixed;
    top: 2rem;
    right: 2rem;
    font-family: 'Orbitron', monospace;
    background: rgba(0, 0, 0, 0.8);
    border: 2px solid #ffeb3b;
    padding: 1rem 1.5rem;
    box-shadow: 0 0 20px rgba(255, 235, 59, 0.5);
    z-index: 1001;
  }

  .counter-label {
    color: #ffeb3b;
    font-size: 0.8rem;
    letter-spacing: 2px;
    display: block;
    margin-bottom: 0.5rem;
  }

  .counter-value {
    color: #00ff41;
    font-size: 2rem;
    font-weight: 900;
    text-shadow: 0 0 10px #00ff41;
    display: block;
    font-variant-numeric: tabular-nums;
    width: 4.5ch;
  }

  .countdown-timer {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-family: 'Orbitron', monospace;
    z-index: 10000;
    pointer-events: none;
  }

  .timer-value {
    font-size: 10rem;
    font-weight: 900;
    color: #fe8019;
    text-shadow: 0 0 40px #fe8019, 0 0 80px #fe8019;
    animation: countdown-pulse 1s ease-in-out infinite;
  }

  @keyframes countdown-pulse {
    0%, 100% { transform: scale(1); opacity: 1; }
    50% { transform: scale(1.1); opacity: 0.8; }
  }

  .highscore-list {
    position: fixed;
    bottom: 2rem;
    right: 2rem;
    font-family: 'Orbitron', monospace;
    background: rgba(0, 0, 0, 0.8);
    border: 2px solid #00d4ff;
    padding: 1rem;
    box-shadow: 0 0 20px rgba(0, 212, 255, 0.5);
    z-index: 1001;
    min-width: 150px;
  }

  .highscore-title {
    color: #00d4ff;
    font-size: 0.8rem;
    letter-spacing: 2px;
    margin-bottom: 0.5rem;
    text-align: center;
  }

  .highscore-item {
    display: flex;
    justify-content: space-between;
    gap: 1rem;
    color: #ffeb3b;
    font-size: 1rem;
    margin: 0.3rem 0;
  }

  .rank {
    color: #ff9800;
  }

  .score {
    font-variant-numeric: tabular-nums;
  }

  .neon-btn {
    display: inline-block;
    margin-top: 2rem;
    padding: 1rem 2.5rem;
    font-family: 'Orbitron', monospace;
    text-transform: uppercase;
    letter-spacing: 3px;
    color: #00d4ff;
    background: transparent;
    border: 2px solid #00d4ff;
    cursor: pointer;
    transition: all 0.3s;
    text-decoration: none;
  }

  .neon-btn:hover {
    color: #000;
    background: #00d4ff;
    box-shadow: 0 0 30px #00d4ff;
  }

  .about-grid, .skills-grid, .contact-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 2rem;
  }

  .card {
    background: rgba(0, 0, 0, 0.7);
    border: 2px solid #00ff41;
    padding: 2rem;
    transition: all 0.3s;
    opacity: 0;
    transform: translateY(30px);
  }

  .card.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .card:hover {
    border-color: #ffeb3b;
    box-shadow: 0 0 30px rgba(255, 235, 59, 0.5);
    transform: translateY(-5px);
  }

  .card h3 {
    color: #fe8019;
    font-size: 1.3rem;
    margin-bottom: 0.5rem;
  }

  .date {
    color: #00d4ff;
    font-size: 0.9rem;
    margin-bottom: 1rem;
    font-family: 'Orbitron', monospace;
  }

  .company {
    color: #ffeb3b;
    margin-bottom: 1rem;
  }

  .card ul {
    list-style: none;
    padding: 0;
  }

  .card li {
    padding-left: 1.5rem;
    position: relative;
    margin-bottom: 0.5rem;
  }

  .card li::before {
    content: '▸';
    position: absolute;
    left: 0;
    color: #fe8019;
  }

  h4 {
    color: #ffeb3b;
    font-family: 'Orbitron', monospace;
    margin-bottom: 0.5rem;
  }

  .skill-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
  }

  .skill-tag {
    background: rgba(0, 255, 65, 0.2);
    border: 1px solid #00ff41;
    padding: 0.3rem 0.8rem;
    font-size: 0.85rem;
    color: #00ff41;
    transition: all 0.3s;
  }

  .skill-tag:hover {
    background: #ffeb3b;
    color: #000;
  }

  .contact-item {
    display: flex;
    align-items: center;
    gap: 1rem;
    padding: 1rem;
    border: 2px solid #00ff41;
    transition: all 0.3s;
  }

  .contact-item:hover {
    border-color: #ffeb3b;
    box-shadow: 0 0 20px rgba(255, 235, 59, 0.3);
  }

  .contact-item a {
    color: #fff;
    text-decoration: none;
  }

  .social-links {
    display: flex;
    gap: 1.5rem;
    margin-top: 2rem;
    justify-content: center;
  }

  .social-links a {
    color: #fff;
    padding: 0.8rem 1.5rem;
    border: 2px solid #00ff41;
    text-decoration: none;
    font-family: 'Orbitron', monospace;
    transition: all 0.3s;
  }

  .social-links a:hover {
    background: #fe8019;
    color: #000;
    box-shadow: 0 0 30px #fe8019;
  }

  footer {
    text-align: center;
    padding: 2rem;
    border-top: 2px solid #00ff41;
    font-family: 'Orbitron', monospace;
    color: #ffeb3b;
  }

  @media (max-width: 768px) {
    .hero-title { font-size: 3rem; }
    section { padding: 4rem 1rem; }
    .sun { width: 200px; height: 200px; }
    .palm { display: none; }
    .dot-nav { right: 1rem; }
  }
</style>
