# GPIO Library for ESP32

A GPIO (General Purpose Input/Output) library for **BlueScript** on ESP32.
This package provides a high-level interface to control pins, read digital states, and handle hardware interrupts using the `driver/gpio.h` ESP-IDF APIs.

## Installation

Install this package in your BlueScript project:

```bash
bscript project install https://github.com/bluescript-lang/pkg-gpio-esp32.git
```

## Usage

### Basic: Blink an LED

```typescript
import * as time from "time";
import { GPIO, PinMode, PinLevel } from "gpio";

// Configure GPIO 2 (Onboard LED) as Output
const led = new GPIO(2, PinMode.Output);

while (true) {
    led.write(PinLevel.High);
    time.delay(500);
    led.write(PinLevel.Low);
    time.delay(500);
}

led.close();
```

### Advanced: Button Interrupts

You can listen for changes on a pin (interrupts) without blocking the main loop.

```typescript
import { GPIO, PinMode, PinLevel, PullMode, InterruptEdge, InterruptType } from "gpio";

const button = new GPIO(0, PinMode.Input);

// Handle button press (Falling edge)
button.onChange(InterruptEdge.Falling, InterruptType.Soft, () => {
    console.log("Button Pressed!");
});
```

## API Reference

### Class: `GPIO`

#### `constructor(pin: integer, mode: PinMode)`
Initializes a GPIO pin with the specified mode.
- **pin**: The GPIO number (e.g., `2`, `23`).
- **mode**: Initial `PinMode` (see Enums).

#### `write(level: PinLevel): void`
Sets the output level of the pin.
- **level**: `PinLevel.High` or `PinLevel.Low`.

#### `read(): PinLevel`
Reads the current level of the pin.
- **Returns**: `PinLevel.High` or `PinLevel.Low`.

#### `setMode(mode: PinMode): void`
Changes the pin mode dynamically.

#### `setPullMode(mode: PullMode): void`
Configures internal pull-up/pull-down resistors.
- **mode**: `PullMode` (see Enums).

#### `onChange(edge: InterruptEdge, type: InterruptType, listener: () => void): void`
Registers an interrupt handler function.
- **edge**: The trigger condition (Rising, Falling, or Change).
- **type**:
    - `InterruptType.Soft`: The listener runs on the **Main Thread**. Safe to use all BlueScript features (console.log, allocation, etc.).
    - `InterruptType.Hard`: The listener runs directly in the **ISR (Interrupt Service Routine)**. extremely fast, but **very restricted**. Do not allocate memory or use heavy operations.
- **listener**: The callback function to execute.

#### `offChange(): void`
Removes the interrupt handler and disables the interrupt for this pin.

#### `close(): void`
Resets the pin and releases resources.


## Enums

### `PinMode`
| Name | Value | Description |
| :--- | :--- | :--- |
| `Input` | 0 | Input only. |
| `Output` | 1 | Output only. |
| `InputOutput` | 2 | Both Input and Output. |

### `PinLevel`
| Name | Value | Description |
| :--- | :--- | :--- |
| `Low` | 0 | 0V (GND) |
| `High` | 1 | 3.3V (VCC) |

### `PullMode`
| Name | Value | Description |
| :--- | :--- | :--- |
| `Floating` | 0 | No pull resistor. |
| `PullupOnly` | 1 | Internal pull-up enabled. |
| `PulldownOnly` | 2 | Internal pull-down enabled. |
| `PullupPulldown`| 3 | Both pull-up and pull-down enabled. |

### `InterruptEdge`
| Name | Value | Description |
| :--- | :--- | :--- |
| `Rising` | 0 | Triggers when signal goes Low → High. |
| `Falling` | 1 | Triggers when signal goes High → Low. |
| `Change` | 2 | Triggers on any change. |

### `InterruptType`
| Name | Value | Description |
| :--- | :--- | :--- |
| `Soft` | 0 | Queues event to Main Thread (Recommended). |
| `Hard` | 1 | Runs inside ISR (Advanced use only). |

