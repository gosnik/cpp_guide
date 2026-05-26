# 5. Naming and File Conventions

The general consensus is: **be boring, consistent, searchable, and unsurprising**.

Naming conventions matter less than consistency, but a C++ codebase should have a clear standard for files, types, functions, variables, constants, tests, and modules.

## Core Naming Principle

Prefer names that describe intent, role, or domain meaning rather than implementation detail.

Names should be:

- clear
- consistent
- searchable
- pronounceable
- specific enough to avoid ambiguity
- boring rather than clever

Avoid abbreviations unless they are standard in the project, domain, or protocol.

Examples:

```cpp
DepthEstimator depth_estimator;
MissionCommandHandler command_handler;
PressureSample pressure_sample;
```

Prefer:

```cpp
DepthEstimator
MissionCommandHandler
pressure_sample
is_timeout_expired()
```

Avoid:

```cpp
DEst
CmdProc
p
check()
```

## File Naming

Use consistent file names that match the primary type, module, or responsibility in the file.

Recommended convention:

```text
DepthEstimator.hpp
DepthEstimator.cpp
MissionCommandHandler.hpp
MissionCommandHandler.cpp
CanTransport.hpp
CanTransport.cpp
```

Use `PascalCase` for filenames for consistency with class naming.

Avoid inconsistent mixed styles:

```text
BatteryMonitor.cpp
batteryMonitor.cpp
battery-monitor.cpp
batt_mon.cpp
```

## Header Extensions

Choose one header extension and use it consistently.

Common modern C++ choice:

```text
.hpp / .cpp
```

For C++-only projects, `.hpp` makes it clear the header is C++.

For mixed C/C++ embedded projects, `.h` is a logical choice for C header files.

Do not mix `.h`, `.hpp`, `.hh`, and `.hxx` without a clear reason.

## Type Names

Use `PascalCase` for types:

```cpp
class DepthEstimator;
struct PressureSample;
enum class MissionState;
using SampleBuffer = StaticVector<PressureSample, 32>;
```

This applies to:

- classes
- structs
- enums
- type aliases
- concepts

## Function Names

Use one function style consistently.

Recommended:

```cpp
estimate_depth()
parse_mission_file()
request_emergency_surface()
is_valid()
```

Lowercase `snake_case` is common in modern C++ projects and aligns well with the C++ standard library.

`camelCase` is also acceptable if already established by the project:

```cpp
estimateDepth()
parseMissionFile()
requestEmergencySurface()
isValid()
```

Do not mix styles casually.

## Variables

Use descriptive `snake_case` or the project’s chosen variable style:

```cpp
pressure_sample
retry_count
target_depth
mission_timeout
```

Short names are acceptable only for very small scopes or conventional meanings:

```cpp
for (std::size_t i = 0; i < samples.size(); ++i) {
    // ...
}
```

Avoid vague names:

```cpp
data
info
thing
manager
handler
processor
temp
```

unless the scope makes the meaning obvious.

## Member Variables

Use leading underscores for private member variables.

```cpp
class DepthEstimator {
public:
    explicit DepthEstimator(PressureSensor& pressure_sensor);

private:
    PressureSensor& _pressure_sensor;
    DepthCalibration _calibration;
};
```

Leading underscore gives an immediate indication of member scope.

Avoid mixing private-member conventions in the same project. For example, do not mix `_member` with `m_member`:

```cpp
class DepthEstimator {
private:
    PressureSensor& m_pressure_sensor;
    DepthCalibration m_calibration;
};
```


## Constants

Use readable names that explain meaning and units:

```cpp
constexpr auto max_retry_count = 3;
constexpr Milliseconds sensor_startup_delay{250};
constexpr std::size_t max_packet_size_bytes = 256;
```

Avoid magic numbers:

```cpp
if (retry_count > 3) {
    // ...
}

delay(250);
```

Prefer:

```cpp
if (retry_count > max_retry_count) {
    // ...
}

clock.sleep(sensor_startup_delay);
```

## Units in Names

For raw numeric values, include units in the name:

```cpp
timeout_ms
packet_size_bytes
pressure_kpa
depth_m
sample_rate_hz
```

Better still, use strong types where practical:

```cpp
Milliseconds timeout;
Bytes packet_size;
Meters depth;
Hertz sample_rate;
```

Use unit suffixes especially at API boundaries, hardware interfaces, protocols, configuration files, and embedded code.

## Namespaces

Use namespaces to express ownership and module boundaries.

Prefer:

```cpp
namespace mission {
class Parser;
}

namespace hardware::can {
class Transport;
}
```

Avoid dumping unrelated types into broad namespaces such as:

```cpp
namespace common
namespace utils
namespace helpers
```

unless those namespaces are tightly controlled and deliberately small.

## Test Naming

Test files should clearly map to the unit or workflow being tested and follow the same `PascalCase` file naming convention.

Prefix test files with `Test` so they are easy to identify and group together in tree views:

```text
TestDepthEstimator.cpp
TestMissionParser.cpp
TestCanTransport.cpp
TestEmergencySurfaceWorkflow.cpp
```

Test names should describe behavior:

```cpp
TEST(DepthEstimator, ConvertsPressureToDepthUsingCalibration)
TEST(MissionParser, RejectsCommandWithMissingTargetDepth)
TEST(EmergencySurfaceWorkflow, CommandsSurfaceWhenBatteryIsCritical)
```

Prefer behavior names over implementation names.

## Interface Naming

Avoid prefixing every interface with `I` unless it is already the project convention.

Prefer role-based names:

```cpp
class Clock;
class Storage;
class CanTransport;
class MissionCommandSink;
```

instead of:

```cpp
class IClock;
class IStorage;
class ICanTransport;
```

Either style is acceptable, but role-based names often read better in C++.

## Avoid Weak Generic Names

Be suspicious of names like:

```text
Manager
Handler
Processor
Controller
Helper
Utility
Common
Data
Info
Context
```

These are not forbidden, but they often hide unclear responsibility.

Prefer more specific names:

```cpp
MissionScheduler
CommandDispatcher
PressureDepthEstimator
CanFrameParser
PersistentConfigurationStore
```

A generic suffix is acceptable only when the full name makes the responsibility clear.

For example, `MissionCommandHandler` may be acceptable, but `Handler` or `CommandHandler` may be too vague depending on scope.

## File Organization

One file should usually contain one primary type or one tightly related group of functions/types.

Good:

```text
DepthEstimator.hpp
DepthEstimator.cpp
MissionParser.hpp
MissionParser.cpp
CanFrame.hpp
```

Acceptable for tightly related value types:

```text
MissionTypes.hpp
CanProtocolTypes.hpp
```

Avoid monolithic files like:

```text
Utils.cpp
Common.cpp
System.cpp
Everything.hpp
```

unless they are deliberately small and tightly scoped.

## Embedded C++ Naming Notes

For embedded C++, names should make hardware, units, and timing behavior obvious.

Prefer:

```cpp
battery_voltage_mv
pressure_sample_kpa
motor_current_ma
control_loop_period_ms
max_can_payload_bytes
```

Better still, use strong types:

```cpp
Millivolts battery_voltage;
Kilopascals pressure_sample;
Milliamps motor_current;
Milliseconds control_loop_period;
Bytes max_can_payload;
```

Hardware-facing names should describe the real hardware role, not just the bus or implementation detail.

Prefer:

```cpp
DepthPressureSensor
BuoyancyPumpController
CanTelemetryTransport
WatchdogService
```

Avoid:

```cpp
I2CDevice1
SpiThing
BoardObject
HwManager
```

Register names, protocol field names, and datasheet-defined symbols may follow the datasheet or protocol naming where that improves traceability.

## Generated Code

Generated code may follow the generator’s convention.

Do not manually rewrite generated names unless the generator supports a stable naming policy.

Keep generated code clearly separated from hand-written code.

## Public API Stability

Public names are part of the API.

Before renaming public types, functions, files, configuration keys, protocol fields, or serialized names, consider compatibility impact.

Renames are acceptable when they remove ambiguity or improve correctness, but they should be deliberate and migration-aware.

## Recommended Default Convention

Use this unless the existing project has a strong established convention:

```text
Files:              PascalCase.hpp / PascalCase.cpp
Types:              PascalCase
Functions:          snake_case()
Variables:          snake_case
Private members:    _leading_underscore
Constants:          snake_case constexpr names
Namespaces:         lowercase, domain/module based
Tests:              TestThingBeingTested.cpp
```

The most important rule is consistency:

**Choose a convention, document it, apply it automatically where possible, and avoid mixing styles without a deliberate reason.**
