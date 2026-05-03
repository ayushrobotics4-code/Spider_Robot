#include <Wire.h>
#include <Adafruit_PWMServoDriver.h>
#include <WiFi.h>
#include <ESPAsyncWebServer.h>

// --- WIFI CONFIGURATION ---
const char* ap_ssid = "Spider Robot controller"; 

// --- PIN CONFIGURATION ---
const int TRIG_PIN = 5;
const int ECHO_PIN = 18;
const int DIST_THRESHOLD = 20; 

Adafruit_PWMServoDriver pwm = Adafruit_PWMServoDriver();
AsyncWebServer server(80);

// --- YOUR SPIDER-OS HTML CODE ---
const char index_html[] PROGMEM = R"rawliteral(
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Spider-Bot Tactical HUD</title>
    <style>
        :root { --spider-red: #ff002b; --spider-black: #050505; --spider-blue: #001d4a; --glow: 0 0 15px rgba(255, 0, 43, 0.9); }
        body { background-color: var(--spider-black); background-image: radial-gradient(circle at 10% 10%, rgba(255,255,255,0.05) 1px, transparent 1px), linear-gradient(30deg, transparent 49%, rgba(255,255,255,0.1) 50%, transparent 51%), linear-gradient(-30deg, transparent 49%, rgba(255,255,255,0.1) 50%, transparent 51%); background-size: 100% 100%, 50px 50px, 50px 50px; color: white; font-family: 'Segoe UI', sans-serif; margin: 0; display: flex; flex-direction: column; align-items: center; overflow: hidden; height: 100vh; user-select: none; -webkit-user-select: none; }
        #eye-overlay { position: fixed; top: 30%; left: 50%; transform: translate(-50%, -50%); width: 100vw; display: flex; justify-content: center; gap: 15px; pointer-events: none; z-index: 1000; opacity: 0; transition: opacity 0.2s ease-in-out; }
        .eye-frame { width: 170px; height: 85px; background: #000; display: flex; align-items: center; justify-content: center; clip-path: polygon(0% 20%, 30% 0%, 100% 40%, 100% 100%, 40% 100%, 0% 80%); filter: drop-shadow(0 0 10px var(--spider-red)); }
        .eye-glow { width: 88%; height: 85%; background: var(--spider-red); clip-path: polygon(5% 25%, 32% 8%, 95% 45%, 95% 95%, 42% 95%, 5% 75%); box-shadow: inset 0 0 20px rgba(0,0,0,0.9); }
        .eye-frame.right { transform: scaleX(-1); }
        .attack-active #eye-overlay { opacity: 1; }
        #sticky-overlay, #aim-overlay { position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.85); z-index: 200; display: flex; flex-direction: column; justify-content: center; align-items: center; backdrop-filter: blur(8px); }
        .btn-main { background: var(--spider-red); color: white; border: none; padding: 18px 35px; border-radius: 40px; font-weight: 900; font-size: 16px; cursor: pointer; box-shadow: var(--glow); }
        #aim-ui { display: flex; flex-direction: column; align-items: center; gap: 20px; }
        .aim-row { display: flex; gap: 25px; }
        .aim-btn { width: 75px; height: 75px; border: 2px solid var(--spider-red); background: transparent; color: white; border-radius: 50%; font-size: 28px; }
        .header { margin-top: 15px; text-align: center; }
        h1 { color: var(--spider-red); font-size: 20px; text-shadow: var(--glow); margin: 0; }
        #status-text { font-size: 11px; height: 20px; color: #aaa; margin-top: 5px; text-transform: uppercase; }
        .control-area { flex-grow: 1; display: flex; justify-content: center; align-items: center; width: 100%; }
        .hidden { display: none !important; }
        #button-container { display: grid; grid-template-columns: repeat(3, 85px); gap: 12px; }
        .dir-btn { width: 85px; height: 85px; background: rgba(255,255,255,0.05); border: 2px solid var(--spider-red); color: white; border-radius: 20px; font-size: 26px; }
        #joy-base { width: 200px; height: 200px; border: 2px solid var(--spider-red); border-radius: 50%; position: relative; }
        #joy-stick { width: 70px; height: 70px; background: var(--spider-red); border-radius: 50%; position: absolute; left: 65px; top: 65px; box-shadow: var(--glow); touch-action: none; }
        .action-tray { background: var(--spider-blue); width: 100%; padding: 15px 5px; display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; border-top: 2px solid var(--spider-red); }
        .action-btn { background: rgba(255,255,255,0.1); border: 1px solid rgba(255,255,255,0.2); color: white; padding: 12px 0; border-radius: 6px; font-size: 10px; font-weight: bold; }
    </style>
</head>
<body>
    <div id="eye-overlay"><div class="eye-frame left"><div class="eye-glow"></div></div><div class="eye-frame right"><div class="eye-glow"></div></div></div>
    <div id="sticky-overlay" class="hidden"><p id="sticky-title" style="color:var(--spider-red); margin-bottom:20px; font-weight:bold; letter-spacing:2px;">AUTO-DRIVE ACTIVE</p><button class="btn-main" onclick="stopEverything()">RELEASE COMMAND</button></div>
    <div id="aim-overlay" class="hidden"><button id="override-trigger" class="btn-main" onclick="showAimControls()" style="background:white; color:black; margin-bottom:30px;">MANUAL OVERRIDE</button><div id="aim-ui" class="hidden"><div class="aim-row"><button class="aim-btn" onpointerdown="send('aim_left')" onpointerup="send('aim_stop')">◀</button><button class="btn-main" onclick="executeFinalAttack()">RELEASE</button><button class="aim-btn" onpointerdown="send('aim_right')" onpointerup="send('aim_stop')">▶</button></div></div></div>
    <div class="header"><h1>SPIDER-OS v6.0</h1><div id="status-text">Ready...</div><button onclick="toggleMode()" style="background:transparent; color:#666; border:1px solid #444; border-radius:10px; font-size:9px; padding:3px 10px; margin-top:5px;">INPUT: JOYSTICK/BUTTONS</button></div>
    <div class="control-area"><div id="joystick-container"><div id="joy-base"><div id="joy-stick"></div></div></div><div id="button-container" class="hidden"><div style="grid-column: 2"><button class="dir-btn" onpointerdown="startPress('forward')" onpointerup="endPress()">▲</button></div><button class="dir-btn" onpointerdown="startPress('left')" onpointerup="endPress()" style="grid-row: 2">◀</button><button class="dir-btn" onclick="stopEverything()" style="grid-row: 2; background: #222;">■</button><button class="dir-btn" onpointerdown="startPress('right')" onpointerup="endPress()" style="grid-row: 2">▶</button><div style="grid-column: 2"><button class="dir-btn" onpointerdown="startPress('backward')" onpointerup="endPress()">▼</button></div></div></div>
    <div class="action-tray"><button class="action-btn" onclick="send('wave')">WAVE</button><button class="action-btn" onclick="send('dance')">DANCE</button><button class="action-btn" onclick="startAttackSequence()" style="color:var(--spider-red); border-color:var(--spider-red);">ATTACK</button><button class="action-btn" onclick="send('web')">WEB</button><button class="action-btn" onclick="send('sit')">REST</button><button class="action-btn" onclick="send('patrol')">PATROL</button><button class="action-btn" onclick="send('pushup')">FITNESS</button><button class="action-btn" onclick="stopEverything()" style="color:#ff6b6b">STOP</button></div>
    <script>
        const statusText = document.getElementById('status-text'); const overlayContainer = document.body; const stickyOverlay = document.getElementById('sticky-overlay'); const aimOverlay = document.getElementById('aim-overlay'); const aimUI = document.getElementById('aim-ui'); const overrideTrigger = document.getElementById('override-trigger'); const joyContainer = document.getElementById('joystick-container'); const btnContainer = document.getElementById('button-container'); const stick = document.getElementById('joy-stick'); const base = document.getElementById('joy-base'); let lastCmd = "stop", pressTimer, attackCountdown, isSticky = false, isAiming = false, isMoving = false; function toggleMode() { joyContainer.classList.toggle('hidden'); btnContainer.classList.toggle('hidden'); stopEverything(); } function send(cmd) { if (cmd !== lastCmd) { statusText.innerText = "COMMAND: " + cmd.toUpperCase(); fetch(`/move?dir=${cmd}`).catch(() => {}); lastCmd = cmd; } } function startPress(cmd) { send(cmd); pressTimer = setTimeout(() => { isSticky = true; stickyOverlay.classList.remove('hidden'); }, 1000); } function endPress() { clearTimeout(pressTimer); if (!isSticky) send('stop'); } function startAttackSequence() { overlayContainer.classList.add('attack-active'); setTimeout(() => { overlayContainer.classList.remove('attack-active'); }, 3000); aimOverlay.classList.remove('hidden'); overrideTrigger.classList.remove('hidden'); aimUI.classList.add('hidden'); isAiming = false; attackCountdown = setTimeout(() => { if (!isAiming) executeFinalAttack(); }, 5000); } function showAimControls() { isAiming = true; clearTimeout(attackCountdown); overrideTrigger.classList.add('hidden'); aimUI.classList.remove('hidden'); } function executeFinalAttack() { send('attack_execute'); setTimeout(() => { aimOverlay.classList.add('hidden'); }, 500); } function stopEverything() { clearTimeout(pressTimer); clearTimeout(attackCountdown); isSticky = false; isAiming = false; overlayContainer.classList.remove('attack-active'); stickyOverlay.classList.add('hidden'); aimOverlay.classList.add('hidden'); send('stop'); } function handleJoy(e) { if (!isMoving || isSticky) return; const rect = base.getBoundingClientRect(); const cx = rect.left + rect.width / 2, cy = rect.top + rect.height / 2; const clientX = e.touches ? e.touches[0].clientX : e.clientX; const clientY = e.touches ? e.touches[0].clientY : e.clientY; const dx = clientX - cx, dy = clientY - cy; const dist = Math.min(Math.sqrt(dx*dx + dy*dy), 70); const angle = Math.atan2(dy, dx); stick.style.transform = `translate(${Math.cos(angle)*dist}px, ${Math.sin(angle)*dist}px)`; if (dist > 30) { const deg = angle * 180 / Math.PI; if (deg > -45 && deg <= 45) send('right'); else if (deg > 45 && deg <= 135) send('backward'); else if (deg > -135 && deg <= -45) send('forward'); else send('left'); } else send('stop'); } stick.addEventListener('pointerdown', (e) => { isMoving = true; e.target.setPointerCapture(e.pointerId); }); stick.addEventListener('pointermove', handleJoy); stick.addEventListener('pointerup', () => { isMoving = false; stick.style.transform = 'none'; send('stop'); });
    </script>
</body>
</html>
)rawliteral";

// --- SERVO CONTROL LOGIC ---
void setAngle(int servoNum, int angle) {
  int pulse = map(angle, 0, 180, 150, 600);
  pwm.setPWM(servoNum, 0, pulse);
}

void robotStand() {
  for (int i = 0; i < 12; i++) setAngle(i, 90);
}

void moveBackward(int steps) {
  for (int s = 0; s < steps; s++) {
    setAngle(6, 120); setAngle(9, 120); 
    delay(200);
    setAngle(7, 60); setAngle(10, 60);  
    delay(200);
    setAngle(6, 90); setAngle(9, 90);   
    delay(200);
    robotStand(); 
    delay(200);
  }
}

void attackStance() {
  setAngle(0, 160); setAngle(1, 160); 
  setAngle(3, 160); setAngle(4, 160); 
}

// --- SETUP ---
void setup() {
  Serial.begin(115200);
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  pwm.begin();
  pwm.setPWMFreq(60);
  robotStand();

  // ACCESS POINT MODE INITIALIZATION
  WiFi.softAP(ap_ssid);
  
  Serial.println("\nSpider-OS Online in AP Mode!");
  Serial.print("SSID: "); Serial.println(ap_ssid);
  Serial.print("IP: "); Serial.println(WiFi.softAPIP());

  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send_P(200, "text/html", index_html);
  });

  server.on("/move", HTTP_GET, [](AsyncWebServerRequest *request){
    if (request->hasParam("dir")) {
      String command = request->getParam("dir")->value();
      if (command == "backward") { moveBackward(1); }
      else if (command == "stop") { robotStand(); }
      else if (command == "attack_execute") { attackStance(); }
    }
    request->send(200, "text/plain", "OK");
  });

  server.begin();
}

void loop() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duration = pulseIn(ECHO_PIN, HIGH);
  int distance = duration * 0.034 / 2;

  if (distance > 0 && distance < DIST_THRESHOLD) {
    robotStand(); 
    delay(100);
    moveBackward(2); 
    robotStand();
    delay(1500); 
  }
  delay(50); 
}
