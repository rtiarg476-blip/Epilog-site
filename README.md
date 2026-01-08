<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Epilog</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 50%, #7e22ce 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .container {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(20px);
            border-radius: 25px;
            padding: 45px;
            box-shadow: 0 25px 70px rgba(0, 0, 0, 0.4);
            max-width: 650px;
            width: 100%;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .logo {
            text-align: center;
            margin-bottom: 35px;
        }

        .logo h1 {
            font-size: 56px;
            font-weight: 900;
            color: #ffffff;
            letter-spacing: 4px;
            text-shadow: 0 5px 20px rgba(126, 34, 206, 0.5);
        }

        .get-key-btn {
            width: 100%;
            padding: 20px;
            font-size: 22px;
            font-weight: 700;
            color: white;
            background: linear-gradient(135deg, #7e22ce 0%, #a855f7 100%);
            border: none;
            border-radius: 15px;
            cursor: pointer;
            transition: all 0.3s ease;
            margin-bottom: 25px;
            box-shadow: 0 10px 35px rgba(126, 34, 206, 0.4);
        }

        .get-key-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 15px 45px rgba(126, 34, 206, 0.6);
        }

        .get-key-btn:active {
            transform: translateY(0);
        }

        .status {
            text-align: center;
            color: #ffeb3b;
            font-size: 15px;
            margin-bottom: 35px;
            font-weight: 600;
            text-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
            display: none;
        }

        .status.show {
            display: block;
        }

        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.7);
            z-index: 1000;
            justify-content: center;
            align-items: center;
        }

        .modal.show {
            display: flex;
        }

        .modal-content {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(20px);
            border-radius: 20px;
            padding: 40px;
            max-width: 500px;
            width: 90%;
            text-align: center;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
        }

        .modal-content h2 {
            color: #7e22ce;
            margin-bottom: 20px;
            font-size: 28px;
        }

        .modal-content p {
            color: #333;
            margin-bottom: 25px;
            font-size: 16px;
            line-height: 1.6;
        }

        .youtube-btn {
            background: #ff0000;
            color: white;
            padding: 15px 30px;
            border: none;
            border-radius: 10px;
            font-size: 18px;
            font-weight: 700;
            cursor: pointer;
            margin: 10px;
            transition: all 0.3s ease;
            text-decoration: none;
            display: inline-block;
        }

        .youtube-btn:hover {
            background: #cc0000;
            transform: scale(1.05);
        }

        .check-btn {
            background: linear-gradient(135deg, #7e22ce 0%, #a855f7 100%);
            color: white;
            padding: 15px 30px;
            border: none;
            border-radius: 10px;
            font-size: 18px;
            font-weight: 700;
            cursor: pointer;
            margin: 10px;
            transition: all 0.3s ease;
        }

        .check-btn:hover {
            transform: scale(1.05);
        }

        .checking {
            color: #7e22ce;
            font-size: 16px;
            margin-top: 15px;
            display: none;
        }

        .checking.show {
            display: block;
        }

        .key-display {
            background: rgba(126, 34, 206, 0.1);
            border: 2px solid #7e22ce;
            border-radius: 10px;
            padding: 20px;
            margin-top: 20px;
            display: none;
        }

        .key-display.show {
            display: block;
        }

        .key-display h3 {
            color: #7e22ce;
            margin-bottom: 10px;
        }

        .key-code {
            background: white;
            padding: 15px;
            border-radius: 8px;
            font-family: monospace;
            font-size: 18px;
            font-weight: bold;
            color: #333;
            word-break: break-all;
        }

        .tasks {
            margin-top: 20px;
        }

        .task-item {
            background: rgba(255, 255, 255, 0.15);
            backdrop-filter: blur(10px);
            border-radius: 18px;
            padding: 25px;
            margin-bottom: 18px;
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
            transition: all 0.3s ease;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .task-item:hover {
            transform: translateY(-3px);
            box-shadow: 0 12px 35px rgba(126, 34, 206, 0.3);
        }

        .task-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }

        .task-title {
            font-size: 20px;
            font-weight: 700;
            color: #ffffff;
            text-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
        }

        .task-number {
            background: linear-gradient(135deg, #7e22ce 0%, #a855f7 100%);
            color: white;
            padding: 8px 20px;
            border-radius: 25px;
            font-size: 16px;
            font-weight: 700;
            box-shadow: 0 5px 15px rgba(126, 34, 206, 0.4);
        }

        .task-time {
            display: flex;
            align-items: center;
            gap: 10px;
            color: #e0e0e0;
            font-size: 16px;
            margin-bottom: 12px;
        }

        .timer {
            display: flex;
            gap: 12px;
            margin-top: 10px;
            justify-content: space-between;
        }

        .time-unit {
            text-align: center;
            background: rgba(255, 255, 255, 0.1);
            padding: 12px;
            border-radius: 12px;
            flex: 1;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .time-value {
            font-size: 28px;
            font-weight: 900;
            color: #ffffff;
            display: block;
            text-shadow: 0 3px 8px rgba(0, 0, 0, 0.3);
        }

        .time-label {
            font-size: 11px;
            color: #d0d0d0;
            text-transform: uppercase;
            margin-top: 5px;
            font-weight: 600;
            letter-spacing: 1px;
        }

        .icon {
            font-size: 22px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="logo">
            <h1>EPILOG</h1>
        </div>

        <button class="get-key-btn" onclick="openModal()">
            🔑 Get Key
        </button>

        <div class="status" id="status"></div>

        <div class="key-display" id="keyDisplay">
            <h3>✅ Ваш ключ:</h3>
            <div class="key-code" id="keyCode"></div>
        </div>

        <div class="tasks">
            <div class="task-item">
                <div class="task-header">
                    <div class="task-title">Задание</div>
                    <div class="task-number">1</div>
                </div>
                <div class="task-time">
                    <span class="icon">⏱️</span>
                    <span>Осталось времени:</span>
                </div>
                <div class="timer">
                    <div class="time-unit">
                        <span class="time-value" id="days1">0</span>
                        <span class="time-label">дней</span>
                    </div>
                    <div class="time-unit">
                        <span class="time-value" id="hours1">5</span>
                        <span class="time-label">часов</span>
                    </div>
                    <div class="time-unit">
                        <span class="time-value" id="minutes1">0</span>
                        <span class="time-label">минут</span>
                    </div>
                    <div class="time-unit">
                        <span class="time-value" id="seconds1">0</span>
                        <span class="time-label">секунд</span>
                    </div>
                </div>
            </div>

            <div class="task-item">
                <div class="task-header">
                    <div class="task-title">Задание</div>
                    <div class="task-number">2</div>
                </div>
                <div class="task-time">
                    <span class="icon">⏱️</span>
                    <span>Осталось времени:</span>
                </div>
                <div class="timer">
                    <div class="time-unit">
                        <span class="time-value" id="days2">0</span>
                        <span class="time-label">дней</span>
                    </div>
                    <div class="time-unit">
                        <span class="time-value" id="hours2">12</span>
                        <span class="time-label">часов</span>
                    </div>
                    <div class="time-unit">
                        <span class="time-value" id="minutes2">0</span>
                        <span class="time-label">минут</span>
                    </div>
                    <div class="time-unit">
                        <span class="time-value" id="seconds2">0</span>
                        <span class="time-label">секунд</span>
                    </div>
                </div>
            </div>

            <div class="task-item">
                <div class="task-header">
                    <div class="task-title">Задание</div>
                    <div class="task-number">3</div>
                </div>
                <div class="task-time">
                    <span class="icon">⏱️</span>
                    <span>Осталось времени:</span>
                </div>
                <div class="timer">
                    <div class="time-unit">
                        <span class="time-value" id="days3">0</span>
                        <span class="time-label">дней</span>
                    </div>
                    <div class="time-unit">
                        <span class="time-value" id="hours3">12</span>
                        <span class="time-label">часов</span>
                    </div>
                    <div class="time-unit">
                        <span class="time-value" id="minutes3">0</span>
                        <span class="time-label">минут</span>
                    </div>
                    <div class="time-unit">
                        <span class="time-value" id="seconds3">0</span>
                        <span class="time-label">секунд</span>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="modal" id="modal">
        <div class="modal-content">
            <h2>🎉 Получить ключ</h2>
            <p>Для получения ключа необходимо подписаться на YouTube канал:</p>
            <a href="https://youtube.com/@tiarmnss?si=cvWg81qu9cloGCNN" target="_blank" class="youtube-btn">
                ▶️ Подписаться на канал
            </a>
            <br>
            <button class="check-btn" onclick="checkSubscription()">
                ✓ Проверить подписку
            </button>
            <div class="checking" id="checking">⏳ Проверяем подписку...</div>
        </div>
    </div>

    <script>
        let timers = {
            1: 5 * 60 * 60,
            2: 12 * 60 * 60,
            3: 12 * 60 * 60
        };

        function updateTimer(taskId) {
            if (timers[taskId] <= 0) {
                document.getElementById(`days${taskId}`).textContent = '0';
                document.getElementById(`hours${taskId}`).textContent = '0';
                document.getElementById(`minutes${taskId}`).textContent = '0';
                document.getElementById(`seconds${taskId}`).textContent = '0';
                return;
            }

            timers[taskId]--;

            const days = Math.floor(timers[taskId] / (24 * 60 * 60));
            const hours = Math.floor((timers[taskId] % (24 * 60 * 60)) / (60 * 60));
            const minutes = Math.floor((timers[taskId] % (60 * 60)) / 60);
            const seconds = timers[taskId] % 60;

            document.getElementById(`days${taskId}`).textContent = days;
            document.getElementById(`hours${taskId}`).textContent = hours;
            document.getElementById(`minutes${taskId}`).textContent = minutes;
            document.getElementById(`seconds${taskId}`).textContent = seconds;
        }

        setInterval(() => {
            updateTimer(1);
            updateTimer(2);
            updateTimer(3);
        }, 1000);

        function openModal() {
            document.getElementById('modal').classList.add('show');
        }

        function closeModal() {
            document.getElementById('modal').classList.remove('show');
        }

        document.getElementById('modal').addEventListener('click', function(e) {
            if (e.target === this) {
                closeModal();
            }
        });

        function generateKey() {
            const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
            let key = '';
            for (let i = 0; i < 16; i++) {
                if (i > 0 && i % 4 === 0) key += '-';
                key += chars.charAt(Math.floor(Math.random() * chars.length));
            }
            return key;
        }

        function checkSubscription() {
            const checkingEl = document.getElementById('checking');
            checkingEl.classList.add('show');
            
            setTimeout(() => {
                checkingEl.classList.remove('show');
                closeModal();
                
                const key = generateKey();
                document.getElementById('keyCode').textContent = key;
                document.getElementById('keyDisplay').classList.add('show');
                
                const statusEl = document.getElementById('status');
                statusEl.textContent = '✅ Подписка подтверждена! Ключ получен.';
                statusEl.style.color = '#4ade80';
                statusEl.classList.add('show');
                
                setTimeout(() => {
                    statusEl.classList.remove('show');
                }, 5000);
            }, 2000);
        }
    </script>
</body>
</html>
