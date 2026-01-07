
# Java Quest - Technical Documentation

## Overview

An educational Roblox game designed to teach Java programming fundamentals through interactive quizzes and lessons. Players progress through 10 stages, each covering a different Java concept, with a speedrun timer and leaderboard system to encourage replayability.

**Target Audience:** Students learning introductory Java programming  
**Platform:** Roblox  
**Game Mode:** Single-player  
**Duration:** ~5-15 minutes per playthrough

----------

## Game Flow

### 1. **Start Area**

-   Player spawns with movement locked
-   Sees "Start Game" button
-   Clicks button to begin

### 2. **Gameplay Loop**

-   Timer starts counting
-   Player teleported to Stage 1
-   Movement unlocked
-   Must discover lessons and complete quizzes (Stages 1-10)

### 3. **Completion**

-   Timer stops after Stage 10 quiz
-   Time saved to leaderboard
-   Player teleported to Victory Area
-   Views leaderboard board with top 10 times

----------

## Educational Content

### Stage Topics

Stage

Lesson Topic

Quiz Type

Concept Covered

1

Introduction to Java

Multiple Choice

Language basics, JVM, WORA

2

Variables

Multiple Choice

Variable declaration, types

3

Methods

Multiple Choice

Method structure, return types

4

Data Types

Multiple Choice

Primitive vs Reference types

5

Conditionals

**Coding**

if statements, operators

6

Loops

Multiple Choice

for, while, do-while

7

Arrays

Multiple Choice

Array indexing, length

8

OOP

Multiple Choice

OOP principles

9

Classes & Objects

Multiple Choice

Object creation, new keyword

10

Constructors

Multiple Choice

Constructor purpose

**Note:** Stage 5 uses a fill-in-the-blank coding quiz format instead of multiple choice.

----------

## System Architecture

### Core Systems

```
┌─────────────────────────────────────────────────────┐
│                  Game Systems                        │
├─────────────────────────────────────────────────────┤
│  Quiz System      │  Handles multiple choice &      │
│                   │  coding quizzes                 │
├───────────────────┼─────────────────────────────────┤
│  Lesson System    │  Displays educational content   │
├───────────────────┼─────────────────────────────────┤
│  Interaction      │  Unlocks quizzes via tasks      │
│  System           │  (optional gates)               │
├───────────────────┼─────────────────────────────────┤
│  World Actions    │  Opens gates, fixes bridges,    │
│                   │  plays sounds on completion     │
├───────────────────┼─────────────────────────────────┤
│  Journal System   │  Tracks discovered lessons &    │
│                   │  completed quizzes              │
├───────────────────┼─────────────────────────────────┤
│  Timer System     │  Speedrun timer (server-side)   │
├───────────────────┼─────────────────────────────────┤
│  Leaderboard      │  Top 10 fastest times           │
│  System           │  (session-only)                 │
├───────────────────┼─────────────────────────────────┤
│  Teleporter       │  Two-way teleportation with     │
│  System           │  custom transition animation    │
└───────────────────┴─────────────────────────────────┘

```

----------

## Project Structure

### Workspace

```
Workspace/
├── Triggers/
│   ├── LessonTriggers/         # Parts with ProximityPrompts for lessons
│   └── QuizTriggers/           # Parts with ProximityPrompts for quizzes
├── Gates/                      # World objects (gates, bridges, fences)
├── Bridges/
├── Fences/
├── Interactions/               # Interactive objects that unlock quizzes
├── Teleporters/                # Teleporter parts (two-way linked)
└── VictoryArea/
    ├── SpawnLocation           # Where players land after completion
    └── LeaderboardBoard        # Physical board displaying leaderboard

```

### ReplicatedStorage

```
ReplicatedStorage/
├── Content/
│   ├── Lessons                 # ModuleScript: Lesson content data
│   ├── Quizzes                 # ModuleScript: Quiz questions & answers
│   └── Config                  # (Optional) Configuration settings
└── Remotes/                    # RemoteEvents for client-server communication
    ├── LessonRemote
    ├── QuizRemote
    ├── QuizResultRemote
    ├── CodingQuizRemote
    ├── ShowFeedbackRemote
    ├── TeleportPlayerRemote
    ├── JournalDataRemote
    ├── JournalActionRemote
    ├── TimerUpdate
    └── RequestLeaderboard

```

### ServerScriptService

```
ServerScriptService/
├── GameManager                 # Handles game start, player setup
├── LessonController            # Manages lesson triggers & display
├── QuizController              # Manages quiz triggers, validation, completion
├── InteractionController       # Handles interaction tasks
├── TeleporterController        # Manages teleporter logic
├── JournalController           # Tracks discovered/completed content
├── TimerController             # Speedrun timer management
├── LeaderboardController       # Top 10 time tracking
└── WorldActions/
    ├── Templates/              # Reusable action scripts
    │   ├── OpenGate
    │   ├── FixBridge
    │   ├── DisableElectricFence
    │   └── PlaySound
    └── CustomActions/          # Stage-specific complex actions
        └── Stage3_ComplexSequence

```

### StarterPlayer

```
StarterPlayer/
└── StarterPlayerScripts/
    ├── LessonClientController
    ├── QuizClientController
    ├── CodingQuizClientController
    ├── FeedbackClientController
    ├── TeleporterClientController
    ├── JournalClientController
    ├── TimerClientController
    └── LeaderboardClientController

```

### StarterGui

```
StarterGui/
├── LessonGUI                   # Displays lesson content
├── QuizGUI                     # Multiple choice quiz interface
├── CodingQuizGUI               # Fill-in-the-blank coding quiz
├── FeedbackGUI                 # Shows feedback messages
├── TeleportGUI                 # Teleport transition animation
├── JournalGUI                  # Learning journal (press J)
└── TimerGUI                    # Speedrun timer display

```

----------

## Technical Implementation

### Quiz System

**Types:**

-   **Multiple Choice:** 4 answers, 1 correct (Stages 1-4, 6-10)
-   **Coding:** Fill-in-the-blank with 2 text inputs (Stage 5)

**Validation:**

-   Server-authoritative (prevents cheating)
-   Case-insensitive for coding quizzes
-   Whitespace trimmed automatically

**Data Structure:**

```lua
["Stage1"] = {
    Type = "MultipleChoice",
    Question = "What type of programming language is Java?",
    Answers = {"Object-Oriented", "Procedural", "Functional", "Assembly"},
    CorrectAnswer = 1,
    CorrectFeedback = "Correct! Java is...",
    OnComplete = { Type = "Template", ActionName = "OpenGate", Params = {...} }
}

```

### World Action System

**Modular Design:**

-   **Templates:** Reusable actions (OpenGate, FixBridge, PlaySound)
-   **Parameters:** Customize behavior per usage
-   **Multi-Actions:** Execute multiple actions simultaneously

**Example:**

```lua
OnComplete = {
    Type = "MultiTemplate",
    Actions = {
        {ActionName = "OpenGate", Params = {gate = workspace.Gates.Stage5Gate}},
        {ActionName = "PlaySound", Params = {soundId = "rbxassetid://123"}}
    }
}

```

### Interaction System

**Purpose:** Gate quizzes behind tasks (e.g., flip switch to unlock quiz)

**Flow:**

1.  Player tries locked quiz → Shows feedback message
2.  Player finds interaction object (e.g., power switch)
3.  Activates interaction → Quiz unlocks
4.  Player returns to quiz → Now accessible

**Attributes:**

-   Quiz Trigger: `IsLocked`, `LockedFeedback`
-   Interaction: `InteractionName`, `UnlocksQuiz`, `CompletionFeedback`

### Journal System

**Purpose:** Track progress and review content

**Features:**

-   Automatically logs discovered lessons
-   Automatically logs completed quizzes
-   Two tabs: Lessons | Quizzes
-   Detail panel shows full content
-   Press **J** to open/close
-   Session-only (resets on rejoin)

**Data Storage:**

```lua
{
    discoveredLessons = {"Stage1", "Stage2", "Stage3"},
    completedQuizzes = {"Stage1", "Stage5"}
}

```

### Timer System

**Implementation:**

-   **Server-side:** Anti-cheat, accurate timing
-   **Client-side display:** Real-time updates every 0.1s
-   **Format:** MM:SS.S (minutes:seconds.decimal)
-   **Validation:** Min 30s, Max 1 hour

**Flow:**

1.  Start Game → Timer starts (00:00.0)
2.  Complete stages → Timer keeps running
3.  Complete Stage 10 → Timer stops
4.  Time saved to leaderboard

### Leaderboard System

**Storage:** Session-only (resets on server restart)

**Features:**

-   Top 10 fastest times
-   Sorted automatically (fastest first)
-   Physical 3D board in Victory Area
-   Player's entry highlighted
-   Gold/Silver/Bronze for top 3

**Display Format:**

```
#1  PlayerName    04:23.5
#2  AnotherUser   04:45.2
#3  FastRunner    05:12.8

```

### Teleporter System

**Design:** Two-way linked teleporters

**Features:**

-   Attribute-driven (`LinkedTeleporter` attribute)
-   Custom transition animation (3 seconds)
-   Black screen wipe with "Teleporting..." text
-   Player faces teleporter's orientation on arrival

**Animation Sequence:**

1.  Black screen slides in from left (1s)
2.  "Teleporting..." text appears (0.3s)
3.  Player teleported (invisible to player)
4.  Black screen slides out to right (1s)

----------

## UI/UX Design

### GUI Animations

All GUIs use smooth animations:

-   **Open:** Scale up with bounce (Back easing)
-   **Close:** Scale down with bounce
-   **Buttons:** Hover effect (lift 5px)
-   **Feedback:** Fade in/out

### Color Scheme

-   **Correct:** Green `RGB(85, 255, 127)`
-   **Incorrect:** Red `RGB(255, 85, 85)`
-   **Warning:** Yellow/Orange `RGB(255, 200, 100)`
-   **Success:** Green `RGB(100, 255, 150)`
-   **Info:** Blue `RGB(100, 180, 255)`

### Accessibility

-   Large text sizes (16-24px)
-   High contrast (white text on dark backgrounds)
-   Clear button states (hover, active, disabled)
-   Feedback for all actions

----------

## Security & Anti-Cheat

### Server Authority

 **Quiz validation on server** - Client can't fake answers  
 **Timer runs on server** - Client can't modify time  
 **Completion tracking on server** - Can't skip stages  
 **World actions executed by server** - Client can't trigger

### Validation Checks

-   Quiz answers validated server-side
-   Timer completion time validated (30s - 1 hour)
-   Stage completion verified before allowing progress
-   Leaderboard entries validated

### Rate Limiting

-   Quiz cooldowns prevent spam
-   Teleporter cooldown (1.5s)
-   Interaction cooldowns

----------

## Data Persistence

### Session-Only Data

**What's Stored:**

-   Journal progress (lessons/quizzes)
-   Timer state
-   Leaderboard times
-   Completed stages

**When Lost:**

-   Player leaves game
-   Server restarts
-   New game session

**Why Session-Only:**

-   Simpler implementation
-   No storage complexity
-   Appropriate for school project
-   Encourages speedruns

----------

## Performance Considerations

### Optimization Strategies

1.  **Modular Content:** Lessons/Quizzes in separate modules (easy to load)
2.  **Lazy Loading:** Journal only loads when opened
3.  **Client-Side Caching:** Content modules stored on client
4.  **Efficient Updates:** Timer updates every 0.1s (not every frame)
5.  **Event-Driven:** Systems communicate via RemoteEvents (no polling)

### Scalability

-   Supports multiple concurrent players
-   Independent timers per player
-   Session-based storage (no database queries)
-   Minimal server load

----------

## Testing Checklist

### Core Systems

-   [ ] Lessons display correctly
-   [ ] Multiple choice quizzes work
-   [ ] Coding quiz (Stage 5) works
-   [ ] World actions execute properly
-   [ ] Interaction tasks unlock quizzes

### Timer & Leaderboard

-   [ ] Timer starts on game start
-   [ ] Timer displays correctly
-   [ ] Timer stops on Stage 10 completion
-   [ ] Time saved to leaderboard
-   [ ] Leaderboard board displays top 10
-   [ ] Victory Area teleport works

### Journal

-   [ ] Lessons logged when viewed
-   [ ] Quizzes logged when completed
-   [ ] Journal GUI opens (press J)
-   [ ] Content displays in detail panel
-   [ ] Progress tracking accurate

### Teleporters

-   [ ] Teleport animation plays
-   [ ] Player teleports correctly
-   [ ] Two-way functionality works
-   [ ] Player faces correct direction

### Edge Cases

-   [ ] Player leaves mid-game
-   [ ] Multiple players simultaneously
-   [ ] Empty leaderboard display
-   [ ] All 10 stages completed
-   [ ] Wrong answers handled properly

----------

## Maintenance & Updates

### Adding New Content

**New Lesson:**

1.  Add entry to `Lessons` ModuleScript
2.  Place lesson trigger in `Workspace.Triggers.LessonTriggers`
3.  Set `StageName` attribute

**New Quiz:**

1.  Add entry to `Quizzes` ModuleScript
2.  Place quiz trigger in `Workspace.Triggers.QuizTriggers`
3.  Set `StageName` attribute
4.  Configure world action

**New World Action:**

1.  Create template in `WorldActions/Templates`
2.  Reference in quiz's `OnComplete`

### Configuration

**Edit these for customization:**

-   Timer update interval (0.1s default)
-   Leaderboard max entries (10 default)
-   Quiz cooldowns (1.5s default)
-   Teleport animation duration (3s default)

----------

## Known Limitations

1.  **Session-Only Storage:** Progress doesn't persist across sessions
2.  **Server Capacity:** Leaderboard limited to current server
3.  **Single Language:** Only English supported
4.  **Fixed Stage Count:** 10 stages hardcoded
5.  **No Hints System:** Players must figure out answers themselves

----------

## Future Enhancement Ideas

-   Persistent storage (DataStores)
-   Difficulty levels (Easy/Medium/Hard)
-   Hint system for quizzes
-   More coding quiz types
-   Global leaderboard across servers
-   Achievement badges
-   Daily challenges

----------

## License

Educational use only. All rights reserved.

----------

##  Support

For issues or questions:

-   Check Output window for error messages
-   Review this documentation
-   Test in Studio before publishing
-   Use diagnostic scripts for debugging

----------

**Version:** 1.0  
**Last Updated:** January 2026  
**Game Status:** Complete & Functional
