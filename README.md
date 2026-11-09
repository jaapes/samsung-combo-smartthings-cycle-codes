# Samsung WD53DBA900HZA1 SmartThings cycle codes

Reference for other owners of the Samsung Bespoke AI Laundry Combo **WD53DBA900HZA1**, a ventless heat pump all-in-one washer-dryer.

Through the SmartThings API, cycles show up only as codes such as `Table_02_Course_51`. Samsung does not publish what those codes mean. The 23 cycles here were worked out by selecting each one on the physical panel and reading it back from `samsungce.washerCycle` (`washerCycle` and `supportedCycles`) on 2026-11-08.

The machine-readable copy, including per-cycle options (water temperature, spin, soil, dry level, and so on), is [`data/samsung-WD53DBA900HZA1-cycle-codes.json`](data/samsung-WD53DBA900HZA1-cycle-codes.json).

## Model

| | |
| --- | --- |
| Model | Samsung WD53DBA900HZA1 (Bespoke AI Laundry Combo, ventless heat pump) |
| OCF `mnmo` | `AWM-US-M64-24-WD80` |
| `vid` | `DA-WM-WM-01021` |
| Firmware (`mnfv`) when captured | `20260416.215549` |

## Cycles

Default time is the machine's idle estimate at default settings, written as `h:mm`. Allowed cycle types are the values `setCycleType` accepts for that cycle:

- `allInOne` — wash and dry
- `washingOnly` — drying off
- `dryingOnly` — dry only

| Code | Panel name | Default time | Allowed cycle types |
| --- | --- | --- | --- |
| `55` | Activewear | 2:32 | allInOne, washingOnly, dryingOnly |
| `AD` | AI Opti Wash and Dry | 2:22 | allInOne, washingOnly, dryingOnly |
| `AA` | Air Fluff | 0:30 | dryingOnly |
| `06` | Bedding | 4:19 | allInOne, washingOnly, dryingOnly |
| `65` | Colors | 2:36 | allInOne, washingOnly, dryingOnly |
| `57` | Delicates | 2:24 | allInOne, washingOnly, dryingOnly |
| `52` | Eco Cold | 2:50 | allInOne, washingOnly, dryingOnly |
| `53` | Heavy Duty | 4:32 | allInOne, washingOnly, dryingOnly |
| `96` | Less Microfiber | 2:47 | allInOne, washingOnly |
| `A1` | Normal (panel default) | 1:55 | allInOne, washingOnly, dryingOnly |
| `59` | Perm Press | 2:33 | allInOne, washingOnly, dryingOnly |
| `A7` | Product Care | 5:52 | allInOne, washingOnly, dryingOnly |
| `08` | Rinse and Spin | 2:28 | allInOne, washingOnly, dryingOnly |
| `5B` | Small Load | 1:52 | allInOne, washingOnly, dryingOnly |
| `5F` | Spin Only | 2:21 | allInOne, washingOnly, dryingOnly |
| `85` | Steam Normal | 2:57 | allInOne, washingOnly, dryingOnly |
| `5A` | Steam Sanitize | 5:03 | allInOne, washingOnly, dryingOnly |
| `64` | Steam Whites | 3:13 | allInOne, washingOnly, dryingOnly |
| `51` | Super Speed | 1:35 | allInOne, washingOnly, dryingOnly |
| `A9` | Tide Pod Cold | 2:42 | allInOne, washingOnly, dryingOnly |
| `A8` | Time Dry | 1:00 | dryingOnly |
| `54` | Towels | 3:36 | allInOne, washingOnly, dryingOnly |
| `58` | Wool | 1:29 | allInOne, washingOnly, dryingOnly |

## How to use it

Smart Control has to be on at the washer panel. With it off, the machine rejects remote commands.

Commands are `POST` requests to:

```text
https://api.smartthings.com/v1/devices/<DEVICE_ID>/commands
```

Send `Authorization: Bearer $SMARTTHINGS_TOKEN` and `Content-Type: application/json`. `<DEVICE_ID>` and `$SMARTTHINGS_TOKEN` below are placeholders.

Status reads the cycle back as `Table_02_Course_` plus the code (for example `Table_02_Course_51`). `setWasherCycle` takes the short code only (`51`).

### Select a cycle

`samsungce.washerCycle` / `setWasherCycle`. This selects Super Speed:

```bash
curl -sS -X POST \
  -H "Authorization: Bearer $SMARTTHINGS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "commands": [
      {
        "component": "main",
        "capability": "samsungce.washerCycle",
        "command": "setWasherCycle",
        "arguments": ["51"]
      }
    ]
  }' \
  "https://api.smartthings.com/v1/devices/<DEVICE_ID>/commands"
```

### Turn drying on or off

`samsungce.washerCycle` / `setCycleType`. Pass `allInOne`, `washingOnly`, or `dryingOnly`. A cycle accepts only the types listed for it above. Less Microfiber (`96`) has no `dryingOnly`. Time Dry (`A8`) and Air Fluff (`AA`) are `dryingOnly` only.

This turns drying off (wash only):

```bash
curl -sS -X POST \
  -H "Authorization: Bearer $SMARTTHINGS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "commands": [
      {
        "component": "main",
        "capability": "samsungce.washerCycle",
        "command": "setCycleType",
        "arguments": ["washingOnly"]
      }
    ]
  }' \
  "https://api.smartthings.com/v1/devices/<DEVICE_ID>/commands"
```

Use `"allInOne"` to wash and dry, or `"dryingOnly"` to dry only.

### Time Dry and Air Fluff

`samsungce.dryerDryingTime` / `setDryingTime` sets the length for Time Dry (`A8`) and Air Fluff (`AA`). Select that cycle first. Minutes captured for both cycles are `0`, `20`, `30`, `40`, `50`, `60`, `90`, `120`, `150`, and `180`. Time Dry defaults to 60 minutes; Air Fluff defaults to 30.

```bash
curl -sS -X POST \
  -H "Authorization: Bearer $SMARTTHINGS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "commands": [
      {
        "component": "main",
        "capability": "samsungce.dryerDryingTime",
        "command": "setDryingTime",
        "arguments": ["60"]
      }
    ]
  }' \
  "https://api.smartthings.com/v1/devices/<DEVICE_ID>/commands"
```

### Start, pause, or stop

`washerOperatingState` / `setMachineState` takes `run`, `pause`, or `stop`.

```bash
curl -sS -X POST \
  -H "Authorization: Bearer $SMARTTHINGS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "commands": [
      {
        "component": "main",
        "capability": "washerOperatingState",
        "command": "setMachineState",
        "arguments": ["run"]
      }
    ]
  }' \
  "https://api.smartthings.com/v1/devices/<DEVICE_ID>/commands"
```

## Caveats

- Minutes are the machine's idle estimate at default settings; real runs vary (bulky loads can stall in spin).
- Remote dry on/off was tested only on 96 Less Microfiber; other codes list it in the schema.
- A1, 06, 54, 58 options not captured in detail.

## Other models

Other Bespoke AI Combo models (for example the vented WD90F53AV) list the same 23 cycles, but those listings are unverified. Panel names in this repo were checked only on the ventless WD53DBA900HZA1.

## License

MIT. See [LICENSE](LICENSE).
