# Magic Match Memory Game

![Magic Match Game](https://img.shields.io/badge/Game-Memory-blue)
![Svelte](https://img.shields.io/badge/Built%20with-Svelte-orange)
![License](https://img.shields.io/badge/License-MIT-green)

A fun and engaging memory card game built with Svelte. Test your memory by matching pairs of magic-themed cards as quickly as possible!

## Features

- 🎮 Three difficulty levels: Easy, Medium, and Hard
- ⏱️ Timer to track your speed
- 🏆 Score system based on turns, time, and difficulty
- 🎨 Multiple themes (Dark, Light, Fantasy)
- 🔊 Sound effects with toggle option
- 📱 Fully responsive design for all devices
- ♿ Accessibility features for keyboard navigation

## How to Play

1. Click on a card to flip it over
2. Find and match pairs of identical cards
3. Match all pairs to win the game
4. Try to complete the game in as few turns and as little time as possible

## Development

### Prerequisites

- Node.js (v14.0.0 or higher)
- npm (v6.0.0 or higher)

### Installation

1. Clone the repository
   ```bash
   git clone https://github.com/yourusername/magic-match-memory-game.git
   ```

2. Navigate to the project directory
   ```bash
   cd magic-match-memory-game
   ```

3. Install dependencies
   ```bash
   npm install
   ```

4. Start the development server
   ```bash
   npm run dev
   ```

5. Open your browser and navigate to `http://localhost:5000`

### Building for Production

```bash
npm run build
```

The built files will be in the `public` directory, ready to be deployed.

## Project Structure

- `src/App.svelte` - Main game component with game logic
- `src/SingleCard.svelte` - Card component for rendering individual cards
- `public/` - Static assets including images and sounds

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Author

Your Name - dimichiko

---

Feel free to contribute to this project by submitting issues or pull requests!