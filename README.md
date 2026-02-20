# Dynamic EV Charging Automation (3-Phase Amperage)

A Home Assistant blueprint for dynamic EV charging control optimized for **3-phase electrical systems** with **HomeWizard P1 meters** and **Tuya-based EV chargers**.

> **Fork of** [EDV11/electric-vehicle-ev-dynamic-charging-home-assistant-](https://github.com/EDV11/electric-vehicle-ev-dynamic-charging-home-assistant-) — this version adds per-phase amperage monitoring.

## Why I Built This

I was experiencing a situation where a single phase was overloading while charging my EV, with the oven running and cooking on my 3-phase induction cooktop. The original automation didn't adjust the charging amps because the overall combined limits of my mains connection hadn't been reached yet — but one individual phase was already at its limit.

This version monitors each phase independently and scales back charging based on the most constrained phase, preventing this scenario.

## Core Features

- **Per-Phase Protection**: Monitors each phase individually using the bottleneck principle — charging is limited by the most constrained phase, even if other phases have plenty of headroom.
- **Intuitive Configuration**: Uses amperage values directly — simply copy the numbers from your circuit breakers (e.g., 25A per phase).
- **Safety Margin for Interval Changes**: The safety margin accounts for load changes that happen between adjustment cycles (1, 3, or 5 minutes) — like when you turn on the oven or start cooking.
- **Grid Congestion Management**: Optional weekday peak-hour capacity limiting.
- **1-Phase or 3-Phase Support**: Configurable mains connection type.
- **Triple-Phase Charger Monitoring**: Subtracts real-time charger draw from all phases for accurate house load calculation.
- **Gradual Adjustment**: ±1A steps every 5 seconds to prevent charger errors.

## Installation

1. Copy the raw URL: `https://raw.githubusercontent.com/allarddewinter/electric-vehicle-ev-dynamic-charging-home-assistant-amps/main/Dynamic-EV-Charging-Automation.yaml`
2. In Home Assistant: **Settings → Automations & Scenes → Blueprints → Import Blueprint**
3. Paste the URL and click **Import**

Or use the direct import button (if viewing on GitHub).

## Configuration

| Input | Description |
|-------|-------------|
| Mains Type | 1-phase or 3-phase |
| Grid Limit | Per-phase breaker limit (e.g., 25A) — copied from your circuit breaker |
| Safety Margin | Buffer for load changes between intervals (e.g., 10%) |
| Peak Hour Filter | Enable weekday capacity limiting |
| P1 Sensors | Map L1/L2/L3 current sensors |
| Charger Sensors | Map real-time charger current |
| Charger Controls | Set amperage and start/stop entities |

## Version History

| Version | Description |
|---------|-------------|
| **v3.0** | Per-phase amps-based monitoring, bottleneck principle, grid congestion filter |
| v2.1 | Original watts-based version with day/night scheduling |

### v3.0 Highlights

Major architecture change from watts to amps-based monitoring:

- **Per-Phase Monitoring**: Calculates available capacity per phase using the bottleneck principle
- **Unified Grid Limit**: Single amperage limit per phase (replaces day/night power limits)
- **Grid Congestion Filter**: Weekday peak-hour capacity limiting
- **Intuitive Setup**: Uses amperage values from your circuit breakers

## Safety Logic: The Bottleneck Principle

```
Headroom_per_phase = (Grid_Limit × Safety_Margin) - (Grid_Current - Charger_Current)
Charging_Amps = min(headroom_L1, headroom_L2, headroom_L3)
```

The charger runs at the minimum headroom across all phases — protecting the weakest link.

## Breaking Changes from v2.1

Existing v2.1 configurations are incompatible. You must re-configure the blueprint to map:
- Individual L1/L2/L3 P1 current sensors
- Individual L1/L2/L3 charger current sensors

## Community Discussion

https://community.home-assistant.io/t/dynamic-ev-charging-automation
