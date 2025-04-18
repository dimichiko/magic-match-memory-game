<script>
	import { onMount } from 'svelte';
	import SingleCard from './SingleCard.svelte';
	
	// Game images
	let imgCover = 'https://raw.githubusercontent.com/iamshaunjp/React-Firebase/lesson-58/magic-memory/public/img/cover.png';
	let imgHelmet = 'https://raw.githubusercontent.com/iamshaunjp/React-Firebase/lesson-58/magic-memory/public/img/helmet-1.png';
	let imgPotion = 'https://raw.githubusercontent.com/iamshaunjp/React-Firebase/lesson-58/magic-memory/public/img/potion-1.png';
	let imgRing = 'https://raw.githubusercontent.com/iamshaunjp/React-Firebase/lesson-58/magic-memory/public/img/ring-1.png';
	let imgScroll = 'https://raw.githubusercontent.com/iamshaunjp/React-Firebase/lesson-58/magic-memory/public/img/scroll-1.png';
	let imgShield = 'https://raw.githubusercontent.com/iamshaunjp/React-Firebase/lesson-58/magic-memory/public/img/shield-1.png';
	let imgSword = 'https://raw.githubusercontent.com/iamshaunjp/React-Firebase/lesson-58/magic-memory/public/img/sword-1.png';
	
	// Additional images for hard mode
	let imgAxe = 'https://raw.githubusercontent.com/iamshaunjp/React-Firebase/lesson-58/magic-memory/public/img/sword-1.png'; // Replace with actual axe image
	let imgBow = 'https://raw.githubusercontent.com/iamshaunjp/React-Firebase/lesson-58/magic-memory/public/img/sword-1.png'; // Replace with actual bow image
	let imgStaff = 'https://raw.githubusercontent.com/iamshaunjp/React-Firebase/lesson-58/magic-memory/public/img/sword-1.png'; // Replace with actual staff image
	
	// Game state
	let cards = [];
	let turns = 0;
	let choiceOne = null;
	let choiceTwo = null;
	let disabled = false;
	let gameStarted = false;
	let gameWon = false;
	let score = 0;
	let timer = 0;
	let timerInterval;
	let difficulty = 'medium'; // easy, medium, hard
	let matchSound, flipSound, successSound, failSound, winSound;
	let isPlaying = false;
	let theme = 'dark'; // dark, light, fantasy
	
	// Card sets based on difficulty
	const cardSets = {
	  easy: [
		{ src: imgHelmet, matched: false },
		{ src: imgPotion, matched: false },
		{ src: imgRing, matched: false },
	  ],
	  medium: [
		{ src: imgHelmet, matched: false },
		{ src: imgPotion, matched: false },
		{ src: imgRing, matched: false },
		{ src: imgScroll, matched: false },
		{ src: imgShield, matched: false },
		{ src: imgSword, matched: false },
	  ],
	  hard: [
		{ src: imgHelmet, matched: false },
		{ src: imgPotion, matched: false },
		{ src: imgRing, matched: false },
		{ src: imgScroll, matched: false },
		{ src: imgShield, matched: false },
		{ src: imgSword, matched: false },
		{ src: imgAxe, matched: false },
		{ src: imgBow, matched: false },
		{ src: imgStaff, matched: false },
	  ]
	};
	
	onMount(() => {
	  // Initialize sounds
	  matchSound = new Audio('https://freesound.org/data/previews/220/220173_4100837-lq.mp3');
	  flipSound = new Audio('https://freesound.org/data/previews/240/240776_4107740-lq.mp3');
	  successSound = new Audio('https://freesound.org/data/previews/339/339912_5858296-lq.mp3');
	  failSound = new Audio('https://freesound.org/data/previews/340/340086_5858296-lq.mp3');
	  winSound = new Audio('https://freesound.org/data/previews/270/270402_5123851-lq.mp3');
	  
	  // Preload sounds
	  [matchSound, flipSound, successSound, failSound, winSound].forEach(sound => {
		sound.load();
		sound.volume = 0.5;
	  });
	  
	  // Set up initial game
	  shuffleCards();
	});
	
	// Start game timer
	function startTimer() {
	  if (timerInterval) clearInterval(timerInterval);
	  timer = 0;
	  timerInterval = setInterval(() => {
		timer++;
	  }, 1000);
	}
	
	// Stop timer
	function stopTimer() {
	  clearInterval(timerInterval);
	}
	
	// Format time as MM:SS
	function formatTime(seconds) {
	  const minutes = Math.floor(seconds / 60);
	  const remainingSeconds = seconds % 60;
	  return `${minutes.toString().padStart(2, '0')}:${remainingSeconds.toString().padStart(2, '0')}`;
	}
	
	// Calculate score based on turns, matches and time
	function calculateScore() {
	  const cardCount = cards.length / 2;
	  const baseScore = 1000;
	  const turnPenalty = turns * 10;
	  const timePenalty = timer * 2;
	  const difficultyBonus = difficulty === 'easy' ? 0 : difficulty === 'medium' ? 500 : 1000;
	  
	  return Math.max(0, baseScore - turnPenalty - timePenalty + difficultyBonus);
	}
	
	// Shuffle cards based on selected difficulty
	function shuffleCards() {
	  gameWon = false;
	  const selectedCardSet = cardSets[difficulty];
	  const shuffledCards = [...selectedCardSet, ...selectedCardSet]
		.sort(() => Math.random() - 0.5)
		.map((card) => ({ ...card, id: Math.random() }));
	  
	  cards = shuffledCards;
	  turns = 0;
	  choiceOne = null;
	  choiceTwo = null;
	  disabled = false;
	  
	  if (gameStarted) {
		stopTimer();
		startTimer();
	  }
	}
	
	// Start a new game
	function startNewGame() {
	  gameStarted = true;
	  shuffleCards();
	  startTimer();
	}
	
	// Handle card choice
	function handleChoice(card) {
	  if (flipSound && isPlaying) flipSound.play();
	  choiceOne ? (choiceTwo = card) : (choiceOne = card);
	}
	
	// Check if game is won
	function checkWinCondition() {
	  return cards.every(card => card.matched);
	}
	
	// Change difficulty
	function changeDifficulty(event) {
	  difficulty = event.target.value;
	  shuffleCards();
	}
	
	// Toggle sound
	function toggleSound() {
	  isPlaying = !isPlaying;
	}
	
	// Change theme
	function changeTheme(event) {
	  theme = event.target.value;
	}
	
	// Compare selected cards
	$: if (choiceOne && choiceTwo) {
	  disabled = true;
	  
	  if (choiceOne.src === choiceTwo.src) {
		// Cards match
		if (matchSound && isPlaying) matchSound.play();
		
		cards = cards.map(card => {
		  if (card.src === choiceOne.src) {
			return { ...card, matched: true };
		  } else {
			return card;
		  }
		});
		
		// Check for win after updating cards
		setTimeout(() => {
		  if (checkWinCondition()) {
			gameWon = true;
			stopTimer();
			score = calculateScore();
			if (winSound && isPlaying) winSound.play();
		  }
		  resetTurn();
		}, 300);
	  } else {
		// Cards don't match
		if (failSound && isPlaying) failSound.play();
		setTimeout(() => resetTurn(), 1000);
	  }
	}
	
	// Reset choices & increase turn
	function resetTurn() {
	  choiceOne = null;
	  choiceTwo = null;
	  turns++;
	  disabled = false;
	}
  </script>
  
  <div class="app {theme}">
	<header>
	  <h1>Magic Match</h1>
	  <div class="game-controls">
		<button class="primary-btn" on:click={startNewGame}>
		  New Game
		</button>
		
		<div class="control-group">
		  <label for="difficulty">Difficulty:</label>
		  <select id="difficulty" on:change={changeDifficulty} bind:value={difficulty}>
			<option value="easy">Easy (3 pairs)</option>
			<option value="medium">Medium (6 pairs)</option>
			<option value="hard">Hard (9 pairs)</option>
		  </select>
		</div>
		
		<div class="control-group">
		  <label for="theme">Theme:</label>
		  <select id="theme" on:change={changeTheme} bind:value={theme}>
			<option value="dark">Dark</option>
			<option value="light">Light</option>
			<option value="fantasy">Fantasy</option>
		  </select>
		</div>
		
		<button class="sound-btn" on:click={toggleSound}>
		  {isPlaying ? 'Sound: ON' : 'Sound: OFF'}
		</button>
	  </div>
	</header>
	
	<div class="game-stats">
	  <div class="stat">
		<span class="stat-label">Turns:</span> 
		<span class="stat-value">{turns}</span>
	  </div>
	  
	  <div class="stat">
		<span class="stat-label">Time:</span> 
		<span class="stat-value">{formatTime(timer)}</span>
	  </div>
	  
	  {#if gameWon}
		<div class="stat score">
		  <span class="stat-label">Score:</span> 
		  <span class="stat-value">{score}</span>
		</div>
	  {/if}
	</div>
	
	{#if gameWon}
	  <div class="win-message">
		<h2>Congratulations!</h2>
		<p>You've matched all cards in {turns} turns and {formatTime(timer)}!</p>
		<p>Your score: {score}</p>
		<button class="primary-btn" on:click={startNewGame}>
		  Play Again
		</button>
	  </div>
	{/if}
	
	<div class="card-grid" class:game-won={gameWon} style="--columns: {difficulty === 'easy' ? 3 : difficulty === 'medium' ? 4 : 6}">
	  {#each cards as card (card.id)}
		<SingleCard 
		  {card} 
		  {imgCover} 
		  {disabled}
		  {handleChoice}
		  flipped={card === choiceOne || card === choiceTwo || card.matched} 
		  matched={card.matched} />
	  {/each}
	</div>
	
	<footer>
	  <p>Press <kbd>Space</kbd> for new game. Use <kbd>Tab</kbd> and arrow keys to navigate.</p>
	</footer>
  </div>
  
  <style>
	:global(*) {
	  margin: 0;
	  padding: 0;
	  box-sizing: border-box;
	  transition: all 0.25s ease;
	}
	
	.app {
	  max-width: 1200px;
	  margin: 0 auto;
	  min-height: 100vh;
	  text-align: center;
	  padding: 1rem;
	  display: flex;
	  flex-direction: column;
	}
	
	/* Theme styles */
	.dark {
	  background: #1b1523;
	  color: white;
	}
	
	.light {
	  background: #f5f5f5;
	  color: #333;
	}
	
	.fantasy {
	  background: linear-gradient(to bottom, #2c3e50, #4c669f);
	  color: #ffe;
	}
	
	header {
	  margin-bottom: 2rem;
	}
	
	h1 {
	  font-size: 2.5rem;
	  margin-bottom: 1rem;
	  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
	}
	
	.game-controls {
	  display: flex;
	  justify-content: center;
	  align-items: center;
	  flex-wrap: wrap;
	  gap: 1rem;
	  margin-bottom: 1rem;
	}
	
	.control-group {
	  display: flex;
	  align-items: center;
	  gap: 0.5rem;
	}
	
	button, select {
	  background: none;
	  border: 2px solid;
	  padding: 8px 16px;
	  border-radius: 8px;
	  font-weight: bold;
	  cursor: pointer;
	  font-size: 1em;
	  transition: all 0.3s ease;
	}
	
	.dark button, .dark select {
	  border-color: #fff;
	  color: #fff;
	}
	
	.light button, .light select {
	  border-color: #333;
	  color: #333;
	}
	
	.fantasy button, .fantasy select {
	  border-color: #ffe;
	  color: #ffe;
	}
	
	.primary-btn:hover {
	  background: #c23866;
	  color: #fff;
	  transform: translateY(-2px);
	  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
	}
	
	.sound-btn:hover {
	  background: #2a9d8f;
	  color: #fff;
	}
	
	select {
	  background-color: rgba(255, 255, 255, 0.1);
	}
	
	.game-stats {
	  display: flex;
	  justify-content: center;
	  gap: 2rem;
	  margin-bottom: 1rem;
	  font-size: 1.2rem;
	}
	
	.stat {
	  padding: 0.5rem 1rem;
	  border-radius: 8px;
	  background-color: rgba(0, 0, 0, 0.2);
	}
	
	.stat-label {
	  font-weight: bold;
	  margin-right: 0.5rem;
	}
	
	.score {
	  background-color: rgba(194, 56, 102, 0.3);
	  animation: pulse 1.5s infinite;
	}
	
	.card-grid {
	  margin-top: 2rem;
	  display: grid;
	  grid-template-columns: repeat(var(--columns, 4), 1fr);
	  grid-gap: 15px;
	  perspective: 1000px;
	  margin-bottom: 2rem;
	}
	
	.game-won {
	  opacity: 0.7;
	}
	
	.win-message {
	  position: fixed;
	  top: 50%;
	  left: 50%;
	  transform: translate(-50%, -50%);
	  background: rgba(0, 0, 0, 0.85);
	  color: white;
	  padding: 2rem;
	  border-radius: 10px;
	  z-index: 100;
	  box-shadow: 0 0 20px rgba(194, 56, 102, 0.6);
	  animation: fadeIn 0.5s ease;
	}
	
	.win-message h2 {
	  color: #c23866;
	  margin-bottom: 1rem;
	}
	
	.win-message p {
	  margin-bottom: 1rem;
	}
	
	footer {
	  margin-top: auto;
	  padding: 1rem;
	  font-size: 0.9rem;
	  opacity: 0.7;
	}
	
	kbd {
	  background: rgba(255, 255, 255, 0.2);
	  padding: 2px 5px;
	  border-radius: 3px;
	  font-family: monospace;
	}
	
	/* Animations */
	@keyframes pulse {
	  0% { transform: scale(1); }
	  50% { transform: scale(1.05); }
	  100% { transform: scale(1); }
	}
	
	@keyframes fadeIn {
	  from { opacity: 0; }
	  to { opacity: 1; }
	}
	
	/* Mobile responsiveness */
	@media (max-width: 768px) {
	  .card-grid {
		grid-template-columns: repeat(auto-fit, minmax(80px, 1fr));
		grid-gap: 10px;
	  }
	  
	  .game-controls {
		flex-direction: column;
		align-items: stretch;
	  }
	  
	  .game-stats {
		flex-direction: column;
		gap: 0.5rem;
	  }
	}
  </style>