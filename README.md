## README.md 案

# NyeMIDI

**NyeMIDI** is a lightweight MIDI mapping and VRChat world-integration toolkit for live music performance.

It turns performance data from devices such as **Digitone**, **Digitakt**, and other MIDI controllers into VRChat-ready **Note / Control Change interactions**, then connects those interactions to world-side visual states, props, lighting, animation, and performance feedback.

NyeMIDI is part of **EUB-SW02 / Emnyeca's Utility Build Series**.

## What NyeMIDI Does

NyeMIDI provides a practical bridge between hardware performance and VRChat world expression.

It is designed for musicians who want to use familiar hardware controls, and for world creators who want to receive clean, predictable MIDI-driven signals without building a custom MIDI system from scratch.

NyeMIDI consists of two main parts:

1. **NyeMIDI Mapper**
   A lightweight GUI application for musicians.
   It receives MIDI from devices such as Digitone, Digitakt, or other controllers, then maps, filters, scales, converts, and outputs VRChat-friendly MIDI messages.

2. **NyeMIDI World Package**
   A Unity / UdonSharp package for VRChat world creators.
   It receives Realtime MIDI inside the world and connects Note / CC events to prefabs, materials, animator parameters, synced states, and custom Udon behaviours.

The goal is to separate performance-side MIDI handling from world-side implementation, so both musicians and world creators can work without unnecessary technical complexity.

## Core Concept

VRChat worlds can work with Realtime MIDI through Udon. In practice, the most useful and stable event types for world interaction are:

* Note On
* Note Off
* Control Change

NyeMIDI uses those messages as the common language between the performer and the world.

Hardware devices may produce different kinds of MIDI behaviour. Some controls send notes, some send absolute CC values, and some encoders send relative movement values. NyeMIDI normalizes those differences into a predictable mapping layer.

The performer can shape the MIDI signal in a lightweight GUI.
The world creator can receive the result through a compact Unity package.

## Intended Users

NyeMIDI is designed for two groups.

### Musicians and Performers

For musicians, NyeMIDI provides a simple way to use hardware controls during VRChat performances.

Examples:

* Trigger a world object when a Digitakt trig is pressed.
* Make a light pulse when a Digitone track or note is played.
* Use an encoder to control brightness, opacity, blend amount, or animation position.
* Turn a hardware button into a mute / unmute or scene-state toggle.
* Adjust MIDI routing without editing Unity or Udon scripts.
* Save performance mappings as reusable presets.

The performer should not need to understand Udon networking or Unity component wiring just to use a MIDI controller on stage.

### VRChat World Creators

For world creators, NyeMIDI provides an installable package with prefabs and UdonSharp components.

Examples:

* Drop a MIDI receiver prefab into a world.
* Assign MIDI channels, notes, and CC numbers in the Inspector.
* Connect incoming MIDI values to material emission.
* Drive Animator parameters from MIDI.
* Expose simple target components for lights, props, stage devices, and visual systems.
* Import mapping data prepared by the performer.
* Use optional synchronization components when the result needs to be visible to other players.

The world package should be easy to integrate into an existing VRChat world without restructuring the whole project.

## Product Structure

NyeMIDI is organized into two complementary layers.

```text
NyeMIDI
├─ NyeMIDI Mapper
│  ├─ MIDI input selection
│  ├─ MIDI output selection
│  ├─ Trigger mapping
│  ├─ CC mapping
│  ├─ Relative encoder decoding
│  ├─ Scaling / smoothing / filtering
│  ├─ Preset management
│  └─ VRChat-ready MIDI output
│
└─ NyeMIDI World Package
   ├─ UdonSharp MIDI receiver
   ├─ Router prefabs
   ├─ Note targets
   ├─ CC targets
   ├─ Toggle targets
   ├─ Emission / material targets
   ├─ Animator targets
   ├─ Optional synced state components
   └─ Example scenes
```

## NyeMIDI Mapper

**NyeMIDI Mapper** is the musician-facing control application.

It receives MIDI input from external hardware and outputs a clean MIDI stream intended for VRChat Realtime MIDI.

The Mapper is designed to stay lightweight. It is not a DAW, sequencer, synth editor, or performance host. Its job is to make live MIDI control easier to route and safer to use in VRChat.

### Mapper Features

* Select MIDI input device.
* Select MIDI output device.
* Monitor incoming MIDI activity.
* Map notes, trigs, buttons, pads, CCs, and encoders.
* Convert incoming MIDI into VRChat-friendly Note / CC messages.
* Decode relative encoders.
* Scale and invert values.
* Apply dead zones.
* Apply smoothing.
* Quantize values for stable world-side use.
* Rename controls in musician-friendly language.
* Save and load mapping presets.
* Export mapping data for world creators.

### Typical Mapper Flow

```text
Digitone / Digitakt / MIDI Controller
        ↓
NyeMIDI Mapper
        ↓
VRChat MIDI Input
        ↓
NyeMIDI World Package
        ↓
World objects, lights, materials, animation, synced states
```

## Supported MIDI Concepts

NyeMIDI uses Note and Control Change as its primary output language.

### Notes

Notes are used for discrete actions.

Examples:

* Pad hit
* Trig press
* Track trigger
* Momentary button
* One-shot visual pulse
* Scene cue

### Control Change

Control Change messages are used for continuous or stepped values.

Examples:

* Emission intensity
* Object brightness
* Material blend
* Animator float
* Filter-like visual movement
* Encoder-controlled state
* Fader-style value control

### Relative Encoders

Some hardware encoders do not send an absolute value. Instead, they send movement information.

NyeMIDI includes configurable relative encoder decoding so that different controller behaviours can be converted into stable values.

Supported decoder concepts:

* Absolute CC
* Relative binary offset
* Relative two's complement
* Signed step
* Increment / decrement style movement

The exact mode can be selected per mapping.

## NyeMIDI World Package

**NyeMIDI World Package** is the Unity / UdonSharp side of NyeMIDI.

It is designed to be imported into VRChat world projects through a package-friendly structure.

The package provides prefabs and components that receive MIDI events and apply them to world-side targets.

## World Package Features

* Realtime MIDI receiver components.
* Channel / Note / CC routing.
* Note-to-pulse targets.
* CC-to-value targets.
* Toggle targets.
* Material emission targets.
* Animator bool / trigger / float targets.
* Object active-state targets.
* Custom Udon event targets.
* Optional value smoothing.
* Optional quantized synchronization.
* Example prefabs.
* Example scenes.
* Mapping import support.

## World Integration Philosophy

NyeMIDI should be easy to integrate into existing worlds.

The world creator should be able to:

1. Import the package.
2. Add a receiver prefab.
3. Add one or more target prefabs.
4. Assign channels, notes, CCs, or imported mappings.
5. Connect targets to world objects.
6. Test with VRChat Realtime MIDI.

The package should not require a full custom framework, external Python process, OSC receiver, or project-wide architecture change.

## Synchronization Model

MIDI input is treated as local input.

For shared world visuals, NyeMIDI provides optional synchronized state components. These components should synchronize reduced state rather than raw MIDI event streams.

Recommended behaviour:

* Local response is applied immediately.
* High-frequency smoothing remains local.
* Networked values are quantized.
* Sync occurs only when state changes meaningfully.
* Raw MIDI spam is not forwarded directly over the network.

This keeps performance responsive while avoiding unnecessary network load.

## Example Use Cases

### 1. Trigger to Light Pulse

A Digitakt trig sends a note.
NyeMIDI maps it to a VRChat-ready note.
The world package receives the note and flashes a material emission.

### 2. Encoder to Brightness

A Digitone encoder sends CC or relative movement.
NyeMIDI Mapper decodes it into a stable CC value.
The world package maps that CC to light or material intensity.

### 3. Button to Toggle

A controller button sends a note or CC.
NyeMIDI turns it into a toggle state.
The world package applies it to a prop, mute indicator, object state, or Animator parameter.

### 4. Performance Preset

A performer saves a mapping preset for a specific live set.
The world creator imports or mirrors the same mapping in the world package.
The performance setup can be reused across events.

## Package Layout

Recommended Unity package name:

```text
com.emnyeca.nyemidi
```

Recommended repository name:

```text
nyemidi
```

Recommended display name:

```text
NyeMIDI
```

Recommended package structure:

```text
Packages/
  com.emnyeca.nyemidi/
    package.json
    README.md
    Runtime/
      Midi/
      Mapping/
      Targets/
      Sync/
    Editor/
      MappingImporter/
      Inspectors/
    Prefabs/
      Receiver/
      Targets/
      Examples/
    Samples~/
      Basic MIDI Stage/
      Digitone Style Mapping/
      Digitakt Trigger Mapping/
```

The package should keep runtime code, editor tools, prefabs, and samples separated so world creators can import only what they need.

## Presets and Mapping Files

NyeMIDI mappings should be portable.

A mapping preset describes how incoming hardware messages are translated and how world-side targets should interpret them.

A preset may include:

* Device name
* Input channel
* Output channel
* Source note / CC
* Target note / CC
* Control label
* Control type
* Encoder mode
* Value range
* Invert setting
* Smoothing amount
* Quantization setting
* World-facing target name

Preset files should be readable, versioned, and suitable for sharing between performers and world creators.

## Non-Goals

NyeMIDI is not intended to replace a DAW, sequencer, synth editor, or full lighting console.

NyeMIDI does not aim to expose every possible MIDI message directly to VRChat worlds.

The core product is intentionally centered on clean, practical, performance-safe Note / CC interaction.

The following are outside the core responsibility:

* Full DAW control
* Audio processing
* Synth patch editing
* SysEx editing
* MIDI clock transport management
* Full OSC-to-Udon world runtime
* Heavy external bridge workflows
* General-purpose networking framework

Optional extensions may exist, but they should not complicate the core musician-facing and world-creator-facing workflow.

## Design Principles

### Musician-first control

A performer should be able to route and shape signals through a lightweight GUI without editing code.

### World-creator-friendly package

A world creator should be able to import NyeMIDI, place prefabs, assign mappings, and connect targets without rebuilding their world architecture.

### Note / CC as the stable boundary

NyeMIDI uses Note and Control Change as the stable communication layer between performance hardware and VRChat worlds.

### Configuration over hardcoding

Hardware behaviour varies. Encoder modes, CC ranges, channels, smoothing, and target behaviour should be configurable.

### Local immediacy, network restraint

Performance feedback should feel immediate locally. Shared state should be synchronized only when needed and in reduced form.

### Small core, extensible edges

The core should remain compact and reliable. Device-specific profiles, visual systems, and advanced bridges should be optional extensions.

## License

License to be decided.

Suggested options:

* MIT License for broad reuse.
* Custom source-available license if redistribution, commercial use, or packaged resale should be controlled.

## Related Project

NyeMIDI is part of **Emnyeca's Utility Build Series**, a collection of tools and systems for VR music performance, machine-live workflows, and practical creative environments.
