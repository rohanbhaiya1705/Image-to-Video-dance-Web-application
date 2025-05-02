# Image-to-Video-dance-Web-application
Image to Video dance Web application
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dancing Image vs AI</title>
    <style>
        .container {
            max-width: 800px;
            margin: 20px auto;
            padding: 20px;
            font-family: Arial, sans-serif;
        }

        .dance-area {
            display: flex;
            justify-content: space-around;
            margin: 20px 0;
            background: #111;
            padding: 20px;
        }

        .dancer {
            width: 200px;
            height: 200px;
            position: relative;
            transition: transform 0.3s ease;
        }

        #aiOpponent {
            background: #ff4444;
            border-radius: 50%;
        }

        .upload-section {
            margin-bottom: 20px;
            text-align: center;
        }

        .controls {
            background: #333;
            padding: 15px;
            border-radius: 8px;
        }

        input[type="number"] {
            padding: 8px;
            margin: 0 10px;
            width: 120px;
        }

        button {
            padding: 10px 20px;
            background: #4CAF50;
            border: none;
            color: white;
            cursor: pointer;
            border-radius: 4px;
        }

        @keyframes userDance {
            0% { transform: rotate(0deg) scale(1); }
            25% { transform: rotate(10deg) scale(1.1); }
            50% { transform: rotate(-10deg) scale(0.9); }
            75% { transform: rotate(5deg) scale(1.05); }
            100% { transform: rotate(0deg) scale(1); }
        }

        @keyframes aiDance {
            0% { transform: translateY(0) skew(0deg); }
            25% { transform: translateY(-20px) skew(10deg); }
            50% { transform: translateY(0) skew(-10deg); }
            75% { transform: translateY(-10px) skew(5deg); }
            100% { transform: translateY(0) skew(0deg); }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="upload-section">
            <input type="file" id="imageInput" accept="image/*">
            <input type="number" id="durationInput" placeholder="Duration (seconds)" min="1">
            <button onclick="startDance()">Start Dance Battle</button>
        </div>

        <div class="dance-area">
            <img id="userDancer" class="dancer" style="display: none;">
            <div id="aiOpponent" class="dancer"></div>
        </div>

        <div class="controls">
            <div class="progress-bar" style="background: #444; height: 5px;">
                <div id="progress" style="height: 100%; background: #4CAF50; width: 0%;"></div>
            </div>
            <button id="playPause" onclick="toggleDance()">Play</button>
            <span id="timer" style="color: white;">0:00 / 0:00</span>
        </div>
    </div>

    <script>
        let isDancing = false;
        let startTime = 0;
        let pausedDuration = 0;
        let totalDuration = 0;
        let animationFrameId;
        const userDancer = document.getElementById('userDancer');
        const aiOpponent = document.getElementById('aiOpponent');

        function startDance() {
            const file = document.getElementById('imageInput').files[0];
            const duration = parseInt(document.getElementById('durationInput').value);
            
            if (!file || !duration) {
                alert('Please select an image and duration');
                return;
            }

            totalDuration = duration * 1000;
            document.getElementById('timer').textContent = `0:00 / ${formatTime(duration)}`;
            
            const reader = new FileReader();
            reader.onload = (e) => {
                userDancer.src = e.target.result;
                userDancer.style.display = 'block';
                toggleDance();
            };
            reader.readAsDataURL(file);
        }

        function toggleDance() {
            isDancing = !isDancing;
            document.getElementById('playPause').textContent = isDancing ? 'Pause' : 'Play';
            
            if (isDancing) {
                startTime = Date.now() - pausedDuration;
                animateDance();
                startAIAnimation();
            } else {
                pausedDuration = Date.now() - startTime;
                cancelAnimationFrame(animationFrameId);
                stopAIAnimation();
            }
        }

        function animateDance() {
            const elapsed = Date.now() - startTime;
            const progress = Math.min(elapsed / totalDuration, 1);
            
            // Update progress bar
            document.getElementById('progress').style.width = `${progress * 100}%`;
            document.getElementById('timer').textContent = 
                `${formatTime(elapsed / 1000)} / ${formatTime(totalDuration / 1000)}`;

            // Random dance effect
            userDancer.style.transform = `
                rotate(${Math.sin(elapsed/200) * 10}deg)
                scale(${1 + Math.sin(elapsed/300) * 0.2})
            `;

            if (elapsed < totalDuration) {
                animationFrameId = requestAnimationFrame(animateDance);
            } else {
                resetDance();
            }
        }

        function startAIAnimation() {
            aiOpponent.style.animation = `aiDance 0.8s infinite alternate`;
            // Random color changes for AI
            setInterval(() => {
                aiOpponent.style.backgroundColor = 
                    `hsl(${Math.random() * 360}, 70%, 50%)`;
            }, 3000);
        }

        function stopAIAnimation() {
            aiOpponent.style.animation = '';
        }

        function resetDance() {
            isDancing = false;
            pausedDuration = 0;
            document.getElementById('playPause').textContent = 'Play';
            document.getElementById('progress').style.width = '0%';
            userDancer.style.transform = '';
            stopAIAnimation();
        }

        function formatTime(seconds) {
            const mins = Math.floor(seconds / 60);
            const secs = Math.floor(seconds % 60);
            return `${mins}:${secs.toString().padStart(2, '0')}`;
        }
    </script>
</body>
</html>
