Lab Report - 6

TIC-TAC-TOE USING UART, 4X4 KEYPAD,
AND SYSTICK TIMER INTERRUPTS

Sowmya Balaji

Electronic Systems Engineering, IISc

1. Objective

The objective of this assignment is to develop a two-player Tic-Tac-Toe game using two
TM4C123GH6PM LaunchPads, a 4x4 matrix keypad, UART communication, and the SysTick
timer. Each player enters moves on a separate board using the first three rows and three columns
of the keypad, corresponding to the nine cells of the game. The system alternates turns between
players represented by X and O, validates each move, and detects a win or draw. The UART
console displays the updated game board and status messages, while the 7-segment display and
RGB LED indicate the remaining time and the active player.

2. Hardware & GPIO Configuration

Each Tiva LaunchPad is used with the EduARM4 addon board to interface the 4x4 keypad and
multiplexed 7-segment display. Only the upper-left 3x3 keypad positions are used for move
selection. GPIO lines drive the keypad rows and sense the columns, with interrupt-based key
detection and debouncing. A UART interface links the two boards, with the transmit line of each
board connected to the receive line of the other and a common ground. A separate UART console
connection provides a text view of the game. The RGB LED provides turn indication, and the
SysTick timer supplies the periodic timing reference.

3. Game State and Keypad Move Validation

The game state consists of a nine-cell board, the current player, and a game-over flag. Player 1
uses X and Player 2 uses O. A keypad position is mapped to its corresponding row and column on
the game board. A move is accepted only when it is the local player's turn, the selected cell is
empty, and the game is still active. An occupied cell or an out-of-turn selection produces an
"Invalid move" message without modifying the board or advancing the turn. A valid move stores
the player's symbol, updates the console, and is checked for a terminal condition before control
passes to the opponent.

4. UART Communication and Game-Over Detection

After each valid move, its cell position is sent over the inter-board UART so that both boards
maintain the same game state. The receiving board records the opponent's symbol and displays
the updated board on its console. All three rows, three columns, and two diagonals are checked
for three matching non-empty symbols. A winning combination prints "Game won by Player 1/2";
a full board with no winning combination prints "Game Draw". Once the game ends, further
moves are blocked and the turn countdown is stopped. UART reception is interrupt-driven so that
incoming moves are handled promptly.

5. SysTick Countdown, LED Indication, and Interrupt Support

The SysTick interrupt maintains the time available for the current move. At the start of each turn,
the countdown is reloaded with the configured turn duration and the remaining seconds are
shown on the multiplexed 7-segment display. The active player's green LED blinks, while the
waiting player's red LED remains on. At expiry, a timeout event is generated and communicated
to keep both boards consistent. GPIO interrupts capture keypad activity, SysTick provides timer
events, and UART interrupts service communication. Interrupt handlers capture events quickly,
while game-state processing applies debouncing and updates shared state consistently. For the
bonus requirement, a custom register-level UART driver provides initialization, transmit/receive
buffering, and interrupt handling instead of the TivaWare UART library.

6. Conclusion

The design combines keypad-based move entry, UART synchronization, and SysTick timing to
support a two-board Tic-Tac-Toe game. Move validation and row, column, and diagonal checks
provide the required game rules, while console messages, the countdown display, and LED turn
indication give clear feedback to both players. Interrupt-driven GPIO, timer, and UART handling
supports responsive operation, with a custom UART driver addressing the bonus requirement.
