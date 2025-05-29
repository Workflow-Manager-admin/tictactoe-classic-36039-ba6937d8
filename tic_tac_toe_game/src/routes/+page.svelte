<script>
  // Theme colors
  const COLORS = {
    primary: "#ffffff",
    secondary: "#000000",
    accent: "#2196f3"
  };

  // Board state: empty = "", Player X = "X", Player O = "O"
  let board = Array(9).fill("");
  let xIsNext = true; // X always starts first
  let gameOver = false;
  let winner = null;
  let status = "Player X's turn";

  // PUBLIC_INTERFACE
  function handleCellClick(idx) {
    if (board[idx] || gameOver) return;
    board = board.slice(); // create copy for reactivity
    board[idx] = xIsNext ? "X" : "O";
    const gameResult = calculateWinner(board);
    if (gameResult) {
      winner = gameResult;
      status = winner === "draw" ? "It's a draw!" : `Player ${winner} wins! 🏆`;
      gameOver = true;
    } else {
      xIsNext = !xIsNext;
      status = `Player ${xIsNext ? "X" : "O"}'s turn`;
    }
  }

  // PUBLIC_INTERFACE
  function restartGame() {
    board = Array(9).fill("");
    xIsNext = true;
    winner = null;
    gameOver = false;
    status = "Player X's turn";
  }

  // PUBLIC_INTERFACE
  function calculateWinner(b) {
    const lines = [
      [0,1,2], [3,4,5], [6,7,8], // rows
      [0,3,6], [1,4,7], [2,5,8], // cols
      [0,4,8], [2,4,6]           // diags
    ];
    for (let [a, b_idx, c] of lines) {
      if (b[a] && b[a] === b[b_idx] && b[a] === b[c]) {
        return b[a]; // 'X' or 'O'
      }
    }
    if (b.every(cell => cell)) return "draw";
    return null;
  }
</script>

<style>
  .centered-container {
    min-height: 100vh; display: flex; flex-direction: column; justify-content: center; align-items: center;
    background: {COLORS.primary};
  }
  .status-bar {
    margin-bottom: 1rem; font-size: 1.3rem;
    color: {COLORS.secondary};
    text-align: center;
    min-height: 2.5rem;
  }
  .board {
    display: grid;
    grid-template-columns: repeat(3, 64px);
    grid-template-rows: repeat(3, 64px);
    gap: 8px;
    margin-bottom: 1rem;
    background: {COLORS.primary};
    border-radius: 12px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.06);
    padding: 12px;
  }
  .cell {
    background: {COLORS.primary};
    color: {COLORS.secondary};
    font-size: 2.2rem;
    display: flex;
    align-items: center;
    justify-content: center;
    border: 2px solid {COLORS.accent};
    border-radius: 6px;
    outline: none;
    cursor: pointer;
    min-width: 64px;
    min-height: 64px;
    transition: background 0.13s, box-shadow 0.13s;
    font-weight: 500;
    user-select: none;
  }
  .cell:focus, .cell:hover {
    background: #f2f8fe;
    box-shadow: 0 0 0 2px {COLORS.accent}22;
  }
  .cell.disabled {
    pointer-events: none;
    opacity: 0.65;
  }
  .restart-btn {
    background: {COLORS.accent};
    color: #fff;
    border: none;
    padding: 0.65rem 2rem;
    border-radius: 6px;
    font-size: 1.1rem;
    font-weight: 500;
    cursor: pointer;
    transition: background 0.15s;
    margin-top: 0.75rem;
    box-shadow: 0 2px 6px rgba(33,150,243,0.07);
  }
  .restart-btn:active, .restart-btn:focus {
    background: #1760a7;
  }
</style>

<div class="centered-container" style="background: {COLORS.primary};">
  <div class="status-bar">
    {status}
  </div>
  <div class="board">
    {#each board as cell, idx}
      <button
        class="cell {cell || gameOver ? 'disabled' : ''}"
        disabled={!!cell || gameOver}
        aria-label="Board cell"
        on:click={() => handleCellClick(idx)}
      >
        {cell}
      </button>
    {/each}
  </div>
  <button class="restart-btn" on:click={restartGame}>
    Restart Game
  </button>
</div>
