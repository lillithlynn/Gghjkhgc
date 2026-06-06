# Unity Matchplay Sample

A comprehensive, production-ready example demonstrating end-to-end multiplayer game networking with **Unity Matchmaker** and **Multiplay** services. This sample shows best practices for building scalable matchmaking systems and deploying dedicated cloud servers.

## 📋 Table of Contents

- [Features](#features)
- [What You'll Learn](#what-youll-learn)
- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [Running the Project](#running-the-project)
- [Setup Guide](#setup-guide)
- [Architecture](#architecture)
- [Troubleshooting](#troubleshooting)
- [Technologies](#technologies)
- [Contributing](#contributing)

## ✨ Features

- **Matchmaking Ticket Configuration** - Players customize preferences (map, game mode)
- **Intelligent Player Matching** - Automated player matching based on configurable preferences
- **Dynamic Server Provisioning** - Automatic cloud server launch via Multiplay
- **Allocation Payload System** - Servers receive match configuration and auto-configure
- **Robust Client-Server Architecture** - Production-grade networking framework
- **Local Testing Support** - ParrelSync integration for local multiplayer testing
- **Authentication Integration** - Secure player identity management
- **Match Rules Engine** - Flexible filtering and team composition logic
- **Regional Matchmaking** - Geographic-aware player matching

## 📚 What You'll Learn

This sample demonstrates:

✅ How to implement a complete matchmaking pipeline  
✅ Client-server network architecture patterns  
✅ Cloud server deployment and scaling  
✅ Integration with Unity Services (Authentication, Matchmaker, Multiplay)  
✅ Testing multiplayer locally with ParrelSync  
✅ Handling server allocation and configuration  
✅ Building robust connection flows  
✅ Best practices for multiplayer game development  

## 🚀 Quick Start

### Prerequisites

- **Unity 2021.3 LTS** or later
- **Linux Build Support** modules installed (for server builds)
- Cloud project linked to Unity Services
- Minimum 2GB disk space

### 1. Basic Setup

1. Clone this repository
2. Open in Unity 2021.3+
3. Link your project to a Cloud Project:
   - **Services > Cloud > Create Cloud Project**
4. Enable these services:
   - **Authentication** (for player identity)
   - **Matchmaker** (for player matching)
   - **Multiplay** (for cloud servers)

### 2. Run Locally (No Configuration Needed)

**Client Mode:**
```
1. Open the Bootstrap scene (Assets/Scenes/bootStrap)
2. Press Play
3. Select "Local" from the menu
```

**Server + Client Mode (with ParrelSync):**
1. Install ParrelSync from Package Manager
2. **ParrelSync > Clones Manager > Add new Clone**
3. Set clone command line arguments to: `server`
4. Start the clone → it runs as server on `127.0.0.1:7777`
5. Play the base project and select "Local" on main menu
6. Both will connect and you can test multiplayer!

## 📁 Project Structure

### 📂 Scenes

| Scene | Purpose | Key Components |
|-------|---------|-----------------|
| **bootStrap** | Entry point and mode selector | Initializes client/server mode, persistent manager |
| **mainMenu** | Player UI and connection options | Matchmaking UI, local connection flow |
| **game_lab** | Gameplay environment (table) | Game logic, networked gameplay, networking spawner |
| **game_space** | Gameplay environment (sphere) | Alternative game scene, demonstrates map selection |

### 🔄 Execution Modes

| Mode | How to Run | Use Case |
|------|-----------|----------|
| **Client (In-Editor)** | Play from bootStrap scene | Development and testing |
| **Client (Build)** | Standard executable build | Shipping to players |
| **Server (ParrelSync)** | Clone with "server" arguments | Local testing with clients |
| **Server (Cloud)** | Multiplay deployment | Production multiplayer |
| **Headless Server** | Linux build, uploaded to Multiplay | Dedicated server in cloud |

## ▶️ Running the Project

### Option 1: In-Editor Single Player

```
Scene: bootStrap → Click Play → Select "Local"
```

- Tests the game without networking
- Useful for gameplay debugging

### Option 2: Local Multiplayer (2+ Instances)

**Setup:**
1. Install ParrelSync (Window > ParrelSync > Clone Manager)
2. Create clone with argument: `server`
3. Add another clone with argument: `client`
4. Run all 3 instances (base project + 2 clones)

**Result:** Multiple players connect to local server and play together

### Option 3: Cloud Matchmaking (After Full Setup)

```
Scene: bootStrap → Click Play → Select "Matchmake"
```

- Automatically finds match on cloud
- Spins up dedicated Multiplay server
- Connects all players to cloud instance
- *Requires completing Setup Guide (see below)*

### Building for Deployment

**Linux Server Build (For Multiplay):**
```
1. Go to BuildTools menu > Linux Server
2. Output: <project>/Builds/Matchplay-server_<datetime>/
3. Upload the Matchplay.x86_64 executable to Multiplay
```

**Client Build:**
```
File > Build Settings > Select your platform > Build
```

## ⚙️ Setup Guide

### Phase 1: Cloud Project Configuration (5 min)

**Link Your Project:**
1. Open **Services** tab in Editor
2. Go **Cloud > Create Cloud Project**
3. Select/create Organization
4. Select/create Project
5. Wait for services to initialize

### Phase 2: Authentication Service (5 min)

This sample uses **anonymous authentication** - great for quick testing:

1. **Services Dashboard > Authentication**
2. Verify **Anonymous** is enabled
3. No additional configuration needed

*Note: For production, implement proper player authentication*

### Phase 3: Multiplay Server Deployment (15 min)

**Step 1: Install Linux Build Support**
```
1. Editor: File > Build Settings > Dedicated Server
2. Click "Install with Unity Hub"
3. Install these modules:
   ☐ Linux Build Support (IL2CPP)
   ☐ Linux Build Support (Mono)
   ☐ Linux Dedicated Server Build Support
```

**Step 2: Build and Upload Server**
```
1. BuildTools > Linux Server
2. Go to Unity Dashboard: https://dashboard.unity3d.com/multiplay
3. Multiplay Setup > Create a Build
4. Upload the server from Builds/ folder
```

**Step 3: Configure Build Settings**
- **Platform:** Linux
- **Executable:** `Matchplay.x86_64`
- **Query Type:** SQP
- **Launch Parameters:**
  ```
  -ip 0.0.0.0 -port $$port$$ -queryPort $$query_port$$ -logFile $$log_dir$$/matchplaylog.log
  ```

**Step 4: Create a Fleet**
- **Fleet Name:** `matchplay-fleet`
- **Build Config:** Your uploaded build
- **Scaling:**
  - Minimum servers: 1
  - Maximum servers: 5
  - (Increase if expecting high player volume)

### Phase 4: Matchmaker Configuration (10 min)

**Step 1: Create Queue**
1. **Matchmaker Setup > Create Queue**
2. **Queue Name:** `casual-queue`
3. **Max Players per Ticket:** 10
4. *Optional: Create `competitive-queue` for variety*

**Step 2: Create Pool**
1. **Create Pool** (linked to queue)
2. **Select Fleet:** Your Multiplay fleet
3. **Build Config:** Your uploaded build
4. **Timeout:** 
   - 15 seconds (if fleet has min 1 server)
   - 180 seconds (if fleet has min 0 servers)

**Step 3: Match Rules**
1. **Add Match Rules**
2. **Set Region:** Same as your fleet region
3. **Team Definitions:** Configure team sizes/compositions
4. **Filters:** Add any preference filters you want
5. **Finish:** Complete setup

### Phase 5: Test Matchmaking

```
1. Make a client build (File > Build Settings > Build)
2. Run 2+ instances of the build
3. Click "Matchmake" button in each
4. Servers should find match and launch automatically
5. Enjoy your multiplayer game!
```

## 🏗️ Architecture

### System Overview

```
┌─────────────┐         ┌─────────────┐
│   Client 1  │         │   Client 2  │
└──────┬──────┘         └──────┬──────┘
       │                       │
       └───────────┬───────────┘
                   │
            ┌──────▼──────┐
            │  Matchmaker │◄─────── (Finds match, allocates server)
            └──────┬──────┘
                   │
            ┌──────▼──────┐
            │  Multiplay  │◄─────── (Spins up server)
            └──────┬──────┘
                   │
         ┌─────────▼─────────┐
         │  Dedicated Server  │◄─────── (Hosts game)
         └───────────────────┘
```

### Data Flow

1. **Client** → Submits matchmaking ticket with preferences
2. **Matchmaker** → Finds compatible players, allocates server
3. **Multiplay** → Launches dedicated server with configuration
4. **Server** → Receives allocation payload, configures game
5. **Clients** → Connect to server and play together

### Key Components

**Authentication**: Handles player identity (anonymous by default)  
**TicketConfig**: Player preferences (map, mode, etc.)  
**AllocationPayload**: Server configuration from Matchmaker  
**NetworkSpawner**: Manages networked object instantiation  

## 🔧 Troubleshooting

### Issue: "Services not initialized"
```
Solution:
1. Check internet connection
2. Verify Cloud Project is linked (Services > Cloud)
3. Check authentication credentials
4. Restart Unity Editor
```

### Issue: Server won't start locally
```
Solution:
1. Verify ParrelSync clone arguments are set to "server"
2. Check that port 7777 is not in use
3. View Console for detailed error messages
4. Try different port in bootStrap settings
```

### Issue: Matchmake button doesn't work
```
Solution:
1. Verify all services are enabled (Auth, Matchmaker, Multiplay)
2. Check Matchmaker queue exists ("casual-queue")
3. Verify pool is linked to fleet
4. Check internet connection
5. View Editor Console for specific errors
```

### Issue: Players can't connect
```
Solution:
1. Verify firewall allows Unity traffic
2. Check server is running (look for console output)
3. Verify NetworkManager is active
4. Check player has correct match data
5. Review server logs: <project>/Logs/
```

### Issue: High latency/lag
```
Solution:
1. Check regional server deployment matches player region
2. Verify network bandwidth is sufficient
3. Reduce tick rate if needed
4. Check for network congestion
5. Scale fleet if under heavy load
```

## 🔧 Technologies

| Technology | Purpose | Version |
|------------|---------|---------|
| **Unity** | Game engine and framework | 2021.3 LTS+ |
| **Netcode for GameObjects** | Client-server networking | Latest |
| **Transport** | Network transport layer | Included |
| **Authentication Service** | Player identity management | 2.0+ |
| **Matchmaker Service** | Player matching and allocation | Current |
| **Multiplay Service** | Cloud server hosting | Current |
| **ParrelSync** | Local testing and cloning | Latest |

## 📖 Resources

**Official Documentation:**
- [Matchmaker Docs](https://docs.unity.com/matchmaker)
- [Multiplay Docs](https://docs.unity.com/game-server-hosting)
- [Netcode for GameObjects](https://docs-multiplayer.unity3d.com/netcode/)
- [Authentication Service](https://docs.unity3d.com/Manual/com.unity.services.authentication.html)

**Tutorials & Guides:**
- [Dedicated Game Server Sample](https://docs-multiplayer.unity3d.com/netcode/current/learn/sample-dedicated-server/)
- [Unity Services Documentation](https://docs.unity3d.com/Manual/UnityServices.html)
- [Multiplayer Best Practices](https://docs-multiplayer.unity3d.com/netcode/current/learn/bestpractices/)

**Community:**
- [Unity Forums - Multiplayer](https://forum.unity.com/forums/multiplayer.26/)
- [Netcode Discord](https://discord.gg/buMxnnPvTM)

## 🤝 Contributing

Contributions are welcome! Ways you can help:

- 🐛 Report bugs and issues
- ✨ Suggest improvements
- 📖 Improve documentation
- 🔧 Submit code improvements
- 🎮 Share your implementations

Please open an issue first to discuss major changes.

## ⚖️ License

This project is licensed under the **Apache License 2.0** - see the [LICENSE](LICENSE) file for details.

## 📝 Project Notes

- **Tested with:** Unity 2021.3 LTS (PC & Linux Headless)
- **Target Platforms:** PC, Linux, Console (with modifications)
- **Network Architecture:** Client-Server
- **Scalability:** Supports 100+ concurrent matches (with proper fleet scaling)
- **Default Local Server:** `127.0.0.1:7777`
- **Example Match Size:** 2-10 players per match (configurable)

## 🎯 Next Steps

1. ✅ Clone repository and open in Unity
2. ✅ Run locally with "Local" mode (no config needed)
3. ✅ Test with ParrelSync for 2-player local testing
4. ✅ Follow Setup Guide for cloud matchmaking
5. ✅ Deploy to production with Multiplay

---

**Happy multiplayer developing!** 🎮

For questions or issues, please open a GitHub issue or check the troubleshooting section above.
