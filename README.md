<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Epilog - Task Timers</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #0f0c29 0%, #302b63 50%, #24243e 100%);
            min-height: 100vh; display: flex; flex-direction: column; align-items: center;
            justify-content: center; padding: 20px; color: #fff; overflow: hidden;
            position: relative;
        }
        body::before {
            content: ''; position: absolute; top: 0; left: 0; width: 100%; height: 100%;
            background: radial-gradient(circle at 20% 30%, rgba(92, 107, 192, .15) 0%, transparent 40%),
                        radial-gradient(circle at 80% 70%, rgba(106, 90, 205, .15) 0%, transparent 40%);
            z-index: -1;
        }
        .container { text-align: center; max-width: 800px; width: 100%; z-index: 1; }
        .logo {
            font-size: 4rem; font-weight: 700; letter-spacing: .5rem; margin-bottom: 2rem;
            background: linear-gradient(45deg, #6a9eff, #a78bfa); -webkit-background-clip: text;
            -webkit-text-fill-color: transparent; background-clip: text; text-transform: uppercase;
            animation: glow 2s ease-in-out infinite alternate; filter: drop-shadow(0 0 20px rgba(106, 158, 255, .3));
        }
        @keyframes glow { from { filter: drop-shadow(0 0 15px rgba(106, 158, 255, .3)); }
                          to { filter: drop-shadow(0 0 30px rgba(167, 139, 250, .5)); } }
        .get-key-btn {
            background: linear-gradient(45deg, #6a9eff, #a78bfa); border: none; padding: 16px 48px;
            font-size: 1.2rem; font-weight: 600; color: #fff; border-radius: 50px; cursor: pointer;
            transition: all .3s ease; margin: 2rem 0; box-shadow: 0 4px 20px rgba(106, 158, 255, .3);
            position: relative; overflow: hidden; z-index: 2;
        }
        .get-key-btn:hover { transform: translateY(-3px); box-shadow: 0 8px 30px rgba(167, 139, 250, .4); }
        .get-key-btn::after {
            content: ''; position: absolute; top: -50%; left: -60%; width: 20px; height: 200%;
            background: rgba(255, 255, 255, .3); transform: rotate(25deg); transition: all .6s;
        }
        .get-key-btn:hover::after { left: 120%; }
        .timers-grid {
            display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 2rem; margin-top: 2rem;
        }
        .timer-card {
            background: rgba(255, 255, 255, .08); backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px); border: 1px solid rgba(255, 255, 255, .1);
            border-radius: 20px; padding: 2rem; transition: all .3s ease;
            position: relative; overflow: hidden; box-shadow: 0 8px 32px rgba(0, 0, 0, .1);
        }
        .timer-card:hover { transform: translateY(-5px); background: rgba(255, 255, 255, .12); }
        .timer-card::before {
            content: ''; position: absolute; top: 0; left: 0; right: 0; height: 4px;
            background: linear-gradient(90deg, #6a9eff, #a78bfa);
        }
        .timer-title { font-size: 1.3rem; margin-bottom: 1rem; color: #a78bfa; font-weight: 600; }
        .timer-display {
            font-size: 3rem; font-weight: 700; font-family: 'Courier New', monospace;
            color: #fff; margin: 1rem 0; text-shadow: 0 0 10px rgba(106, 158, 255, .5);
        }
        .timer-label { font-size: .9rem; color: #b8b8d9; margin-top: .5rem; }
        .progress-bar {
            width: 100%; height: 6px; background: rgba(255, 255, 255, .1);
            border-radius: 3px; margin-top: 1.5rem; overflow: hidden;
        }
        .progress-fill {
            height: 100%; background: linear-gradient(90deg, #6a9eff, #a78bfa);
            border-radius: 3px; transition: width 1s linear; box-shadow: 0 0 10px rgba(106, 158, 255, .3);
        }
        .key-selection { margin: 2rem 0; }
        .key-option {
            display: inline-block; margin: 0 1rem; padding: 1rem 2rem;
            background: rgba(255, 255, 255, .1); border: 2px solid rgba(106, 158, 255, .3);
            border-radius: 15px; cursor: pointer; transition: all .3s ease;
            backdrop-filter: blur(5px);
        }
        .key-option:hover { border-color: #6a9eff; background: rgba(106, 158, 255, .2); }
        .key-option.selected {
            background: linear-gradient(45deg, rgba(106, 158, 255, .3), rgba(167, 139, 250, .3));
            border-color: #a78bfa;
        }
        .modal {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, .8); z-index: 1000; align-items: center; justify-content: center;
        }
        .modal.active { display: flex; }
        .modal-content {
            background: linear-gradient(135deg, #1a1a2e, #16213e); padding: 3rem 2rem;
            border-radius: 20px; max-width: 500px; text-align: center; position: relative;
            border: 1px solid rgba(106, 158, 255, .3); box-shadow: 0 0 50px rgba(106, 158, 255, .3);
        }
        .modal-title { font-size: 1.8rem; margin-bottom: 1rem; color: #6a9eff; }
        .modal-text { margin: 1rem 0; line-height: 1.6; }
        .youtube-link {
            display: inline-block; margin: 1.5rem 0; padding: 1rem 2rem;
            background: #ff0000; color: #fff; text-decoration: none; border-radius: 10px;
            font-weight: 600; transition: all .3s ease;
        }
        .youtube-link:hover { background: #cc0000; transform: scale(1.05); }
        .timer-countdown {
            font-size: 2rem; font-weight: 700; color: #ff6b6b; margin: 1rem 0;
            font-family: 'Courier New', monospace;
        }
        .check-btn {
            background: linear-gradient(45deg, #6a9eff, #a78bfa); border: none; padding: 1rem 2rem;
            font-size: 1.1rem; font-weight: 600; color: #fff; border-radius: 10px; cursor: pointer;
            margin-top: 1rem; transition: all .3s ease;
        }
        .check-btn:hover { transform: translateY(-2px); box-shadow: 0 5px 20px rgba(167, 139, 250, .4); }
        .check-btn:disabled { opacity: .5; cursor: not-allowed; }
        .key-display {
            background: rgba(0, 0, 0, .5); padding: 1.5rem; border-radius: 10px; margin-top: 1.5rem;
            font-family: 'Courier New', monospace; font-size: 1.2rem; letter-spacing: 2px; color: #6a9eff;
            border: 1px dashed rgba(106, 158, 255, .5);
        }
        .error-text { color: #ff6b6b; margin-top: 1rem; font-weight: 600; }
        @media (max-width: 600px) {
            .logo { font-size: 2.5rem; }
            .timer-display { font-size: 2rem; }
            .timers-grid { grid-template-columns: 1fr; }
            .modal-content { margin: 1rem; padding: 2rem 1rem; }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="logo">Epilog</h1>
        
        <div class="key-selection">
            <div class="key-option selected" data-hours="5">5 часов<br><small>одно задание</small></div>
            <div class="key-option" data-hours="12">12 часов<br><small>три задания</small></div>
        </div>

        <button class="get-key-btn" onclick="openModal()">Get Key</button>
        
        <div class="timers-grid">
            <div class="timer-card">
                <div class="timer-title">Одно задание</div>
                <div class="timer-display" id="timer1">05:00:00</div>
                <div class="timer-label">Осталось времени</div>
                <div class="progress-bar">
                    <div class="progress-fill" id="progress1" style="width: 100%"></div>
                </div>
            </div>
            
            <div class="timer-card">
                <div class="timer-title">Три задания</div>
                <div class="timer-display" id="timer2">12:00:00</div>
                <div class="timer-label">Осталось времени</div>
                <div class="progress-bar">
                    <div class="progress-fill" id="progress2" style="width: 100%"></div>
                </div>
            </div>
        </div>
    </div>

    <div class="modal" id="modal">
        <div class="modal-content">
            <div class="modal-title">📺 Подпишитесь на канал</div>
            <div class="modal-text">
                Чтобы получить ключ, подпишитесь на наш YouTube канал:
            </div>
            <a href="https://youtube.com/@tiarmnss?si=L9KgBb-g2-O8vQ0H" 
               target="_blank" 
               class="youtube-link">
                🔴 Подписаться @tiarmnss
            </a>
            <div class="modal-text">
                После подписки нажмите "Проверить подписку"
            </div>
            <div class="timer-countdown" id="countdown">Осталось: 20 сек</div>
            <button class="check-btn" id="checkBtn" onclick="checkSubscription()">Проверить подписку</button>
            <div id="result"></div>
        </div>
    </div>

    <script>
        // Выбор ключа
        let selectedHours = 5;
        document.querySelectorAll('.key-option').forEach(option => {
            option.addEventListener('click', function() {
                document.querySelectorAll('.key-option').forEach(o => o.classList.remove('selected'));
                this.classList.add('selected');
                selectedHours = parseInt(this.dataset.hours);
            });
        });

        // Модальное окно
        let countdownInterval;
        let timeLeft = 20;
        
        function openModal() {
            document.getElementById('modal').classList.add('active');
            startCountdown();
        }

        function startCountdown() {
            timeLeft = 20;
            updateCountdown();
            countdownInterval = setInterval(() => {
                timeLeft--;
                updateCountdown();
                if (timeLeft <= 0) {
                    clearInterval(countdownInterval);
                    document.getElementById('checkBtn').disabled = true;
                    document.getElementById('result').innerHTML = '<div class="error-text">⏰ Время вышло! Попробуйте снова.</div>';
                }
            }, 1000);
        }

        function updateCountdown() {
            document.getElementById('countdown').textContent = `Осталось: ${timeLeft} сек`;
        }

        function closeModal() {
            document.getElementById('modal').classList.remove('active');
            clearInterval(countdownInterval);
            document.getElementById('checkBtn').disabled = false;
            document.getElementById('result').innerHTML = '';
        }

        // Проверка подписки (имитация)
        function checkSubscription() {
            const btn = document.getElementById('checkBtn');
            const result = document.getElementById('result');
            
            btn.disabled = true;
            btn.textContent = '⏳ Проверяю...';
            
            // Имитация проверки (3 секунды)
            setTimeout(() => {
                if (timeLeft > 0) {
                    clearInterval(countdownInterval);
                    const key = generateKey();
                    const hours = selectedHours === 5 ? 'для одного задания' : 'для трех заданий';
                    result.innerHTML = `
                        <div class="key-display">
                            ✅ Подписка подтверждена!<br>
                            Ваш ключ ${hours}:<br><br>
                            <strong>${key}</strong><br><br>
                            <small>Скопируйте ключ для активации</small>
                        </div>
                        <button class="check-btn" onclick="closeModal()" style="margin-top: 1rem;">Закрыть</button>
                    `;
                    document.getElementById('checkBtn').style.display = 'none';
                } else {
                    btn.disabled = true;
                    result.innerHTML = '<div class="error-text">⏰ Время вышло!</div>';
                }
            }, 3000);
        }

        // Генератор ключа
        function generateKey() {
            const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
            let key = 'EPILOG-';
            for (let i = 0; i < 12; i++) {
                if (i > 0 && i % 4 === 0) key += '-';
                key += chars.charAt(Math.floor(Math.random() * chars.length));
            }
            return key;
        }

        // Таймеры задач
        class Timer {
            constructor(elementId, progressId, totalSeconds) {
                this.element = document.getElementById(elementId);
                this.progressElement = document.getElementById(progressId);
                this.totalSeconds = totalSeconds;
                this.remainingSeconds = totalSeconds;
                this.isRunning = true;
                this.start();
            }

            start() {
                const interval = setInterval(() => {
                    if (this.remainingSeconds <= 0) {
                        clearInterval(interval);
                        this.isRunning = false;
                        this.element.textContent = 'Время вышло!';
                        return;
                    }
                    this.remainingSeconds--;
                    this.updateDisplay();
                    this.updateProgress();
                }, 1000);
            }

            updateDisplay() {
                const hours = Math.floor(this.remainingSeconds / 3600);
                const minutes = Math.floor((this.remainingSeconds % 3600) / 60);
                const seconds = this.remainingSeconds % 60;
                this.element.textContent = String(hours).padStart(2, '0') + ':' + 
                                          String(minutes).padStart(2, '0') + ':' + 
                                          String(seconds).padStart(2, '0');
            }

            updateProgress() {
                this.progressElement.style.width = (this.remainingSeconds / this.totalSeconds * 100) + '%';
            }
        }

        // Инициализация таймеров
        const timer1 = new Timer('timer1', 'progress1', 18000);
        const timer2 = new Timer('timer2', 'progress2', 43200);
    </script>
</body>
</html>
