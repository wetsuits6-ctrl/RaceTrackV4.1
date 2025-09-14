# Beachside Racetrack Management System

A comprehensive real-time race management system with multi-device interfaces for managing go-kart races, tracking lap times, and displaying live race information to spectators.

## Quick Start

### Prerequisites
- Node.js (v14 or higher)
- npm

### Installation & Setup

1. **Clone and install dependencies:**
```bash
git clone <repository-url>
cd beachside-racetrack
npm install
```

2. **Create environment file:**
   Create a `.env` file in the root directory:
```env
RECEPTIONIST_KEY=your_receptionist_password
SAFETY_KEY=your_safety_password
OBSERVER_KEY=your_observer_password
SESSION_SECRET=your_session_secret_key
RACE_TIMER_MINUTES=10
```

3. **Start the server:**
```bash
# Production (10-minute races)
npm start

# Development (1-minute races for testing)
npm run dev
```

4. **Access the system:**
   Open your browser to `http://localhost:3000`

## Multi-Device Network Setup

To access from phones, tablets, and other devices on your network:

### Windows + WSL Setup

**1. Start server inside WSL:**
```bash
npm run dev
```

**2. Get WSL IP:**
```bash
ip addr show eth0
```

**3. Get Windows IP:**
```bash
ipconfig
```

**4. Setup port forwarding + firewall (run PowerShell/CMD as Admin):**

Replace `<WSL-IP>` with your WSL IP from step 2, and `<WIN-IP>` with your Windows IP from step 3:

```bash
netsh interface portproxy add v4tov4 listenport=3000 listenaddress=0.0.0.0 connectport=3000 connectaddress=<WSL-IP>
```

**Allow TCP port 3000 through Windows Firewall:**
```bash
netsh advfirewall firewall add rule name="WSL 3000 Inbound" dir=in action=allow protocol=TCP localport=3000
netsh advfirewall firewall add rule name="WSL 3000 Outbound" dir=out action=allow protocol=TCP localport=3000
```

**5. On your phone/tablet, open:**
```
http://<WIN-IP>:3000
```

### Direct Linux/Mac Setup
```bash
# Find your network IP
ifconfig | grep "inet " | grep -v 127.0.0.1

# Access from any device using: http://<YOUR-IP>:3000
```

## User Guide

### System Overview

The system provides three user roles and multiple public displays:

**Staff Interfaces (Password Protected):**
- **Front Desk**: Session and driver management
- **Race Control**: Race start/stop and safety controls
- **Lap Line Tracker**: Real-time lap time recording

**Public Displays (No Login Required):**
- **Leaderboard**: Live race standings and lap times
- **Next Race**: Shows upcoming drivers and sessions
- **Race Countdown**: Large timer display for spectators
- **Race Flags**: Safety flag status display

### Front Desk Interface (`/front-desk`)

**Access:** Requires Receptionist password

**Core Functions:**
- Create new race sessions
- Add/remove drivers from sessions
- Assign cars to drivers (manual or auto-assign)
- Manage driver information

**Workflow:**
1. Create a new session with a descriptive name
2. Add drivers one by one, specifying names
3. Assign cars (1-12) or use auto-assign for random selection
4. Sessions become available for Race Control to start

**Key Features:**
- Real-time validation of driver names and car assignments
- Support for up to 8 drivers per session
- Auto-assignment prevents car conflicts
- Live updates across all connected devices

### Race Control Interface (`/race-control`)

**Access:** Requires Safety password

**Core Functions:**
- Select and start race sessions
- Control race safety flags (Green, Yellow, Red, Checkered)
- End races and manage results
- Monitor race status and timing

**Safety Protocol:**
- **Start Race**: Begins selected session with green flag
- **Green Flag**: Normal racing conditions
- **Yellow Flag**: Caution - drivers slow down
- **Red Flag**: Danger - all drivers stop immediately
- **Checkered Flag**: Race finished
- **End Race**: Emergency stop with automatic red flag

**Workflow:**
1. Select a prepared session from the list
2. Press "Start Race" to begin
3. Use flag controls to manage safety during the race
4. Race automatically ends after configured time limit
5. Use "End Race" for emergency stops

### Lap Line Tracker (`/lap-line-tracker`)

**Access:** Requires Observer password

**Core Functions:**
- Record lap times for each driver during active races
- Generate realistic lap times (45-75 seconds)
- Real-time leaderboard updates
- Visual feedback for recorded laps

**Usage:**
1. Wait for a race to start (interface shows when race is active)
2. Tap driver buttons as they cross the finish line
3. System automatically generates realistic lap times
4. All displays update immediately with new lap data

**Features:**
- Large, touch-friendly driver buttons
- Visual confirmation when laps are recorded
- Automatic car and lap count display
- Only active during live races

### Public Display Interfaces

#### Leaderboard (`/leader-board` or `/`)
- **Purpose**: Main spectator display showing live race standings
- **Features**: Position, driver names, lap counts, best times, gaps to leader
- **Modes**: Shows live race data or last completed race when idle
- **Display**: Optimized for large screens and TVs

#### Next Race (`/next-race`)
- **Purpose**: Shows upcoming drivers and race information
- **Modes**:
    - **Next Race**: Shows drivers in upcoming session
    - **Paddock Alert**: Flashing display when drivers should report
    - **Current Race**: Shows active race participants
- **Features**: Automatic mode switching based on race state

#### Race Countdown (`/race-countdown`)
- **Purpose**: Large countdown timer for spectators
- **Displays**:
    - Race preparation status
    - Live countdown timer during races (e.g., 10:00 → 0:00)
    - Race completion status
- **Features**: Visual warnings when time is running low

#### Race Flags (`/race-flags`)
- **Purpose**: Large safety flag display
- **Shows**: Current flag status with color-coded backgrounds
- **Modes**: Green (safe), Yellow (caution), Red (danger), Checkered (finished)
- **Features**: Sound effects and animations for flag changes

## System Architecture

### Technology Stack
- **Backend**: Node.js, Express.js, Socket.IO
- **Frontend**: Vanilla JavaScript, HTML5, CSS3
- **Authentication**: Express Sessions
- **Real-time Communication**: WebSocket via Socket.IO
- **Data Persistence**: JSON file storage

### Security Features
- Role-based authentication with separate passwords
- Session management with 24-hour expiration
- CSRF protection and secure cookie handling
- Network access controls

### Race State Management
- **Idle**: No active race, sessions can be selected
- **Racing**: Active race in progress, lap times being recorded
- **Finished**: Race completed, results displayed
- Automatic transitions and timer management

## Configuration

### Environment Variables
- `RECEPTIONIST_KEY`: Password for front desk access
- `SAFETY_KEY`: Password for race control access
- `OBSERVER_KEY`: Password for lap tracker access
- `SESSION_SECRET`: Secret key for session encryption
- `RACE_TIMER_MINUTES`: Race duration (default: 10 minutes)
- `PORT`: Server port (default: 3000)

### Race Settings
- Default race duration: 10 minutes (configurable via environment)
- Maximum drivers per session: 8
- Available cars: 1-12
- Lap time range: 45-75 seconds (auto-generated)

## Troubleshooting

### Common Issues

**"npm run dev/npm run not working":**
```bash
chmod +x node_modules/.bin/cross-env
```

**Cannot access from other devices:**
- Check firewall settings
- Verify port forwarding (WSL users)
- Ensure all devices are on the same network

**Session timeouts:**
- Sessions expire after 24 hours of inactivity
- Log out and log back in with the appropriate password

**Race state issues:**
- Refresh the browser if displays show incorrect state
- Check server logs for error messages
- Ensure only one person is using Race Control at a time

### Performance Tips
- Use wired network connections for critical displays
- Close unnecessary browser tabs
- Restart the server daily for optimal performance
- Use dedicated devices for each display when possible

## Development

### File Structure
```
beachside-racetrack/
├── server.js              # Main server application
├── package.json           # Dependencies and scripts
├── .env                   # Environment configuration
├── lastRace.json          # Persistent race data
├── public/                # Static web files
│   ├── front-desk.html
│   ├── race-control.html
│   ├── lap-line-tracker.html
│   ├── leader-board.html
│   ├── next-race.html
│   ├── race-countdown.html
│   └── race-flags.html
└── README.md
```

### Adding Features
- All client-side code is in individual HTML files
- Server-side logic is centralized in `server.js`
- Real-time updates use Socket.IO events
- State management is handled server-side

### API Endpoints
- `POST /login` - User authentication
- `POST /logout` - Session termination
- `GET /api/auth-status` - Check authentication status
- Socket.IO events for real-time race management

This system is designed for reliability and ease of use in a live racing environment. Each interface is optimized for its specific role, whether that's quick data entry, safety control, or public display.