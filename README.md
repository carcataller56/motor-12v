<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Activador Motor Remoto</title>
  <script src="https://unpkg.com/mqtt/dist/mqtt.min.js"></script>
  <style>
    body { font-family: -apple-system, BlinkMacSystemFont, sans-serif; background: #121212; color: #fff; text-align: center; padding: 25px 15px; margin: 0; }
    .card { max-width: 380px; margin: 0 auto; background: #1e1e1e; padding: 25px; border-radius: 16px; box-shadow: 0 4px 15px rgba(0,0,0,0.5); }
    h2 { margin-top: 0; color: #e0e0e0; font-size: 22px; }
    input[type="password"] { width: 80%; padding: 12px; font-size: 20px; text-align: center; border-radius: 8px; border: 1px solid #444; background: #2a2a2a; color: #fff; letter-spacing: 6px; margin-bottom: 20px; outline: none; }
    .btn { width: 90%; background: #e74c3c; color: white; border: none; padding: 18px; font-size: 18px; font-weight: bold; border-radius: 10px; cursor: pointer; transition: 0.2s; }
    .btn:disabled { background: #555; cursor: not-allowed; }
    .btn:active:not(:disabled) { transform: scale(0.97); background: #c0392b; }
    #estado { margin-top: 20px; font-size: 15px; color: #aaa; }
  </style>
</head>
<body>
  <div class="card">
    <h2>Control de Motor 12V</h2>
    <p style="color: #888; font-size: 14px;">Ingresá el PIN de seguridad:</p>
    <input type="password" id="pinInput" maxlength="6" placeholder="••••">
    
    <button id="btnActivar" class="btn" onclick="intentarDisparo()" disabled>CONECTANDO...</button>
    <p id="estado">Estableciendo enlace seguro...</p>
  </div>

  <script>
    const PIN_CORRECTO = "1234"; // << CAMBIÁ TU CLAVE ACÁ
    const TOPIC = "casa/control/motor_secreto_77"; // << DEBE COINCIDIR CON EL ESP32
    
    const client = mqtt.connect('wss://broker.emqx.io:8084/mqtt');

    const btn = document.getElementById('btnActivar');
    const estado = document.getElementById('estado');

    client.on('connect', () => {
      btn.disabled = false;
      btn.innerText = "ACTIVAR MOTOR (5s)";
      estado.innerText = "Enlace listo. Esperando comando.";
      estado.style.color = "#2ecc71";
    });

    client.on('offline', () => {
      btn.disabled = true;
      btn.innerText = "DESCONECTADO";
      estado.innerText = "Reconectando al servidor...";
      estado.style.color = "#e74c3c";
    });

    function intentarDisparo() {
      const pin = document.getElementById('pinInput').value;
      if (pin !== PIN_CORRECTO) {
        alert("PIN incorrecto. Acceso denegado.");
        document.getElementById('pinInput').value = "";
        return;
      }

      btn.disabled = true;
      client.publish(TOPIC, 'PULSO_5S');
      estado.innerText = "¡Orden enviada con éxito!";
      estado.style.color = "#3498db";

      setTimeout(() => {
        btn.disabled = false;
        estado.innerText = "Enlace listo.";
        estado.style.color = "#2ecc71";
        document.getElementById('pinInput').value = "";
      }, 6000);
    }
  </script>
</body>
</html>
