# miniaviator.md
aviator india game
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Mini Aviator Game Enhanced</title>
<style>
  body {
    background: #121212;
    color: #fff;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    margin: 0;
  }
  h1 {
    margin-bottom: 0.2em;
  }
  #multiplier {
    font-size: 5rem;
    margin-bottom: 0.5em;
    color: #00ff00;
    transition: color 0.3s ease;
  }
  #multiplier.win {
    animation: pulse 1s infinite alternate;
  }
  #status {
    font-size: 1.3rem;
    margin-bottom: 1em;
    min-height: 1.5em;
  }
  .buttons {
    margin-top: 1em;
  }
  button {
    background: #ff4500;
    color: #fff;
    border: none;
    padding: 1em 2em;
    font-size: 1.2rem;
    border-radius: 8px;
    cursor: pointer;
    margin: 0 10px;
    transition: background-color 0.3s ease;
  }
  button:disabled {
    background: #555;
    cursor: not-allowed;
  }
  #balance, #highScore {
    margin: 0.3em 0;
    font-weight: bold;
  }
  input[type="number"] {
    width: 80px;
    font-size: 1.1rem;
    padding: 0.2em 0.5em;
    margin-left: 10px;
    border-radius: 6px;
    border: none;
    text-align: center;
  }

  @keyframes pulse {
    0% { transform: scale(1); color: #00ff00; }
    100% { transform: scale(1.1); color: #00cc00; }
  }
</style>
</head>
<body>
  <h1>Mini Aviator Game</h1>
  <div id="multiplier">1.00x</div>
  <div id="status">Press "Start" to play</div>

  <div>
    <label for="betInput">Bet coins:</label>
    <input type="number" id="betInput" min="1" value="10" />
  </div>
  <div id="balance">Balance: 100 coins</div>
  <div id="highScore">High score: 1.00x</div>

  <div class="buttons">
    <button id="startBtn">Start</button>
    <button id="cashoutBtn" disabled>Cash Out</button>
  </div>

<script>
  const multiplierEl = document.getElementById('multiplier');
  const statusEl = document.getElementById('status');
  const startBtn = document.getElementById('startBtn');
  const cashoutBtn = document.getElementById('cashoutBtn');
  const balanceEl = document.getElementById('balance');
  const highScoreEl = document.getElementById('highScore');
  const betInput = document.getElementById('betInput');

  let multiplier = 1;
  let interval;
  let crashed = false;
  let crashPoint;
  let balance = 100;
  let bet = 10;
  let highScore = parseFloat(localStorage.getItem('aviatorHighScore')) || 1;

  highScoreEl.textContent = `High score: ${highScore.toFixed(2)}x`;
  balanceEl.textContent = `Balance: ${balance} coins`;

  // Sound effects
  const sounds = {
    start: new Audio('https://actions.google.com/sounds/v1/cartoon/slide_whistle_to_drum_hit.ogg'),
    cashout: new Audio('https://actions.google.com/sounds/v1/cartoon/clang_and_wobble.ogg'),
    crash: new Audio('https://actions.google.com/sounds/v1/alarms/beep_short.ogg'),
  };

  function playSound(name) {
    if (sounds[name]) {
      sounds[name].currentTime = 0;
      sounds[name].play();
    }
  }

  function startGame() {
    bet = parseInt(betInput.value);
    if (isNaN(bet) || bet < 1) {
      alert('Please enter a valid bet (minimum 1 coin).');
      return;
    }
    if (bet > balance) {
      alert('You do not have enough coins for this bet.');
      return;
    }

    multiplier = 1;
    crashed = false;
    crashPoint = (Math.random() * 4) + 1; // Crash between 1x and 5x
    multiplierEl.textContent = multiplier.toFixed(2) + 'x';
    multiplierEl.classList.remove('win');
    multiplierEl.style.color = '#00ff00';
    statusEl.textContent = 'Game started!';

    startBtn.disabled = true;
    cashoutBtn.disabled = false;
    betInput.disabled = true;

    balance -= bet;
    updateBalance();

    playSound('start');

    interval = setInterval(() => {
      if (crashed) return;

      multiplier += 0.01;
      multiplierEl.textContent = multiplier.toFixed(2) + 'x';

      if (multiplier >= crashPoint) {
        crash();
      }
    }, 50);
  }

  function crash() {
    crashed = true;
    clearInterval(interval);
    multiplierEl.style.color = '#ff0000';
    statusEl.textContent = `Crashed at ${multiplier.toFixed(2)}x! You lost ${bet} coins.`;
    startBtn.disabled = false;
    cashoutBtn.disabled = true;
    betInput.disabled = false;
    playSound('crash');
  }

  function cashOut() {
    if (crashed) return;
    clearInterval(interval);

    const winnings = Math.floor(bet * multiplier);
    balance += winnings;
    updateBalance();

    statusEl.textContent = `You cashed out at ${multiplier.toFixed(2)}x and won ${winnings} coins!`;
    multiplierEl.style.color = '#00ff00';
    multiplierEl.classList.add('win');

    if (multiplier > highScore) {
      highScore = multiplier;
      localStorage.setItem('aviatorHighScore', highScore);
      highScoreEl.textContent = `High score: ${highScore.toFixed(2)}x 🎉`;
    }

    startBtn.disabled = false;
    cashoutBtn.disabled = true;
    betInput.disabled = false;
    playSound('cashout');
  }

  function updateBalance() {
    balanceEl.textContent = `Balance: ${balance} coins`;
  }

  startBtn.addEventListener('click', () => {
    multiplierEl.classList.remove('win');
    startGame();
  });

  cashoutBtn.addEventListener('click', cashOut);
</script>
</body>
</html>
