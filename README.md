# Sheina-
We had so much love in ours that we didn't know how to support it❤️‍🩹
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Página secreta para mi novia ❤️</title>
  <style>
    body { font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif; padding:20px; background:#f7f7fb; color:#222; text-align:center; }
    .card { max-width:560px; margin:20px auto; background:white; border-radius:12px; padding:20px; box-shadow:0 8px 24px rgba(0,0,0,0.06); }
    video { width:100%; border-radius:8px; background:#000; }
    #qr-img { width:200px; height:200px; margin: 12px auto; display:block; }
    .hidden { display:none; }
    button { padding:10px 14px; border-radius:8px; border:0; background:#3b82f6; color:white; font-weight:600; cursor:pointer; }
    small { color:#666; display:block; margin-top:8px; }
    #secretContent { text-align:left; }
  </style>
  <!-- Librería de escaneo QR (cliente) -->
  <script src="https://unpkg.com/qr-scanner@1.4.2/qr-scanner.min.js"></script>
</head>
<body>
  <div class="card">
    <h1>Entrada secreta 💌</h1>
    <p>Para entrar a la sorpresa, escaneá el código QR con la cámara. Si el código coincide, se desbloquea la página.</p>

    <!-- Ejemplo de QR (reemplazá el texto CODIGOSECRETO123 por lo que quieras) -->
    <img id="qr-img" alt="QR de ejemplo" src="https://chart.googleapis.com/chart?chs=300x300&cht=qr&chl=CODIGOSECRETO123&choe=UTF-8">

    <p><strong>¿No podés usar la cámara?</strong></p>
    <input type="file" id="file-input" accept="image/*"><br><small>Subí la foto del QR si preferís</small>

    <hr>

    <div id="scanner-area">
      <video id="video" muted playsinline></video>
      <div style="margin-top:10px">
        <button id="startBtn">Iniciar cámara</button>
        <button id="stopBtn" class="hidden">Detener</button>
      </div>
      <small id="status">Estado: esperando inicio.</small>
    </div>

    <hr>

    <div id="result" class="hidden">
      <h2>Escaneado ✔️</h2>
      <p id="scannedText"></p>
      <p>Si el código es correcto, verás la sorpresa abajo.</p>
    </div>

    <div id="locked" style="margin-top:12px;">
      <h3>Estado: 🔒 Bloqueado</h3>
      <p>Escaneá el QR correcto para desbloquear.</p>
    </div>

    <div id="unlocked" class="hidden" style="margin-top:14px;">
      <h3>¡Bienvenida! 🔓</h3>
      <div id="secretContent">
        <!-- Personalizá el contenido de la sorpresa acá -->
        <h2>Para mi amor, <span id="nombre">[Nombre]</span> 💖</h2>
        <p>Te amo. Gracias por ser tan especial. Esto es solo para vos. 😘</p>
        <p>Puedes cambiar este mensaje en el HTML para hacerlo más personal (añadí fotos, música, etc.).</p>
      </div>
    </div>

  </div>

<script>
/*
  Cómo funciona:
  - Definí el SECRETCODE abajo (string exacto). Generá un QR que contenga exactamente ese texto.
  - El ejemplo de imagen usa: CODIGOSECRETO123
  - Cambiá SECRETCODE por lo que quieras y regenerá el QR (o reemplazá la imagen QR).
*/
const SECRETCODE = "CODIGOSECRETO123"; // <-- PONÉ TU CÓDIGO AQUÍ (sensitivo a mayúsculas/minúsculas)

const video = document.getElementById('video');
const startBtn = document.getElementById('startBtn');
const stopBtn = document.getElementById('stopBtn');
const status = document.getElementById('status');
const result = document.getElementById('result');
const scannedText = document.getElementById('scannedText');
const locked = document.getElementById('locked');
const unlocked = document.getElementById('unlocked');
const fileInput = document.getElementById('file-input');

let qrScanner = null;

QrScanner.WORKER_PATH = 'https://unpkg.com/qr-scanner@1.4.2/qr-scanner-worker.min.js';

// Iniciar cámara y escaneo
startBtn.addEventListener('click', async () => {
  startBtn.disabled = true;
  status.textContent = 'Estado: inicializando cámara...';
  try {
    qrScanner = new QrScanner(video, resultText => {
      onScan(resultText);
    }, { returnDetailedScanResult: true });

    await qrScanner.start();
    status.textContent = 'Estado: cámara activa. Apuntá al QR.';
    startBtn.classList.add('hidden');
    stopBtn.classList.remove('hidden');
  } catch (e) {
    console.error(e);
    status.textContent = 'Error al iniciar cámara. Probá con la opción de subir imagen.';
    startBtn.disabled = false;
  }
});

stopBtn.addEventListener('click', () => {
  if (qrScanner) {
    qrScanner.stop();
    qrScanner.destroy();
    qrScanner = null;
  }
  status.textContent = 'Estado: detenido.';
  startBtn.classList.remove('hidden');
  stopBtn.classList.add('hidden');
  startBtn.disabled = false;
});

// Manejo de imagen subida (fallback)
fileInput.addEventListener('change', async (e) => {
  const f = e.target.files[0];
  if (!f) return;
  status.textContent = 'Estado: analizando imagen...';
  try {
    const result = await QrScanner.scanImage(f, { returnDetailedScanResult: true });
    onScan(result.data || result);
  } catch(err) {
    console.error(err);
    status.textContent = 'No se detectó un QR válido en la imagen.';
  }
});

function onScan(text) {
  result.classList.remove('hidden');
  scannedText.textContent = Texto detectado: "${text}";
  status.textContent = 'Estado: QR detectado.';
  // comparar con el secreto (match exacto)
  if (String(text).trim() === SECRETCODE) {
    unlock();
  } else {
    fail();
  }
}

function unlock() {
  locked.classList.add('hidden');
  unlocked.classList.remove('hidden');
  status.textContent = 'Estado: desbloqueado 🎉';
  // detener cámara si está encendida
  if (qrScanner) {
    qrScanner.stop();
    qrScanner.destroy();
    qrScanner = null;
    startBtn.classList.remove('hidden');
    stopBtn.classList.add('hidden');
  }
}

function fail() {
  locked.classList.remove('hidden');
  unlocked.classList.add('hidden');
  status.textContent = 'Estado: código incorrecto. Intentá otra vez.';
}

// Opcional: personalizar nombre en el contenido desbloqueado
document.getElementById('nombre').textContent = "Mi amor"; // cambiá si querés
</script>
</body>
</html>
