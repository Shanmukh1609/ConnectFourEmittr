# Connect Four - Multiplayer with WebSockets

A real-time multiplayer Connect Four game built with Go (Backend) and React (Frontend), featuring WebSocket-based communication, automatic matchmaking, and resilient game state handling.

## Key Features

### 🔄 Robust Reconnection System
The game is designed to handle network instability gracefully.
- **Graceful Disconnects:** If a player disconnects (e.g., closes tab, network loss), the server detects the interruption immediately using `HandleDisconnect`.
- **Reconnection Window:** The game enters a "paused" state for that player, initiating a **30-second countdown**.
    - **Frontend:** Displays a warning: *"Opponent Disconnected! Winning by forfeit in: 30s"*.
    - **Backend:** Starts a timer. If the user reconnects within this window, `JoinOrReconnect` restores their session, updates their websocket connection, and the game resumes exactly where it left off.
- **Forfeit:** If the player fails to reconnect within the 30-second window, they automatically forfeit the game.

### ⏱️ Game Timers
Time management is crucial to keep the game flowing:
1.  **Turn Timer (45s):**
    -   Each player has 45 seconds to make a move.
    -   **Backend Enforcement:** The server monitors the time since `TurnStartTime`. If the time exceeds 45 seconds, the server automatically executes a **random valid move** on behalf of the player to keep the game going.
    -   **Frontend Display:** A visual timer counts down the remaining seconds for the active player.

2.  **Matchmaking Timer (20s):**
    -   When joining the queue, if no human opponent is found within 20 seconds, the server automatically spawns a **CompetitiveBot** so you can play immediately.

3.  **Forfeit Timer (30s):**
    -   As mentioned in the Reconnection section, this timer runs when a player goes offline during an active match.

## 🐳 Docker Setup

You can easily run the entire stack using Docker Compose. The Backend and Frontend are configured to run independently.

### Prerequisites
- Docker
- Docker Compose

### 1. Start the Backend
The backend runs on port `8080`.
```bash
cd backend
docker-compose up --build
```
*Note: The backend connects to an external PostgreSQL database defined in `backend/docker-compose.yml`.*

### 2. Start the Frontend
The frontend runs on port `3000` and proxies requests to the backend.
```bash
cd frontend
docker-compose up --build
```

### 3. Access the Game
Once both services are running, open your browser and navigate to:
http://localhost:3000

## Architecture Highlights
-   **Backend:** Go (Golang) with Gorilla WebSockets. handles game logic, state synchronization, and timeouts.
-   **Frontend:** React.js using Context API for state management.
-   **Communication:** Real-time bi-directional events (Match Started, Player Move, Game Over, etc.) via WebSockets.
