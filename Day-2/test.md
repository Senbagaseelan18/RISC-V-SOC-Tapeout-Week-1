<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Week 1 Day 2 - Timing Libraries & Synthesis</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
            background: linear-gradient(135deg, #0f0f23 0%, #1a1a3e 25%, #2d1b69 50%, #8b5cf6 100%);
            color: white;
            line-height: 1.6;
            overflow-x: hidden;
        }

        .hero-section {
            text-align: center;
            padding: 4rem 2rem;
            position: relative;
            background: radial-gradient(ellipse at top, rgba(139, 92, 246, 0.3) 0%, transparent 70%);
        }

        .hero-title {
            font-size: clamp(2.5rem, 5vw, 4rem);
            font-weight: 900;
            background: linear-gradient(45deg, #60a5fa, #a78bfa, #f472b6);
            background-size: 200% 200%;
            -webkit-background-clip: text;
            background-clip: text;
            -webkit-text-fill-color: transparent;
            animation: gradientShift 3s ease-in-out infinite;
            margin-bottom: 1rem;
        }

        .hero-subtitle {
            font-size: 1.5rem;
            color: #d1d5db;
            margin-bottom: 3rem;
            opacity: 0.9;
        }

        @keyframes gradientShift {
            0%, 100% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
        }

        .floating-particles {
            position: absolute;
            width: 100%;
            height: 100%;
            overflow: hidden;
            z-index: -1;
        }

        .particle {
            position: absolute;
            background: rgba(139, 92, 246, 0.6);
            border-radius: 50%;
            animation: float 6s ease-in-out infinite;
        }

        @keyframes float {
            0%, 100% { transform: translateY(0px) rotate(0deg); }
            50% { transform: translateY(-20px) rotate(180deg); }
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 0 2rem;
        }

        .toc-card {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(20px);
            border-radius: 20px;
            padding: 2rem;
            margin: 2rem 0;
            border: 1px solid rgba(255, 255, 255, 0.2);
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .toc-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 30px 60px rgba(139, 92, 246, 0.2);
        }

        .section {
            margin: 4rem 0;
            padding: 2rem;
            background: rgba(255, 255, 255, 0.05);
            border-radius: 20px;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            position: relative;
            overflow: hidden;
        }

        .section::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 3px;
            background: linear-gradient(90deg, #60a5fa, #a78bfa, #f472b6);
            animation: shimmer 2s linear infinite;
        }

        @keyframes shimmer {
            0% { transform: translateX(-100%); }
            100% { transform: translateX(100%); }
        }

        .section-title {
            font-size: 2.5rem;
            font-weight: 800;
            margin-bottom: 2rem;
            background: linear-gradient(45deg, #fbbf24, #f59e0b);
            -webkit-background-clip: text;
            background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .subsection-title {
            font-size: 1.8rem;
            font-weight: 700;
            margin: 2rem 0 1rem 0;
            color: #60a5fa;
            position: relative;
            display: inline-block;
        }

        .subsection-title::after {
            content: '';
            position: absolute;
            bottom: -5px;
            left: 0;
            width: 100%;
            height: 2px;
            background: linear-gradient(90deg, #60a5fa, transparent);
            animation: underlineGrow 1s ease-out;
        }

        @keyframes underlineGrow {
            from { width: 0; }
            to { width: 100%; }
        }

        .info-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 2rem;
            margin: 2rem 0;
        }

        .info-card {
            background: linear-gradient(135deg, rgba(139, 92, 246, 0.1), rgba(59, 130, 246, 0.1));
            border-radius: 15px;
            padding: 1.5rem;
            border: 1px solid rgba(139, 92, 246, 0.3);
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .info-card::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: conic-gradient(from 0deg, transparent, rgba(139, 92, 246, 0.1), transparent);
            animation: rotate 4s linear infinite;
            opacity: 0;
            transition: opacity 0.3s ease;
        }

        .info-card:hover::before {
            opacity: 1;
        }

        .info-card:hover {
            transform: translateY(-10px) scale(1.02);
            box-shadow: 0 20px 40px rgba(139, 92, 246, 0.3);
        }

        @keyframes rotate {
            from { transform: rotate(0deg); }
            to { transform: rotate(360deg); }
        }

        .table-wrapper {
            overflow-x: auto;
            margin: 2rem 0;
            border-radius: 15px;
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(10px);
        }

        .modern-table {
            width: 100%;
            border-collapse: collapse;
            background: transparent;
        }

        .modern-table th {
            background: linear-gradient(135deg, #8b5cf6, #a78bfa);
            color: white;
            padding: 1.5rem 1rem;
            text-align: left;
            font-weight: 700;
            border: none;
        }

        .modern-table td {
            padding: 1.5rem 1rem;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
            transition: background 0.3s ease;
        }

        .modern-table tr:hover td {
            background: rgba(139, 92, 246, 0.1);
        }

        .code-block {
            background: linear-gradient(135deg, #1f2937, #374151);
            border-radius: 15px;
            padding: 2rem;
            margin: 2rem 0;
            border-left: 4px solid #8b5cf6;
            position: relative;
            overflow: hidden;
        }

        .code-block::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(45deg, transparent 48%, rgba(139, 92, 246, 0.1) 50%, transparent 52%);
            background-size: 20px 20px;
            animation: codePattern 10s linear infinite;
            opacity: 0.5;
        }

        @keyframes codePattern {
            0% { background-position: 0 0; }
            100% { background-position: 20px 20px; }
        }

        .code-block code {
            color: #e5e7eb;
            font-family: 'Fira Code', monospace;
            font-size: 0.95rem;
            line-height: 1.8;
            position: relative;
            z-index: 1;
        }

        .emoji-large {
            font-size: 2rem;
            margin-right: 1rem;
            display: inline-block;
            animation: bounce 2s infinite;
        }

        @keyframes bounce {
            0%, 20%, 50%, 80%, 100% { transform: translateY(0); }
            40% { transform: translateY(-10px); }
            60% { transform: translateY(-5px); }
        }

        .highlight-box {
            background: linear-gradient(135deg, rgba(251, 191, 36, 0.1), rgba(245, 158, 11, 0.1));
            border: 2px solid #fbbf24;
            border-radius: 15px;
            padding: 1.5rem;
            margin: 1.5rem 0;
            position: relative;
        }

        .highlight-box::before {
            content: '💡';
            position: absolute;
            top: -10px;
            left: 20px;
            background: #fbbf24;
            color: #1f2937;
            padding: 5px 10px;
            border-radius: 50%;
            font-size: 1.2rem;
        }

        .nav-dots {
            position: fixed;
            right: 2rem;
            top: 50%;
            transform: translateY(-50%);
            display: flex;
            flex-direction: column;
            gap: 1rem;
            z-index: 100;
        }

        .nav-dot {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.3);
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .nav-dot.active {
            background: #8b5cf6;
            transform: scale(1.5);
            box-shadow: 0 0 20px rgba(139, 92, 246, 0.5);
        }

        .progress-bar {
            position: fixed;
            top: 0;
            left: 0;
            width: 0%;
            height: 3px;
            background: linear-gradient(90deg, #60a5fa, #a78bfa, #f472b6);
            z-index: 1000;
            transition: width 0.3s ease;
        }

        @media (max-width: 768px) {
            .hero-title {
                font-size: 2.5rem;
            }
            .section-title {
                font-size: 2rem;
            }
            .nav-dots {
                display: none;
            }
            .info-grid {
                grid-template-columns: 1fr;
            }
        }

        .fade-in {
            opacity: 0;
            transform: translateY(30px);
            animation: fadeInUp 0.8s ease forwards;
        }

        @keyframes fadeInUp {
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .glow-text {
            text-shadow: 0 0 20px rgba(139, 92, 246, 0.5);
        }

        .tech-badge {
            display: inline-block;
            background: linear-gradient(45deg, #8b5cf6, #a78bfa);
            color: white;
            padding: 0.5rem 1rem;
            border-radius: 25px;
            font-size: 0.9rem;
            font-weight: 600;
            margin: 0.25rem;
            box-shadow: 0 4px 15px rgba(139, 92, 246, 0.3);
            transition: transform 0.3s ease;
        }

        .tech-badge:hover {
            transform: translateY(-3px) scale(1.05);
        }
    </style>
</head>
<body>
    <div class="progress-bar" id="progressBar"></div>
    
    <div class="nav-dots">
        <div class="nav-dot active" data-section="0"></div>
        <div class="nav-dot" data-section="1"></div>
        <div class="nav-dot" data-section="2"></div>
        <div class="nav-dot" data-section="3"></div>
    </div>

    <div class="floating-particles">
        <!-- Particles will be generated by JavaScript -->
    </div>

    <section class="hero-section">
        <div class="container">
            <h1 class="hero-title glow-text">
                <span class="emoji-large">🌟</span>
                Week 1 — Day 2
            </h1>
            <p class="hero-subtitle">
                Timing Libraries · Hierarchical vs Flat Synthesis · Flop Coding Styles
            </p>
            <div style="margin-top: 2rem;">
                <span class="tech-badge">Digital Design</span>
                <span class="tech-badge">VLSI</span>
                <span class="tech-badge">Synthesis</span>
                <span class="tech-badge">Liberty</span>
            </div>
        </div>
    </section>

    <div class="container">
        <div class="toc-card fade-in">
            <h2 class="section-title">📖 Table of Contents</h2>
            <div class="info-grid">
                <div class="info-card">
                    <h3><span class="emoji-large">⏱️</span>Introduction to Timing Libraries</h3>
                    <p>Library Structure, PVT Corners, SKY130 Examples, AND Gate Flavors</p>
                </div>
                <div class="info-card">
                    <h3><span class="emoji-large">🏗️</span>Hierarchical vs Flat Synthesis</h3>
                    <p>Concepts and Lab Experiments (Parts 1 & 2)</p>
                </div>
                <div class="info-card">
                    <h3><span class="emoji-large">🔁</span>Flop Coding Styles</h3>
                    <p>Different Styles, Synthesis & Simulation Labs (Parts 1–5)</p>
                </div>
            </div>
        </div>

        <section class="section fade-in" id="section-1">
            <h2 class="section-title">⏱️ 1. Introduction to Timing Libraries (.lib)</h2>
            
            <div class="highlight-box">
                A <strong>timing library (.lib)</strong> is the <em>dictionary of standard cells</em> for synthesis.
                It tells the tool <strong>how each logic gate behaves</strong> in terms of <strong>function, delay, power, and area</strong>.
            </div>

            <div class="subsection-title">🔬 Library Structure</div>
            <p>Inside a <code>.lib</code>, each <strong>cell</strong> is described with:</p>
            <div class="info-grid">
                <div class="info-card">
                    <h4>📊 Logic Equation</h4>
                    <p>Boolean function definition</p>
                </div>
                <div class="info-card">
                    <h4>🔌 Pins & Directions</h4>
                    <p>Input/output specifications</p>
                </div>
                <div class="info-card">
                    <h4>⏰ Timing Data</h4>
                    <p>Setup, hold, propagation delay</p>
                </div>
                <div class="info-card">
                    <h4>🔋 Power Models</h4>
                    <p>Leakage & dynamic switching</p>
                </div>
            </div>

            <div class="subsection-title">🌡️ PVT Corners</div>
            <p>The behavior of silicon changes with <strong>Process, Voltage, Temperature (PVT)</strong>:</p>
            
            <div class="table-wrapper">
                <table class="modern-table">
                    <thead>
                        <tr>
                            <th>Corner</th>
                            <th>Voltage</th>
                            <th>Temperature</th>
                            <th>Process</th>
                            <th>Behavior</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td><strong>SS</strong></td>
                            <td>1.60 V</td>
                            <td>125 °C</td>
                            <td>Slow-Slow</td>
                            <td>Worst Delay, Low Power</td>
                        </tr>
                        <tr>
                            <td><strong>TT</strong></td>
                            <td>1.80 V</td>
                            <td>25 °C</td>
                            <td>Typical</td>
                            <td>Balanced</td>
                        </tr>
                        <tr>
                            <td><strong>FF</strong></td>
                            <td>1.95 V</td>
                            <td>0 °C</td>
                            <td>Fast-Fast</td>
                            <td>Best Delay, High Power</td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="subsection-title">📂 Example Library: SKY130</div>
            <div class="code-block">
                <strong>Filename:</strong><br>
                <code>sky130_fd_sc_hd__tt_025C_1v80.lib</code>
            </div>

            <div class="table-wrapper">
                <table class="modern-table">
                    <thead>
                        <tr>
                            <th>Part</th>
                            <th>Meaning</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td><code>sky130_fd_sc_hd</code></td>
                            <td>SkyWater 130nm High-Density Cell Library</td>
                        </tr>
                        <tr>
                            <td><code>tt</code></td>
                            <td>Typical process corner</td>
                        </tr>
                        <tr>
                            <td><code>025C</code></td>
                            <td>Temperature = 25 °C</td>
                        </tr>
                        <tr>
                            <td><code>1v80</code></td>
                            <td>Operating Voltage = 1.80 V</td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="subsection-title">🏗️ Example: AND Gate Flavors</div>
            <p>The same <strong>2-input AND gate</strong> is available in multiple drive strengths:</p>

            <div class="table-wrapper">
                <table class="modern-table">
                    <thead>
                        <tr>
                            <th>Cell Flavor</th>
                            <th>Area (µm²)</th>
                            <th>Speed</th>
                            <th>Power</th>
                            <th>Delay</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td><strong>AND2_0</strong> 🐢</td>
                            <td>6.25 × 10⁸</td>
                            <td>Very Slow</td>
                            <td>Very Low 🔋</td>
                            <td>High ⏱️</td>
                        </tr>
                        <tr>
                            <td><strong>AND2_2</strong> ⚖️</td>
                            <td>7.50 × 10⁸</td>
                            <td>Medium</td>
                            <td>Moderate 🔋</td>
                            <td>Medium ⏱️</td>
                        </tr>
                        <tr>
                            <td><strong>AND2_4</strong> 🚀</td>
                            <td>8.75 × 10⁸</td>
                            <td>Very Fast</td>
                            <td>High 🔋</td>
                            <td>Very Low ⚡</td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="highlight-box">
                💡 Designers select a flavor depending on <strong>timing closure and power budgets</strong>.
            </div>

            <div class="subsection-title">📜 Liberty Snippet Example</div>
            <div class="code-block">
                <code>
cell ("sky130_fd_sc_hd__and2_0") {<br>
  &nbsp;&nbsp;area : 6.25;<br>
  &nbsp;&nbsp;pin(A1) { direction : input; }<br>
  &nbsp;&nbsp;pin(A2) { direction : input; }<br>
  &nbsp;&nbsp;pin(X)  { direction : output;<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;function : "(A1 & A2)"; }<br>
}<br><br>
cell ("sky130_fd_sc_hd__and2_2") {<br>
  &nbsp;&nbsp;area : 7.50;<br>
  &nbsp;&nbsp;pin(A1) { direction : input; }<br>
  &nbsp;&nbsp;pin(A2) { direction : input; }<br>
  &nbsp;&nbsp;pin(X)  { direction : output;<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;function : "(A1 & A2)"; }<br>
}<br><br>
cell ("sky130_fd_sc_hd__and2_4") {<br>
  &nbsp;&nbsp;area : 8.75;<br>
  &nbsp;&nbsp;pin(A1) { direction : input; }<br>
  &nbsp;&nbsp;pin(A2) { direction : input; }<br>
  &nbsp;&nbsp;pin(X)  { direction : output;<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;function : "(A1 & A2)"; }<br>
}
                </code>
            </div>
        </section>

        <section class="section fade-in" id="section-2">
            <h2 class="section-title">🏗️ 2. Hierarchical vs Flat Synthesis</h2>
            
            <div class="info-grid">
                <div class="info-card">
                    <h3>📌 Hierarchical Synthesis</h3>
                    <p>Preserves design hierarchy, synthesizes each module independently</p>
                    <ul style="margin-top: 1rem;">
                        <li>✅ Better organization</li>
                        <li>✅ Faster compile times</li>
                        <li>❌ Less optimization opportunity</li>
                    </ul>
                </div>
                <div class="info-card">
                    <h3>🔄 Flat Synthesis</h3>
                    <p>Flattens all hierarchy, optimizes across module boundaries</p>
                    <ul style="margin-top: 1rem;">
                        <li>✅ Maximum optimization</li>
                        <li>✅ Better area/timing</li>
                        <li>❌ Longer compile times</li>
                    </ul>
                </div>
            </div>

            <div class="highlight-box">
                🧪 <strong>Lab Experiments:</strong> Part 1 & 2 demonstrate the trade-offs between hierarchical preservation and flat optimization strategies.
            </div>
        </section>

        <section class="section fade-in" id="section-3">
            <h2 class="section-title">🔁 3. Flop Coding Styles & Optimizations</h2>
            
            <div class="subsection-title">💡 Why Flops?</div>
            <p>Flip-flops are the fundamental storage elements in digital design, crucial for:</p>
            <div class="info-grid">
                <div class="info-card">
                    <h4>🔄 Sequential Logic</h4>
                    <p>State storage between clock cycles</p>
                </div>
                <div class="info-card">
                    <h4>⏰ Timing Control</h4>
                    <p>Synchronous operation</p>
                </div>
                <div class="info-card">
                    <h4>🔧 Pipeline Stages</h4>
                    <p>Data flow control</p>
                </div>
            </div>

            <div class="subsection-title">🔎 Different Coding Styles</div>
            <div class="highlight-box">
                🧪 <strong>Synthesis & Simulation Labs (Parts 1–5):</strong><br>
                Explore various flip-flop implementations, reset strategies, and optimization techniques through hands-on experiments.
            </div>

            <div class="info-grid">
                <div class="info-card">
                    <h4>🔄 Synchronous Reset</h4>
                    <p>Reset controlled by clock edge</p>
                </div>
                <div class="info-card">
                    <h4>⚡ Asynchronous Reset</h4>
                    <p>Immediate reset independent of clock</p>
                </div>
                <div class="info-card">
                    <h4>🎯 Set/Reset Priority</h4>
                    <p>Handling conflicting control signals</p>
                </div>
                <div class="info-card">
                    <h4>🔧 Enable Logic</h4>
                    <p>Conditional updates and power savings</p>
                </div>
            </div>
        </section>
    </div>

    <script>
        // Generate floating particles
        function createParticles() {
            const particlesContainer = document.querySelector('.floating-particles');
            const particleCount = 15;

            for (let i = 0; i < particleCount; i++) {
                const particle = document.createElement('div');
                particle.classList.add('particle');
                
                // Random size between 2px and 6px
                const size = Math.random() * 4 + 2;
                particle.style.width = `${size}px`;
                particle.style.height = `${size}px`;
                
                // Random position
                particle.style.left = `${Math.random() * 100}%`;
                particle.style.top = `${Math.random() * 100}%`;
                
                // Random animation delay
                particle.style.animationDelay = `${Math.random() * 6}s`;
                particle.style.animationDuration = `${4 + Math.random() * 4}s`;
                
                particlesContainer.appendChild(particle);
            }
        }

        // Progress bar functionality
        function updateProgressBar() {
            const scrollTop = window.pageYOffset;
            const docHeight = document.body.scrollHeight - window.innerHeight;
            const scrollPercent = (scrollTop / docHeight) * 100;
            
            document.getElementById('progressBar').style.width = scrollPercent + '%';
        }

        // Navigation dots functionality
        function updateNavDots() {
            const sections = document.querySelectorAll('.section');
            const dots = document.querySelectorAll('.nav-dot');
            
            let current = 0;
            const scrollPosition = window.pageYOffset + window.innerHeight / 2;

            sections.forEach((section, index) => {
                const sectionTop = section.offsetTop;
                const sectionBottom = sectionTop + section.offsetHeight;
                
                if (scrollPosition >= sectionTop && scrollPosition <= sectionBottom) {
                    current = index + 1; // +1 because first dot is for hero section
                }
            });

            dots.forEach((dot, index) => {
                dot.classList.toggle('active', index === current);
            });
        }

        // Fade in animation on scroll
        function handleScrollAnimations() {
            const elements = document.querySelectorAll('.fade-in');
            
            elements.forEach(element => {
                const elementTop = element.getBoundingClientRect().top;
                const elementVisible = 150;
                
                if (elementTop < window.innerHeight - elementVisible) {
                    element.style.opacity = '1';
                    element.style.transform = 'translateY(0)';
                }
            });
        }

        // Smooth scroll for navigation dots
        document.querySelectorAll('.nav-dot').forEach((dot, index) => {
            dot.addEventListener('click', () => {
                const sections = [document.querySelector('.hero-section'), ...document.querySelectorAll('.section')];
                if (sections[index]) {
                    sections[index].scrollIntoView({
                        behavior: 'smooth'
                    });
                }
            });
        });

        // Event listeners
        window.addEventListener('scroll', () => {
            updateProgressBar();
            updateNavDots();
            handleScrollAnimations();
        });

        window.addEventListener('load', () => {
            createParticles();
            handleScrollAnimations();
        });

        // Initialize
        document.addEventListener('DOMContentLoaded', () => {
            // Add initial fade-in delays
            const fadeElements = document.querySelectorAll('.fade-in');
            fadeElements.forEach((element, index) => {
                element.style.animationDelay = `${index * 0.1}s`;
            });
        });
    </script>
</body>
</html>
