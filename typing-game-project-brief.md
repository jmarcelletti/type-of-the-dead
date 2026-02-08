# Type of the Dead - Browser-Based Typing Game Project Brief

## Context
User is looking for a modern typing game similar to "Typing of the Dead" that works on macOS or Chromebook (no Windows). After researching available options, we determined that building a custom browser-based game would be the best solution since:
- Blood Typers (2025, most similar to Typing of the Dead) is Windows-only
- Epistory has Mac compatibility issues, especially on Apple Silicon
- Browser-based games like ZType work but aren't quite what the user wants

## User Background
- Backend developer with 30 years experience at Microsoft
- Expertise in Go, REST APIs, PostgreSQL, HashiCorp Vault
- Lives in Sparta, NJ
- Wants local play only (no online multiplayer needed)
- Runs macOS (likely MacBook) and/or Chromebook

## Project Requirements

### Core Gameplay
Create a browser-based typing game inspired by "Typing of the Dead" with:
- Zombies approaching the player from various positions
- Each zombie has a word floating above it
- Player types the word to shoot/kill that zombie
- Progressive difficulty (harder/longer words as game continues)
- Wave-based gameplay
- Local high scores (browser localStorage)

### Graphics Style (APPROVED BY USER)
The user has approved this visual style - see the mockup code below for reference:

**Visual Aesthetic:**
- 2D Canvas-based rendering
- Retro arcade aesthetic with modern effects
- Dark color scheme (#0f0f1e background, #1a1a2e secondary)
- Zombies as simple red circles (#ff4444) with yellow eyes
- Player represented as green crosshair (#00ff41) at bottom center
- High contrast, readable text

**Visual Elements:**
- Health bars above zombies (green to red gradient)
- Words displayed above each zombie in bold monospace font
- HUD showing: Score, Wave number, Lives (hearts)
- Typing input box at bottom (green border, black background)
- Bullet tracers (green lines from player to target)
- Muzzle flash effects
- Particle effects on zombie hits/deaths
- Ground/floor with grid pattern

**Color Palette:**
- Background: #0f0f1e to #1a1a2e gradient
- Player/UI: #00ff41 (bright green)
- Zombies: #ff4444 (red)
- Text: #ffffff (white) with black stroke
- Health: #00ff00 (healthy) to #ff4444 (low)

### Game Mechanics

**Zombie Behavior:**
- Spawn at random positions at top/sides of screen
- Move toward player at varying speeds
- Each zombie has a word of appropriate difficulty
- Damage player when reaching bottom
- Die when word is fully typed

**Typing System:**
- Case-insensitive typing
- Must type complete word to kill zombie
- Switch targets by starting to type a different word
- Visual feedback showing current typed letters
- Clear indication of which zombie is targeted

**Difficulty Progression:**
- Start with 3-5 letter words
- Progress to longer words (8-10+ letters) in later waves
- Increase zombie spawn rate
- Increase zombie movement speed
- Mix of fast/slow zombies

**Scoring:**
- Points per zombie killed
- Combo multipliers for consecutive kills without errors
- Bonus points for accuracy
- Wave completion bonuses

**Lives System:**
- Start with 3 lives (hearts in HUD)
- Lose life when zombie reaches player
- Game over at 0 lives

### Technical Requirements

**File Structure:**
- Single standalone HTML file that works when opened directly in browser
- All JavaScript, CSS, and game logic embedded
- No external dependencies except standard browser APIs
- Works on modern browsers (Chrome, Safari, Firefox, Edge)

**Performance:**
- 60 FPS gameplay
- Smooth animations
- No lag during typing
- Efficient canvas rendering

**Features to Include:**
1. **Main Menu Screen**
   - Title: "TYPE OF THE DEAD"
   - Start Game button
   - High Scores display
   - Options (difficulty settings)

2. **Gameplay Screen**
   - Game canvas with zombies and player
   - HUD (score, wave, lives)
   - Typing input display
   - Pause functionality (ESC key)

3. **Game Over Screen**
   - Final score display
   - High score comparison
   - Restart option

4. **Audio (optional but nice to have)**
   - Use Web Audio API for sound effects
   - Gunshot sounds when typing
   - Zombie death sounds
   - Background music (optional)
   - Mute toggle

5. **Local Storage**
   - Save high scores
   - Save settings/preferences
   - Track statistics (total zombies killed, accuracy, etc.)

### Word Dictionary
Include a comprehensive word list with varying difficulty:
- Easy: 3-5 letters (cat, dog, run, jump, fast, slow, etc.)
- Medium: 6-8 letters (zombie, typing, keyboard, monster, etc.)
- Hard: 9+ letters (apocalypse, resurrection, contaminated, etc.)

Consider using thematic words related to zombies/horror when appropriate.

### Controls
- **Keyboard**: All typing input
- **ESC**: Pause/unpause game
- **SPACE**: Start game from menu, continue from game over
- **Mouse**: Optional for menu navigation

## Graphics Mockup Code (APPROVED)

Here's the mockup that the user approved showing the visual style:

```jsx
import React, { useEffect, useRef, useState } from 'react';

export default function TypingGameMockup() {
  const canvasRef = useRef(null);
  const [scene, setScene] = useState('gameplay'); // gameplay, menu, gameover

  useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;
    
    const ctx = canvas.getContext('2d');
    const width = canvas.width;
    const height = canvas.height;

    // Clear canvas
    ctx.fillStyle = '#1a1a2e';
    ctx.fillRect(0, 0, width, height);

    if (scene === 'gameplay') {
      // Draw game scene mockup
      
      // Background gradient
      const bgGrad = ctx.createLinearGradient(0, 0, 0, height);
      bgGrad.addColorStop(0, '#0f0f1e');
      bgGrad.addColorStop(1, '#1a1a2e');
      ctx.fillStyle = bgGrad;
      ctx.fillRect(0, 0, width, height);

      // Ground/floor
      ctx.fillStyle = '#16213e';
      ctx.fillRect(0, height - 100, width, 100);
      ctx.strokeStyle = '#0f3460';
      ctx.lineWidth = 2;
      for (let i = 0; i < width; i += 50) {
        ctx.beginPath();
        ctx.moveTo(i, height - 100);
        ctx.lineTo(i + 25, height);
        ctx.stroke();
      }

      // Player position (bottom center)
      const playerX = width / 2;
      const playerY = height - 80;
      
      // Draw player as simple gun/crosshair
      ctx.fillStyle = '#00ff41';
      ctx.beginPath();
      ctx.arc(playerX, playerY, 15, 0, Math.PI * 2);
      ctx.fill();
      ctx.strokeStyle = '#00ff41';
      ctx.lineWidth = 3;
      ctx.beginPath();
      ctx.moveTo(playerX - 25, playerY);
      ctx.lineTo(playerX + 25, playerY);
      ctx.moveTo(playerX, playerY - 25);
      ctx.lineTo(playerX, playerY + 25);
      ctx.stroke();

      // Draw zombies at different positions
      const zombies = [
        { x: 150, y: 150, word: 'BRAIN', health: 1.0 },
        { x: 400, y: 250, word: 'ZOMBIE', health: 0.6 },
        { x: 600, y: 100, word: 'INFECTED', health: 0.3 },
        { x: 300, y: 350, word: 'UNDEAD', health: 0.8 },
      ];

      zombies.forEach(zombie => {
        // Zombie body (simple circle with shadow)
        ctx.shadowBlur = 15;
        ctx.shadowColor = 'rgba(255, 0, 0, 0.5)';
        
        // Body
        ctx.fillStyle = zombie.health < 0.5 ? '#8b0000' : '#ff4444';
        ctx.beginPath();
        ctx.arc(zombie.x, zombie.y, 30, 0, Math.PI * 2);
        ctx.fill();
        
        // Eyes
        ctx.fillStyle = '#ffff00';
        ctx.beginPath();
        ctx.arc(zombie.x - 10, zombie.y - 5, 5, 0, Math.PI * 2);
        ctx.arc(zombie.x + 10, zombie.y - 5, 5, 0, Math.PI * 2);
        ctx.fill();
        
        ctx.shadowBlur = 0;

        // Health bar
        const barWidth = 60;
        const barHeight = 6;
        const barX = zombie.x - barWidth / 2;
        const barY = zombie.y - 50;
        
        ctx.fillStyle = '#333';
        ctx.fillRect(barX, barY, barWidth, barHeight);
        ctx.fillStyle = zombie.health > 0.5 ? '#00ff00' : '#ff4444';
        ctx.fillRect(barX, barY, barWidth * zombie.health, barHeight);
        ctx.strokeStyle = '#000';
        ctx.strokeRect(barX, barY, barWidth, barHeight);

        // Word above zombie
        ctx.font = 'bold 24px monospace';
        ctx.fillStyle = '#ffffff';
        ctx.strokeStyle = '#000000';
        ctx.lineWidth = 4;
        const wordWidth = ctx.measureText(zombie.word).width;
        ctx.strokeText(zombie.word, zombie.x - wordWidth / 2, zombie.y - 60);
        ctx.fillText(zombie.word, zombie.x - wordWidth / 2, zombie.y - 60);
      });

      // Draw some bullet trails/tracers
      ctx.strokeStyle = '#00ff41';
      ctx.lineWidth = 2;
      ctx.globalAlpha = 0.6;
      ctx.beginPath();
      ctx.moveTo(playerX, playerY);
      ctx.lineTo(400, 250);
      ctx.stroke();
      ctx.globalAlpha = 1.0;

      // Muzzle flash effect
      ctx.fillStyle = 'rgba(255, 255, 100, 0.8)';
      ctx.beginPath();
      ctx.arc(playerX, playerY - 20, 10, 0, Math.PI * 2);
      ctx.fill();

      // HUD - Score
      ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
      ctx.fillRect(10, 10, 200, 60);
      ctx.strokeStyle = '#00ff41';
      ctx.strokeRect(10, 10, 200, 60);
      ctx.font = 'bold 16px monospace';
      ctx.fillStyle = '#00ff41';
      ctx.fillText('SCORE: 1,337', 20, 35);
      ctx.fillText('WAVE: 3', 20, 55);

      // HUD - Lives
      ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
      ctx.fillRect(width - 210, 10, 200, 40);
      ctx.strokeStyle = '#ff4444';
      ctx.strokeRect(width - 210, 10, 200, 40);
      ctx.fillStyle = '#ff4444';
      ctx.fillText('LIVES: ❤️ ❤️ ❤️', width - 190, 35);

      // Current typing input box
      ctx.fillStyle = 'rgba(0, 0, 0, 0.8)';
      ctx.fillRect(width / 2 - 150, height - 50, 300, 40);
      ctx.strokeStyle = '#00ff41';
      ctx.lineWidth = 2;
      ctx.strokeRect(width / 2 - 150, height - 50, 300, 40);
      ctx.font = 'bold 20px monospace';
      ctx.fillStyle = '#00ff41';
      ctx.fillText('> ZOMB_', width / 2 - 130, height - 25);

      // Particle effects
      for (let i = 0; i < 5; i++) {
        ctx.fillStyle = `rgba(255, 100, 100, ${Math.random() * 0.5})`;
        ctx.beginPath();
        ctx.arc(
          400 + (Math.random() - 0.5) * 40,
          250 + (Math.random() - 0.5) * 40,
          Math.random() * 5 + 2,
          0,
          Math.PI * 2
        );
        ctx.fill();
      }

    } else if (scene === 'menu') {
      // Title screen
      const grad = ctx.createLinearGradient(0, 0, 0, height);
      grad.addColorStop(0, '#0f0f1e');
      grad.addColorStop(1, '#1a1a2e');
      ctx.fillStyle = grad;
      ctx.fillRect(0, 0, width, height);

      // Title
      ctx.font = 'bold 60px monospace';
      ctx.fillStyle = '#ff4444';
      ctx.strokeStyle = '#000';
      ctx.lineWidth = 6;
      ctx.strokeText('TYPE OF THE DEAD', width / 2 - 300, 150);
      ctx.fillText('TYPE OF THE DEAD', width / 2 - 300, 150);

      // Subtitle
      ctx.font = 'italic 24px monospace';
      ctx.fillStyle = '#00ff41';
      ctx.fillText('Survive the zombie apocalypse with your keyboard', width / 2 - 280, 200);

      // Menu buttons
      const buttons = ['START GAME', 'HIGH SCORES', 'OPTIONS'];
      buttons.forEach((btn, i) => {
        const y = 300 + i * 80;
        ctx.fillStyle = 'rgba(255, 68, 68, 0.2)';
        ctx.fillRect(width / 2 - 150, y, 300, 60);
        ctx.strokeStyle = '#ff4444';
        ctx.lineWidth = 3;
        ctx.strokeRect(width / 2 - 150, y, 300, 60);
        ctx.font = 'bold 24px monospace';
        ctx.fillStyle = '#ffffff';
        const textWidth = ctx.measureText(btn).width;
        ctx.fillText(btn, width / 2 - textWidth / 2, y + 38);
      });

    } else if (scene === 'gameover') {
      // Game over screen
      ctx.fillStyle = 'rgba(0, 0, 0, 0.8)';
      ctx.fillRect(0, 0, width, height);

      ctx.font = 'bold 72px monospace';
      ctx.fillStyle = '#ff0000';
      ctx.strokeStyle = '#000';
      ctx.lineWidth = 6;
      ctx.strokeText('GAME OVER', width / 2 - 250, height / 2 - 50);
      ctx.fillText('GAME OVER', width / 2 - 250, height / 2 - 50);

      ctx.font = 'bold 32px monospace';
      ctx.fillStyle = '#ffffff';
      ctx.fillText('Final Score: 1,337', width / 2 - 150, height / 2 + 30);
      ctx.fillText('Press SPACE to continue', width / 2 - 200, height / 2 + 100);
    }

  }, [scene]);

  return (
    <div style={{ 
      display: 'flex', 
      flexDirection: 'column', 
      alignItems: 'center', 
      gap: '20px',
      padding: '20px',
      backgroundColor: '#0a0a0f',
      minHeight: '100vh'
    }}>
      <h2 style={{ color: '#00ff41', fontFamily: 'monospace', margin: 0 }}>
        Graphics Style Preview
      </h2>
      
      <canvas 
        ref={canvasRef} 
        width={800} 
        height={600}
        style={{ 
          border: '3px solid #00ff41',
          boxShadow: '0 0 20px rgba(0, 255, 65, 0.3)',
          borderRadius: '8px'
        }}
      />
      
      <div style={{ display: 'flex', gap: '10px' }}>
        <button 
          onClick={() => setScene('menu')}
          style={{
            padding: '12px 24px',
            backgroundColor: '#ff4444',
            color: 'white',
            border: 'none',
            borderRadius: '4px',
            fontFamily: 'monospace',
            fontWeight: 'bold',
            cursor: 'pointer',
            fontSize: '16px'
          }}
        >
          Menu Screen
        </button>
        <button 
          onClick={() => setScene('gameplay')}
          style={{
            padding: '12px 24px',
            backgroundColor: '#00ff41',
            color: 'black',
            border: 'none',
            borderRadius: '4px',
            fontFamily: 'monospace',
            fontWeight: 'bold',
            cursor: 'pointer',
            fontSize: '16px'
          }}
        >
          Gameplay
        </button>
        <button 
          onClick={() => setScene('gameover')}
          style={{
            padding: '12px 24px',
            backgroundColor: '#666',
            color: 'white',
            border: 'none',
            borderRadius: '4px',
            fontFamily: 'monospace',
            fontWeight: 'bold',
            cursor: 'pointer',
            fontSize: '16px'
          }}
        >
          Game Over
        </button>
      </div>

      <div style={{ 
        color: '#ccc', 
        fontFamily: 'monospace', 
        maxWidth: '700px',
        lineHeight: '1.6'
      }}>
        <p><strong style={{ color: '#00ff41' }}>Graphics Style:</strong></p>
        <ul style={{ textAlign: 'left' }}>
          <li>2D Canvas-based rendering</li>
          <li>Retro arcade aesthetic with modern effects</li>
          <li>Zombies represented as simple shapes with personality</li>
          <li>Glowing effects, particle systems, screen shake</li>
          <li>Clear, readable text with high contrast</li>
          <li>Health bars, HUD elements, visual feedback</li>
        </ul>
        <p style={{ marginTop: '20px' }}>
          This is a <strong>static mockup</strong>. The actual game would be fully animated 
          with zombies moving, bullets flying, explosions, typing effects, and smooth 60 FPS gameplay.
        </p>
      </div>
    </div>
  );
}
```

## Deliverable

Create a **single standalone HTML file** named `type-of-the-dead.html` that:
- Contains all HTML, CSS, and JavaScript embedded
- Opens and runs directly in any modern browser (just double-click the file)
- Works on macOS and Chromebook
- Implements all the gameplay mechanics described above
- Uses the approved graphics style from the mockup
- Includes local high score tracking
- Is fully playable and fun

## Additional Notes

- User is experienced backend developer, so code quality matters
- No need for multiplayer or backend - fully client-side
- Focus on solid gameplay loop and good "game feel"
- Performance is important - should run smoothly on both Mac and Chromebook
- Consider adding screen shake on zombie death for better feedback
- Combo system would be a nice touch for replayability

## References

Games that inspired this:
- Typing of the Dead (2000s arcade/PC game)
- ZType (browser game at zty.pe)
- Blood Typers (2025 Steam game, Windows-only)

The goal is to capture the fun of these games in a simple, accessible browser-based format that works everywhere.
