
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Nhận Lì Xì</title>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(145deg, #fffae3, #ffe4c1);
      text-align: center;
      padding: 20px;
      overflow: hidden;
      animation: backgroundShift 10s infinite alternate;
    }

    @keyframes backgroundShift {
      0% { background: linear-gradient(145deg, #fffae3, #ffe4c1); }
      100% { background: linear-gradient(145deg, #ffd5a8, #fffae3); }
    }

    .lixi-container {
      width: 250px;
      height: 500px;
      background-color: #fff;
      border-radius: 15px;
      border: 5px solid #ffba08;
      position: relative;
      box-shadow: 0 6px 15px rgba(0, 0, 0, 0.1);
      margin: 50px auto;
      padding: 20px;
      text-align: center;
    }

    .button {
      width: 70px;
      height: 70px;
      background-color: #ffba08;
      border-radius: 50%;
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      border: none;
      cursor: pointer;
      box-shadow: 0px 6px 15px rgba(0, 0, 0, 0.2);
      animation: pulse 1.5s infinite alternate;
    }

    .button:hover {
      transform: translate(-50%, -50%) scale(1.1);
      box-shadow: 0px 8px 20px rgba(0, 0, 0, 0.3);
    }

    @keyframes pulse {
      0% { transform: translate(-50%, -50%) scale(1); }
      100% { transform: translate(-50%, -50%) scale(1.1); }
    }

    .message {
      margin-top: 30px;
      font-size: 1.4em;
      font-weight: bold;
      color: #4a4e69;
      display: none;
      animation: messageAppear 1.5s forwards;
    }

    @keyframes messageAppear {
      0% { opacity: 0; transform: translateY(20px); }
      100% { opacity: 1; transform: translateY(0); }
    }

    .btn-refresh {
      background: linear-gradient(145deg, #ffba08, #faa307);
      color: white;
      padding: 12px 20px;
      border: none;
      border-radius: 25px;
      font-size: 1.1em;
      cursor: pointer;
      display: none;
      margin-top: 20px;
      animation: buttonGlow 1.5s infinite alternate;
    }

    .btn-refresh:hover {
      transform: scale(1.1);
      box-shadow: 0px 8px 15px rgba(0, 0, 0, 0.3);
    }

    @keyframes buttonGlow {
      0% { box-shadow: 0px 0px 10px #faa307; }
      100% { box-shadow: 0px 0px 20px #ffba08; }
    }

    .firework {
      position: absolute;
      width: 10px;
      height: 10px;
      background: radial-gradient(circle, #ffba08, #f72585);
      border-radius: 50%;
      animation: fireworks 1s infinite ease-out;
      pointer-events: none;
    }

    #fireworks-container {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      pointer-events: none;
    }

    .key-input-container {
      margin-top: 20px;
      perspective: 1000px;
    }

    .key-input {
      padding: 15px;
      font-size: 1.2em;
      width: 100%;
      margin-bottom: 20px;
      border-radius: 8px;
      border: 1px solid #ddd;
      box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
      transform-style: preserve-3d;
      transition: transform 0.3s ease-out;
    }

    .key-input:focus {
      outline: none;
      transform: rotateY(15deg);
      box-shadow: 0 5px 25px rgba(0, 0, 0, 0.2);
    }

    .key-submit {
      background-color: #ffba08;
      padding: 12px 25px;
      border-radius: 5px;
      cursor: pointer;
      font-size: 1.1em;
      border: none;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
      transition: transform 0.3s ease-out;
    }

    .key-submit:hover {
      transform: scale(1.05);
      box-shadow: 0 6px 15px rgba(0, 0, 0, 0.3);
    }

    /* Thêm kiểu chữ in đậm và phong cách mới */
    .key-input, .message {
      font-family: 'Courier New', monospace;
      letter-spacing: 2px;
      font-weight: bold; /* In đậm toàn bộ chữ */
    }

    .key-input {
      font-size: 1.2em;
      color: #4a4e69;
    }

    .message {
      font-size: 1.3em;
      color: #f72585;
      font-family: 'Courier New', monospace;
    }
    
    .btn-refresh {
      font-family: 'Courier New', monospace;
      font-weight: bold;
      text-transform: uppercase;
    }

    b {
      font-weight: bold;  /* Đảm bảo chữ trong thẻ <b> được in đậm */
    }

  </style>
</head>
<body>

  <div class="lixi-container">
    <div class="key-input-container">
      <input type="text" class="key-input" id="keyInput" placeholder="Nhập Mã Đi Pé">
    </div>
    <button class="key-submit" onclick="validateKey()">Mở Lì Xì ❄</button>
    <div class="message" id="message"></div>
    <button class="btn-refresh" id="refresh" onclick="refreshPage()">Lấy Lì Xì</button>
    <div id="fireworks-container"></div>
  </div>

  <script>
    let hasOpened = false;  
    let ipAddress = null;   
    let availableKeys = {}; // Để lưu trữ các key và số lượng lượt sử dụng

    // Lấy danh sách key từ file
    fetch("key.txt")
      .then(response => response.text())
      .then(data => {
        const keys = data.split("\n");
        keys.forEach(key => {
          const [keyValue, quantity] = key.split(":");
          availableKeys[keyValue] = parseInt(quantity);
        });
      });

    function createFirework() {
      const firework = document.createElement("div");
      firework.classList.add("firework");
      firework.style.left = Math.random() * 100 + "%";
      firework.style.top = Math.random() * 100 + "%";
      document.getElementById("fireworks-container").appendChild(firework);

      setTimeout(() => firework.remove(), 1000);
    }

    function launchFireworks() {
      setInterval(createFirework, 200);
    }

    function validateKey() {
      const key = document.getElementById("keyInput").value;

      if (hasOpened) {
        alert("Bạn đã mở lì xì rồi, chỉ một lần duy nhất!");
        return;
      }

      if (key && availableKeys[key] > 0) {
        const min = 1000;
        const max = 20000;
        const amount = Math.floor(Math.random() * (max - min + 1)) + min;

        const messageDiv = document.getElementById("message");
        const refreshButton = document.getElementById("refresh");

        messageDiv.style.display = "block";
        refreshButton.style.display = "inline-block";
        messageDiv.innerHTML = `🎉 Chúc mừng!Pé Trúng Được Lì Xì <strong style="color: #d62828;">${amount.toLocaleString()}đ</strong>!`;

        document.getElementById("keyInput").style.display = "none";  
        document.querySelector(".key-submit").style.display = "none";

        hasOpened = true;

        // Giảm lượt sử dụng của key và lưu lại
        availableKeys[key] -= 1;
        if (availableKeys[key] === 0) {
          delete availableKeys[key];
        }
        updateKeyFile();

        launchFireworks();
      } else {
        alert("Mã Không Đúng! Vui Lòng Nhắn Tin ( 𝙆𝙎𝙊𝙍 𝙃𝙐𝘼𝙉 ᴀᴅᴍɪɴ 💸 ) Để Lấy Mã");
      }
    }

    function updateKeyFile() {
      const keyFileContent = Object.entries(availableKeys)
        .map(([key, quantity]) => `${key}:${quantity}`)
        .join("\n");

      // Gửi nội dung về server (đây là mô phỏng, bạn cần làm điều này với một server backend thực sự)
      console.log(keyFileContent); 
    }

    function refreshPage() {
      window.location.reload();
    }
  </script>
</body>
</html>
