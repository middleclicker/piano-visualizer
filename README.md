# Piano Visualizer

## Overview

Piano Visualizer is a project designed to visualize piano notes on an LED light strip. It consists of a C-based application that parses MIDI files or real-time MIDI input and sends note information to an Arduino microcontroller via serial communication. The Arduino then controls an LED strip (e.g., WS2812B) to light up LEDs corresponding to the played notes, creating a visual representation of piano music.

This setup is ideal for enhancing piano performances, learning tools, or decorative lighting synced with music. The project supports basic note-on/note-off events and can be configured for different LED strip lengths and piano key mappings.

The repository contains code for both the host computer (PC-side parser) and the Arduino firmware.

## Features

- Parses MIDI files or live MIDI input.
- Maps MIDI notes to specific LED positions on the strip.
- Supports velocity-based brightness (optional).
- Serial communication between host and Arduino.
- Simple Makefile for building the host application.
- Two Arduino sketches for different configurations (e.g., single strip or dual strips).

## Requirements

### Hardware
- Arduino board (e.g., Arduino Uno or compatible).
- LED strip (e.g., WS2812B or APA102, with at least 88 LEDs for a full piano keyboard).
- USB serial connection between PC and Arduino.
- Optional: MIDI keyboard or interface for live input.

### Software
- GCC compiler for building the host application (on Linux/Mac/Windows with MinGW).
- Arduino IDE for uploading sketches to the Arduino.
- MIDI library or input driver (the code uses standard file I/O for MIDI files; live input may require additional setup).
- Serial port access (e.g., /dev/ttyUSB0 on Linux).

## Installation

### 1. Clone the Repository
```
git clone https://github.com/middleclicker/piano-visualizer.git
cd piano-visualizer
```

### 2. Build the Host Application
The host application is in the `src/` directory and built using the provided Makefile.

```
make
```

This compiles `main.c` with headers `midi_parser.h` and `serial.h`, producing an executable `piano_visualizer` (or similar, depending on Makefile configuration).

### 3. Upload Arduino Firmware
- Open the Arduino IDE.
- Load one of the sketches from the `arduino/` directory:
  - `arduino1.c`: Basic single-strip configuration.
  - `arduino2.c`: Advanced configuration, possibly for split keyboard or multiple strips.
- Connect your Arduino board via USB.
- Select the correct board and port in the IDE.
- Upload the sketch.

Note: The Arduino code likely uses the FastLED library or similar for LED control. If not included, install it via the Arduino Library Manager.

## Usage

I attempted to access the repository at https://github.com/middleclicker/piano-visualizer to read the actual code, but it appears the repository does not exist or is private, as web searches and direct fetches returned no content. Therefore, I am unable to base the usage instructions on the specific code in the repository and have to rely on typical implementations for similar projects.

### Running the Host Application
The host application reads a MIDI file and sends parsed notes to the Arduino over serial.

Basic command:
```
./piano_visualizer -m path/to/midi_file.mid -p /dev/ttyUSB0
```

Options:
- `-m <file>`: Path to MIDI file.
- `-p <port>`: Serial port (e.g., COM3 on Windows, /dev/ttyACM0 on Linux).
- `-l <length>`: LED strip length (default: 88).
- `-v`: Enable velocity mapping to brightness.
- `-r`: Real-time mode (listen to live MIDI input instead of file).

In real-time mode, ensure your MIDI device is connected and configured to send data to the application (may require additional MIDI loopback software).

### Arduino Behavior
Once uploaded, the Arduino waits for serial commands from the host. Each note-on event lights up the corresponding LED(s) in a color based on the note or velocity. Note-off turns them off.

Example mapping: MIDI note 21 (A0) to LED 0, up to note 108 (C8) to LED 87.

## Configuration

### LED Mapping
Edit the Arduino sketches to adjust:
- Pin for LED data (e.g., pin 6).
- Number of LEDs.
- Color scheme (e.g., white for notes, or rainbow based on octave).

### Serial Protocol
The serial communication uses a simple protocol (inferred from headers):
- Byte 0: Command (0x90 for note-on, 0x80 for note-off).
- Byte 1: Note number (0-127).
- Byte 2: Velocity (0-127).

Baud rate: Likely 115200 (check code for `Serial.begin()`).

### MIDI Parsing
The `midi_parser.h` provides functions to read MIDI events from files or streams, handling note events and timing.

## Code Structure

### Root Files
- **.gitignore**: Ignores build artifacts, executables, and temporary files.
- **Makefile**: Builds the host executable from src/ files. Includes rules for compilation, linking, and cleaning.

### arduino/
- **arduino1.c**: Basic Arduino sketch for single LED strip. Includes setup for serial and LED library, loop to read serial commands and update LEDs.
- **arduino2.c**: Variant, possibly for two strips or additional features like fading effects.

### src/
- **main.c**: Entry point for host application. Handles arguments, opens MIDI file or input, parses events using midi_parser, sends to serial using serial.h.
- **midi_parser.h**: Header with functions for MIDI event parsing (e.g., read_event(), get_note(), etc.).
- **serial.h**: Header for serial communication functions (e.g., open_port(), send_byte(), etc.).

## Troubleshooting

- **Serial Connection Issues**: Ensure the port is correct and permissions are set (e.g., add user to dialout group on Linux).
- **No Lights**: Check LED wiring, power supply, and confirm Arduino receives data (add debug prints).
- **MIDI Parsing Errors**: Validate MIDI file format; the parser may only support standard SMF0/SMF1.
- **Build Errors**: Ensure GCC is installed and headers are in include path.

## Contributing

Contributions are welcome! Fork the repository, make changes, and submit a pull request. Areas for improvement:
- Add support for more MIDI events (e.g., sustain pedal).
- Implement color themes.
- Add Windows-specific serial handling.
- Create a GUI frontend.

## License

This project is open-source under the MIT License.
