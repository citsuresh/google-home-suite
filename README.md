# Google Home Suite

A cohesive suite that enables local-network communication between an **Android foreground service** and multiple **client applications** (e.g., .NET console client) to **discover devices** and **toggle device state**.

> **Components**
> - `android-service/` — Fork of `google-home-api-sample-app-android` with a foreground service to serve local network clients.
> - `clients/dotnet-console/` — .NET console client to list devices and toggle device state.
> - Future clients (CLI, web, desktop, etc.) can be added under `clients/`.

---

## Table of Contents

- #architecture
- #repository-structure
- #prerequisites
- #getting-started
  - #clone--initialize-submodules
  - #keeping-submodules-in-sync
- #build--run
  - #android-foreground-service
  - #net-console-client
- #configuration
- #protocol
- #integration-testing
- #versioning--releases
- #development-workflow
- #contributing
- #license

---

## Architecture

The suite follows a **service–client** pattern over the local network:

- **Android Foreground Service**
  - Runs locally on the Android device.
  - Exposes a network endpoint (e.g., TCP/UDP/WebSocket/HTTP) for discovery and control.
  - Implements a simple wire protocol for:
    - `LIST_DEVICES`
    - `TOGGLE_STATE`
    - `PING`
    - `ERROR` responses
- **Clients**
  - Discover devices via the service and issue commands.
  - First client: **.NET console** (lists devices & toggles state).
  - Future clients can reuse the protocol (CLI, web dashboard, desktop UI).

---

## Repository Structure

```text
google-home-suite/
├─ google-home-bridge-android-service/                # Submodule: Fork of `google-home-api-sample-app-android` with a foreground service to serve local network clients.
│  └─ ...
├─ clients/
│  ├─ dotnet-console/              # Submodule: .NET console client
│  │  └─ src/ ...
│  └─ README.md
└─ README.md
```

---

## Prerequisites

- **Git** with submodule support
- **Android**
  - Android Studio (latest)
  - Android SDK & tools
- **.NET**
  - .NET SDK `>= 8.0`

---

## Getting Started

### Clone & Initialize Submodules

```bash
git clone https://github.com/<you>/google-home-suite.git
cd google-home-suite
git submodule update --init --recursive
```

Add submodules:

```bash
git submodule add https://github.com/<you>/google-home-api-sample-app-android android-service
git submodule add https://github.com/<you>/google-home-net-console-client clients/dotnet-console
git commit -m "Add android-service and dotnet-console as submodules"
git push origin main
```

### Keeping Submodules in Sync

```bash
git submodule update --remote --merge
```

---

## Build & Run

### Android Foreground Service

1. Open `android-service/` in Android Studio.
2. Build and run on emulator or device.
3. Ensure the service is reachable on the network.

### .NET Console Client

```bash
cd clients/dotnet-console
dotnet build
dotnet run -- --host 192.168.1.50 --port 8080 --command list
dotnet run -- --host 192.168.1.50 --port 8080 --command toggle --device-id <ID>
```

---

## Configuration

Example `config.json`:

```json
{
  "serviceHost": "192.168.1.50",
  "servicePort": 8080,
  "protocolVersion": 1,
  "timeoutMs": 4000
}
```

---

## Protocol

Document in `docs/protocol.md`:

- Transport: TCP/HTTP/WebSocket
- Commands: `LIST_DEVICES`, `TOGGLE_STATE`
- Example:

```json
{ "type": "LIST_DEVICES", "protocolVersion": 1 }
```

Response:

```json
{
  "type": "DEVICES",
  "devices": [
    { "id": "lamp-01", "name": "Living Room Lamp", "state": "on" }
  ]
}
```

---

## Integration Testing

Add CI workflow to:
- Build Android app
- Launch emulator or mock service
- Run .NET client tests

---

## Versioning & Releases

- Tag each component repo separately.
- Tag umbrella repo for suite releases (pin submodule SHAs).

---

## Development Workflow

- Update submodules for new versions.
- Keep `docs/protocol.md` updated.
- Use feature branches and PRs.

---

## Contributing

See `docs/architecture.md` and `docs/protocol.md`. Add tests and update docs for changes.

---

## License

- Android service: original license from Google repo.
- Clients and umbrella repo: MIT (or your chosen license).
