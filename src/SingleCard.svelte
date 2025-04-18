<script>
    export let card;
    export let imgCover;
    export let flipped = false;
    export let disabled = false;
    export let matched = false;
    export let handleChoice;
    
    function handleClick() {
      if (!disabled && !flipped) {
        handleChoice(card);
      }
    }
    
    // Accessible keyboard navigation
    function handleKeyDown(event) {
      if (event.key === 'Enter' || event.key === ' ') {
        if (!disabled && !flipped) {
          handleChoice(card);
        }
        event.preventDefault();
      }
    }
  </script>
  
  <div 
    class="card" 
    class:flipped={flipped} 
    class:matched={matched}
    on:click={handleClick}
    on:keydown={handleKeyDown}
    tabindex="0"
    role="button"
    aria-label={flipped ? "Card flipped" : "Card face down"}
    aria-pressed={flipped}
  >
    <div class="card-inner">
      <div class="card-front">
        <img src={card.src} alt="Card front" />
      </div>
      <div class="card-back">
        <img src={imgCover} alt="Card back" />
      </div>
    </div>
  </div>
  
  <style>
    .card {
      position: relative;
      cursor: pointer;
      aspect-ratio: 3/4;
      border-radius: 8px;
      overflow: hidden;
      transition: transform 0.15s ease-out;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    }
  
    .card:hover:not(.flipped) {
      transform: translateY(-5px);
      box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
    }
    
    .card:focus {
      outline: 3px solid #c23866;
      box-shadow: 0 0 0 3px rgba(194, 56, 102, 0.5);
    }
  
    .card-inner {
      position: relative;
      width: 100%;
      height: 100%;
      transform-style: preserve-3d;
      transition: transform 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }
  
    .flipped .card-inner {
      transform: rotateY(180deg);
    }
  
    .card-front, .card-back {
      position: absolute;
      width: 100%;
      height: 100%;
      backface-visibility: hidden;
      display: flex;
      align-items: center;
      justify-content: center;
      border-radius: 8px;
    }
  
    .card-front {
      background: #fff;
      transform: rotateY(180deg);
      padding: 10px;
    }
  
    .card-back {
      background: #c23866;
      transform: rotateY(0deg);
    }
  
    .matched {
      animation: matched 0.5s ease forwards;
      box-shadow: 0 0 15px rgba(76, 175, 80, 0.7);
    }
  
    img {
      width: 100%;
      height: 100%;
      object-fit: contain;
    }
  
    @keyframes matched {
      0% { 
        box-shadow: 0 0 5px 2px rgba(76, 175, 80, 0.2);
      }
      50% { 
        box-shadow: 0 0 15px 5px rgba(76, 175, 80, 0.7);
      }
      100% { 
        box-shadow: 0 0 5px 2px rgba(76, 175, 80, 0.3);
      }
    }
  </style>