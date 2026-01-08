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
            position: relative;
            overflow: hidden;
        }

        body::before {
            content: '';
            position: absolute;
            width: 500px;
            height: 500px;
            background: radial-gradient(circle, rgba(126, 34, 206, 0.3) 0%, transparent 70%);
            border-radius: 50%;
            top: -250px;
            right: -250px;
            animation: float 6s ease-in-out infinite;
        }

        body::after {
            content: '';
            position: absolute;
            width: 400px;
            height: 400px;
            background: radial-gradient(circle, rgba(30, 60, 114, 0.4) 0%, transparent 70%);
            border-radius: 50%;
            bottom: -200px;
            left: -200px;
            animation: float 8s ease-in-out infinite reverse;
        }

        @keyframes float {
            0%, 100% { transform: translateY(0px); }
            50% { transform: translateY(-30px); }
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
            position: relative;
            z-index: 1;
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
            text-shadow: 0 5px 20px rgba(0, 0, 0, 0.3);
            animation: glow 2s ease-in-out infinite alternate;
        }

        @keyframes glow {
            from {
                text-shadow: 0 5px 20px rgba(255, 255, 255, 0.3),
                             0 0 30px rgba(126, 34, 206, 0.5);
            }
            to {
                text-shadow: 0 5px 20px rgba(255, 255, 255, 0.5),
                             0 0 50px rgba(126, 34, 206, 0.8);
            }
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
            cursor: not-allowed;
            transition: all 0.4s ease;
            margin-bottom: 25px;
            opacity: 0.6;
            box-shadow: 0 10px 35px rgba(126, 34, 206, 0.4);
            position: relative;
            overflow: hidden;
        }

        .get-key-btn::before {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            width: 0;
            height: 0;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 50%;
            transform: translate(-50%, -50%);
            transition: width 0.6s, height 0.6s;
        }

        .get-key-btn:hover::before {
            width: 300px;
            height: 300px;
        }

        .status {
            text-align: center;
            color: #ffeb3b;
            font-size: 15px;
            margin-bottom: 35px;
            font-weight: 600;
            text-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
        }

        .status-icon {
            animation: pulse 1.5s infinite;
            display: inline-block;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); opacity: 1; }
            50% { transform: scale(1.1); opacity: 0.8; }
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
            transition: all 0.4s ease;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .task-item:hover {
            transform: translateY(-5px);
            box-shadow: 0 15px 40px rgba(126, 34, 206, 0.4);
            background: rgba(255, 255, 255, 0.2);
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
            filter: drop-shadow(0 2px 5px rgba(0, 0, 0, 0.3));
        }

        @media (max-width: 600px) {
            .container {
                padding: 30px 20px;
            }
            
            .logo h1 {
                font-size: 42px;
            }

            .timer {
                gap: 8px;
            }

            .time-value {
                font-size: 24px;
            }

            .time-label {
                font-size: 9px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="logo">
            <h1>EPILOG</h1>
        </div>

        <button class="get-key-btn" disabled>
            🔑 Get Key
        </button>

        <div class="status">
            <span class="status-icon">⚠️</span> Функция временно недоступна
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

    <script>
        // Начальное время для каждого задания (в секундах)
        let timers = {
            1: 5 * 60 * 60,      // 5 часов
            2: 12 * 60 * 60,     // 12 часов
            3: 12 * 60 * 60      // 12 часов
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

        // Обновление таймеров каждую секунду
        setInterval(() => {
            updateTimer(1);
            updateTimer(2);
            updateTimer(3);
        }, 1000);
    </script>
</body>
</html>
