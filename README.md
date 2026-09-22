## Android Tic-Tac-Toe in Java with a full game-tree minimax AI opponent and a local two-player mode

## Overview

A native Android app with two game modes:

- **One-Player:** you play against the computer, which picks its moves by searching the complete Tic-Tac-Toe game tree with minimax.
- **Two-Player:** two people take turns on the same device.

Tic-Tac-Toe's game tree is small enough to search exhaustively on a phone in a fraction of a second, so the AI needs no depth limit and no heuristic evaluation function. Every position is scored by playing it out to the end.

## Features

- One-Player mode: you play **O** and move first; the computer plays **X** and replies using minimax.
- Two-Player mode: X and O alternate on one device.
- Winning lines are highlighted in green, draws are detected, and **New Game** resets the board.

## How the AI Works

The search lives in `GameBoard.java` (in the method currently named `alphaBetaPruning`; see [Known Issues](#known-issues)).

- **Terminal scores:** `+1` if X (the computer) wins, `-1` if O (the human) wins, `0` for a draw.
- **Players:** X is the maximizing player and O is the minimizing player.
- **Search:** the method recursively tries every empty cell, alternating players. It places a mark on a shared 3×3 board, evaluates the resulting position, and then undoes the move (backtracking).
- **Move choice:** at the root, the computer plays a move whose minimax value is at least `0`, meaning a move that does not lose against best play.

```text
minimax(board, player):
    if X has won:       return +1
    if O has won:       return -1
    if board is full:   return 0
    for each empty cell:
        place player's mark
        score = minimax(board, opponent)
        undo the move
    return max(scores) if player is X else min(scores)
```

## Project Structure

```text
app/src/main/java/com/example/ali/tic_tac_toe/
├── MainActivity.java        # Menu: choose One-Player or Two-Player
├── OnePlayerActivity.java   # Human (O) vs. minimax AI (X)
├── TwoPlayerActivity.java   # Local two-player game
├── GameBoard.java           # Board state, win detection, minimax search
└── Point.java               # (row, column) cell coordinate
app/src/main/res/layout/
├── activity_main.xml        # Mode-selection screen
└── activity_game_board.xml  # 3×3 board, turn/status label, New Game button
```

## Requirements

- Android Studio
- Android Gradle Plugin 4.1.3 (requires Gradle 6.5 or newer)
- `compileSdkVersion` / `targetSdkVersion` 30, `minSdkVersion` 21
- Java

## Getting Started

1. Clone the repository:

   ```bash
   git clone https://github.com/ak811/tictactoe-minimax.git
   ```

2. In Android Studio, choose **File → Open** and select the project root (the folder containing `settings.gradle`).
3. Let Gradle sync. If it fails, see the dependency note under [Known Issues](#known-issues).
4. Run the `app` configuration on an emulator or a physical device (Android 5.0+).

## Known Issues

- **No alpha-beta pruning yet.** Despite its name, `alphaBetaPruning` takes no alpha/beta bounds. Its early exit on a found win uses `break` inside a `switch`, which leaves the `switch` rather than the `for` loop, so the search is fully exhaustive.
- **Move choice is not always optimal.** The computer keeps the *last* root move with a value of at least `0`, so it never picks a losing move but can settle for a draw when a win is available.
- **The computer's cells stay tappable.** After the computer moves, its cell is not disabled, so tapping it in One-Player mode overwrites the X with an O.
- **Dependencies need updating.** The activities extend `android.support.v7.app.AppCompatActivity`, but the appcompat library is not declared in `app/build.gradle`, and `constraint-layout:2.0.4` is published only under AndroidX. The project also has no Gradle wrapper, and its `applicationId` (`com.example.ali.purblepairs`) is left over from another project.
- **Computer-first mode is disabled.** `whoBeginsFirstDialog()` is commented out; when enabled, the computer's opening move is random rather than chosen by minimax.

## Roadmap

- Implement true alpha-beta pruning and compare nodes expanded against plain minimax.
- Fix root move selection so the computer always takes a win when one exists.
- Migrate to AndroidX and add a Gradle wrapper.
- Re-enable the option for the computer to move first.

## License

MIT © 2022 ak811. See [LICENSE](LICENSE).
