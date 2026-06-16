# C.O.I.S.A
**Code fOr calculatIon of uSing a drivetrAin**

A gear ratio analyzer for brushed DC line follower robots. Fill in your motor specs and robot dimensions, and COISA tells you how fast your robot will go, how much torque margin you have, whether your wheels will slip, and which gear combination is best for your build.

Built by [LARI — Liga Acadêmica de Robótica e Inovação / Unifor](https://www.unifor.br).

---

## What it does

- Evaluates your current gear ratio across multiple PWM levels
- Scores every pinion/wheel combination in a configurable search range
- Flags slip risk and torque failure
- Exports results to a formatted Excel file (`.xlsx`)

---

## Requirements

- Python 3.x
- `openpyxl` (for Excel export)

```bash
pip install openpyxl
```

---

## Usage

1. Open `COISA_EN.py`
2. Fill in your parameters in the configuration block at the top (everything above the `CALCULATIONS` line)
3. Run it:

```bash
python COISA_EN.py
```

Results print to the terminal and, if `EXPORT_XLSX = True`, are saved to `coisa.xlsx`.

---

## Configuration

All parameters are at the top of the file. You only need to edit those — never touch anything below the `CALCULATIONS` line.

### Motor
| Variable | What it is | Example |
|---|---|---|
| `MOTOR_KV` | RPM per volt (from datasheet) | `12000` |
| `MOTOR_VOLTAGE` | Battery nominal voltage | `3.7` (1S LiPo) |
| `MOTOR_STALL_TORQUE` | Peak torque in mNm | `1.4` |
| `MOTOR_STALL_CURRENT` | Current at stall in amps | `1.8` |

### Gear Setup
| Variable | What it is | Example |
|---|---|---|
| `MY_PINION_TEETH` | Teeth on the motor shaft gear | `11` |
| `MY_WHEEL_TEETH` | Teeth on the wheel gear | `70` |
| `GEAR_MODULE` | Tooth size in mm | `0.3` |
| `GEAR_EFFICIENCY` | Power retained after mesh losses | `0.85` |

### Wheel
| Variable | What it is |
|---|---|
| `WHEEL_DIAMETER_MM` | Set to `None` if the wheel IS the gear (diameter is calculated automatically). Otherwise enter the actual rolling diameter in mm. |

### Robot
| Variable | What it is | Example |
|---|---|---|
| `ROBOT_MASS_G` | Total mass in grams — weigh it with the battery | `80` |
| `FRICTION_COEFF` | Wheel-to-floor grip coefficient | `0.45` |
| `NUM_DRIVE_WHEELS` | Wheels in contact with the floor | `4` |
| `NUM_MOTORS` | Total motors in the drivetrain | `2` |

### Search
| Variable | What it is |
|---|---|
| `TARGET_SPEED_MS` | Target speed in m/s at 50% PWM. Set to `None` to let torque guide the search. |
| `PINION_RANGE` | Range of pinion tooth counts to test |
| `WHEEL_RANGE` | Range of wheel tooth counts to test |
| `MIN_CENTER_DIST_MM` | Minimum shaft-to-shaft distance your chassis can print reliably |
| `MAX_WHEEL_DIAM_MM` | Maximum wheel diameter that fits your chassis |

---

## Output

### Per-ratio table
Shows motor RPM, wheel RPM, speed, torque, torque margin, slip risk, and a rating at each PWM point.

| Rating | Torque margin at 50% PWM |
|---|---|
| EXCELLENT | ≥ 40% |
| GOOD | 25 – 39% |
| MARGINAL | 10 – 24% |
| WEAK | 0 – 9% |
| FAIL | < 0% — motor cannot move the robot |

### Ideal Ratio Finder
Scans all combinations in your search range and ranks them by a composite score (0–100) based on torque margin, speed, current draw, and printability.

### Excel export
When `EXPORT_XLSX = True`, saves all ranked combinations to `coisa.xlsx` with a formatted header and a red→green color scale on the score column.

---

## Tips & known limitations

**Stall torque** values from datasheets vary between manufacturers and even between units of the same motor. Measure your motor directly if possible for better accuracy.

**Friction coefficient** depends heavily on your wheel material and floor surface. Rubber on smooth MDF ≈ 0.45. A printed nylon gear running directly on the floor ≈ 0.25. Adjust `FRICTION_COEFF` to match your setup.

**Torque margin** — keep it above 30% at 50% PWM for reliable cornering. Below that, the robot will struggle under load and behave inconsistently through turns.

**Center distance** is the exact motor-shaft-to-wheel-axle measurement in your CAD file. Print a test bracket, check that the gears mesh properly, then lock that value into your design before finalizing the chassis.

**Hunting tooth** — gear pairs where GCD(pinion, wheel) = 1 are preferred. Every tooth eventually contacts every other tooth, distributing wear evenly. The Ideal Ratio Finder filters for this automatically.

**Target speed** — set `TARGET_SPEED_MS` if your track has a known optimal speed. Leave it as `None` to let the torque margin guide the search instead.

---

## License

MIT — see [LICENSE](LICENSE).

---

*COISA is a LARI internal tool released open source for the robotics community.*
