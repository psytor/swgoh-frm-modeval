# Project Overview

This project is a web application designed to help players of the mobile game *Star Wars: Galaxy of Heroes* (SWGoH) manage and evaluate their mods.

The core purpose is to provide clear, actionable recommendations on whether a player should keep, sell, slice, or level up a specific mod. The application will source player data directly from the game's API, eliminating the need for manual data entry.

## Key Goals

*   **Educate New Players:** The primary goal is to create a user-friendly system for new players who are often overwhelmed by the complexity of the mod system. Recommendations will be educational, explaining the *why* behind a decision, not just the *what*.
*   **Scalable Evaluations:** The application will eventually offer multiple evaluation methods, catering to players at all levels—from beginners needing basic guidance to competitive veterans requiring more nuanced analysis.
*   **Data-Driven:** By connecting directly to the game API, the tool will always work with the player's most current mod inventory.

# Guiding Principles

These principles define *how* we will build the project, ensuring a manageable, understandable, and high-quality process.

1.  **Phased & Iterative Development:** We will not build everything at once. Development will proceed in small, logical phases. Each phase will have a clear, achievable goal and result in a tangible, working piece of the application. This allows for easy tracking, clear progress, and ensures every step is understood before moving to the next.

2.  **Modular & Focused Code:** We will write small, focused functions that each have a single, clear responsibility. We will explicitly avoid large, complex functions that try to do too many things at once. This approach makes the code easier to read, test, and debug. It allows us to modify one piece of logic (e.g., evaluating a specific stat) without the risk of breaking the entire system.

# Tech Stack & Conventions

## Backend

*   **Language:** Python
*   **Framework:** FastAPI
*   **Reasoning:** Python's readability combined with FastAPI's high performance and automatic API documentation makes it ideal for our data processing and backend logic.

## Frontend

*   **Framework:** React
*   **Build Tool:** Vite
*   **Component Library:** Material-UI (MUI)
*   **Reasoning:** This stack provides a modern, fast, and scalable foundation. Vite ensures a smooth development experience. React's component architecture is powerful, and Material-UI allows us to quickly build a beautiful, consistent, and readable user interface with pre-built components like cards and modals, minimizing the need to write complex JavaScript and CSS from scratch.

# Development Workflow

Our development process is designed to be fast and efficient, running entirely on your local machine. This provides instant feedback and keeps the production server clean. You will need three terminal windows open to run the full application.

## One-Time Setup

Before you run the project for the first time, you'll need to prepare the backend and frontend environments.

**Backend Setup (in `backend/` directory):**
```bash
# 1. Navigate to the backend directory
cd backend/

# 2. Create a Python virtual environment named 'venv'
python3 -m venv venv

# 3. Activate the virtual environment to isolate dependencies
# (You must do this every time you open a new backend terminal)
source venv/bin/activate

# 4. Install the required Python packages from the requirements file
pip install -r requirements.txt
```

**Frontend Setup (in `frontend/` directory):**
```bash
# 1. Navigate to the frontend directory
cd frontend/

# 2. Install the required JavaScript packages
npm install
```

## Running the Local Development Environment

**Terminal 1: Start the API Dependency (Docker)**

This container provides the live connection to the game's API. We will use `docker compose` to manage it.

First, create a `docker-compose.yml` file in the project root with the following content. This file defines the API service configuration.

```yaml
# docker-compose.yml
services:
  comlink:
    image: ghcr.io/swgoh-utils/swgoh-comlink:latest
    environment:
      - APP_NAME=FRM-Mod-Evaluator
      - PORT=3200
      # - ACCESS_KEY=your_access_key # Optional: Uncomment and set if you use HMAC
      # - SECRET_KEY=your_secret_key # Optional: Uncomment and set if you use HMAC
    ports:
      - "3200:3200" # Exposes the container's port to your local machine
```

Then, run the following command from the **project root** to start the container:

```bash
docker compose up
```

**Terminal 2: Start the Backend Server (Python/FastAPI)**

This terminal runs our Python code that will process the data from the API container.

```bash
# 1. Navigate to the backend directory
cd backend/

# 2. Activate the virtual environment (if not already active)
source venv/bin/activate

# 3. Start the FastAPI server on port 8000 with auto-reload
uvicorn main:app --reload --port 8000
```

**Terminal 3: Start the Frontend Server (React/Vite)**

This terminal runs our user interface. It will fetch data from our backend server.

```bash
# 1. Navigate to the frontend directory
cd frontend/

# 2. Start the Vite development server
npm run dev
```

After these steps, the Vite server will give you a local URL (like `http://localhost:5173`) to view the application. Changes to the frontend code will appear instantly in your browser.

# Key Files & Directories

*   `/backend/`: Contains all the Python and FastAPI source code. This is our application's "brain."
*   `/frontend/`: Contains all the React, Vite, and Material-UI source code. This is what the user sees and interacts with.
*   `/doc/`: Contains project documentation, ideas, and notes.
*   `docker-compose.yml`: Configures and launches the external SWGoH Comlink API service.
*   `GEMINI.md`: This file. It defines our project goals, conventions, and workflows.

# Project Roadmap

This roadmap breaks our project into manageable phases. Each phase builds upon the last, resulting in a testable application at every stage.

### Phase 1: The Foundation (Data Pipeline & Transformation)
*   **Goal:** Create a working data pipeline that fetches raw data from the `comlink` API, has the backend clean and simplify it, and displays a basic result on the frontend. This solves the data size problem from day one.
*   **Features:**
    1.  Backend connects to the `comlink` API and fetches the full player data.
    2.  Backend transforms the raw data into a clean, optimized list of "Mod" objects, containing only the data the UI needs.
    3.  Frontend features a simple page with an Ally Code input and a "Fetch" button.
    4.  On click, the frontend calls the backend and displays a basic, unstyled list of the cleaned mod data.

### Phase 2: The User Interface (The Mod Card)
*   **Goal:** Create the primary visual element of the application.
*   **Features:**
    1.  Design and build a reusable `<ModCard>` component using Material-UI.
    2.  The card will display the essential, visible information for a single mod (set, slot, primary/secondary stats, equipped character).
    3.  Replace the basic list from Phase 1 with a grid of styled `<ModCard>` components.

### Phase 3: Core Interactions (Locking & Details)
*   **Goal:** Add basic user interaction to the mod cards.
*   **Features:**
    1.  Add a "Lock" icon to each card that reflects the mod's locked status from the game data.
    2.  Implement the "Mod Details" modal that appears when a user clicks on a card.
    3.  Add a "Refresh" button to allow re-fetching the data.

### Phase 4: The Brains (Evaluation & Filtering)
*   **Goal:** Implement the core recommendation logic and allow users to sort through their mods.
*   **Features:**
    1.  Implement the first evaluation method for new players.
    2.  Display the recommendation on each `<ModCard>`.
    3.  Add basic filters (Slot, Set, Rarity, Tier).
*   **Note:** The decision of whether to perform the evaluation logic on the **backend** or **frontend** will be made during this phase. We will analyze the trade-offs and test both approaches if necessary.

### Phase 5: Advanced Features (Advanced Filtering & Player Management)
*   **Goal:** Add the final "power user" features.
*   **Features:**
    1.  Implement advanced filters (filter by character, by specific secondary stats).
    2.  Implement a player management system to save and switch between multiple Ally Codes.
