# LoRa Communicator

A Flutter-based mobile application for communicating with Bluetooth-enabled LoRa devices, designed around a lightweight chat-style interface and BLE transport layer. This project is intended for use with compatible LoRa modules such as Heltec devices and demonstrates BLE discovery, connection management, message framing, and remote app control.

## Overview

LoRa Communicator provides a simple mobile UI for:

- scanning for nearby LoRa-capable devices
- connecting to a target module over Bluetooth
- sending text messages to a selected recipient
- receiving messages from the connected device
- persisting chat history locally on-device
- checking remote configuration to enable or disable the app

The project serves as a practical prototype for BLE-to-LoRa communication, combining a modern Flutter front end with low-level device communication, message packaging, and local state management.

## Features

- BLE device scanning for LoRa modules
- Device selection and connection workflow
- Chat-style messaging interface
- Message sending and receiving through BLE
- Packet framing and raw-byte handling
- Local storage of message history using Shared Preferences
- Remote app-enable/disable check via a hosted config file
- Android Bluetooth permission handling
- Dark mode UI optimized for field use

## Tech Stack

- Flutter / Dart
- flutter_blue_plus for BLE communication
- Provider for state management
- permission_handler for runtime permissions
- uuid for packet/message IDs
- intl for localized formatting
- shared_preferences for local persistence
- http for remote configuration fetches

## Architecture

The app is organized around a small service and provider architecture:

- `BleService`
  - handles Bluetooth scanning
  - manages device connection and characteristic discovery
  - sends data to the target LoRa module
  - listens for incoming raw BLE data

- `PacketFramerService`
  - receives raw data from BLE
  - decodes incoming packets
  - manages message framing and ACK handling
  - translates device traffic into app-level chat messages

- `ChatProvider`
  - stores and retrieves chat history
  - sends user-generated messages
  - listens for incoming framed messages
  - updates message statuses

- `RemoteConfigService`
  - fetches a remote config file
  - determines whether the app should be enabled or blocked

## App Flow

1. App starts and checks remote configuration
2. If enabled, the user is shown the chat screen
3. User taps “Select Device”
4. App scans for nearby Bluetooth devices matching a Heltec LoRa pattern
5. User selects a target device
6. BLE connection is established and services/characteristics are discovered
7. Messages are sent via BLE to the connected LoRa module
8. Incoming device messages are decoded and shown in the chat interface

## Project Structure

```text
lora_comms/
├── lib/
│   ├── constants/
│   │   └── app_constants.dart
│   ├── models/
│   │   └── chat_message.dart
│   ├── providers/
│   │   └── chat_provider.dart
│   ├── screens/
│   │   ├── blocked_screen.dart
│   │   ├── chat_screen.dart
│   │   ├── loading_screen.dart
│   │   ├── settings_screen.dart
│   │   └── splash_screen.dart
│   ├── services/
│   │   ├── ble_service.dart
│   │   ├── packet_framer_service.dart
│   │   └── remote_config_service.dart
│   ├── widgets/
│   │   └── chat_bubble.dart
│   └── main.dart
├── android/
├── ios/
├── pubspec.yaml
├── README.md
└── assets/
```

## Getting Started

### Prerequisites

- Flutter SDK 3.0 or newer
- Android device or emulator with Bluetooth support
- A compatible LoRa module using BLE communication
- A device in range for testing

### Installation

1. Clone the repository

```bash
git clone https://github.com/oddproblem/lora_comms.git
cd lora_comms
```

2. Install dependencies

```bash
flutter pub get
```

3. Run the app

```bash
flutter run
```

### Android permissions

This project requests Bluetooth-related permissions at runtime, especially for Android devices:

- Bluetooth scan
- Bluetooth connect

These permissions are required for the BLE discovery and communication flow.

## Bluetooth Communication

The app searches for devices whose platform name contains:

```text
Heltec-LoRa
```

Once a target device is selected, it attempts to establish a BLE connection and discovers the required service and characteristics needed for transmit/receive operations.

The app currently expects a paired LoRa module to expose the required BLE UUIDs and service configuration used by the project.

## Message Format

The app uses a simple message pipeline for communication:

- outgoing: `recipient_id,message`
- incoming: raw Bluetooth payloads are decoded and mapped into chat messages
- ACK status handling is included in the framing layer and is prepared for confirmation responses from a peer device

This makes it possible to validate a low-overhead communication channel suitable for simple messaging between mobile and LoRa-enabled hardware.

## Remote App Control

The app includes a remote configuration check through `RemoteConfigService`. It fetches a config file from a GitHub-hosted raw URL and determines whether the app should allow access.

If the remote configuration fetch fails, the app fails closed and disables access by default.

This is useful for:

- maintenance mode
- emergency access control
- remote lockdown of the application

## Notes

This project is a prototype and is best suited for experimental or hardware-integration development. It focuses on the basic communication workflow and demonstrates a clean structure for extending into:

- more robust packet validation
- end-to-end encryption
- error recovery
- custom LoRa protocol definitions
- multi-device routing
- offline fallback handling

## License

This project currently does not include a license file. If you want to publish or distribute it, you should add a license before public release.

## Contributing

Contributions, bug reports, and feature extensions are welcome. If you are improving the communication protocol, BLE handling, or UX flow, feel free to open a pull request or improve the project structure.

---

Created for experimental LoRa-based mobile communication and BLE device integration.
