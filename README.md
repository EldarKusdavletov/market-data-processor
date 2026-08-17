# Market Data Processor

A small C++20 project that simulates real-time market events and processes them through a lightweight in-memory pipeline.

The repository demonstrates:

- modeling market data events (`TRADE`, `QUOTE`)
- random event generation for several symbols
- event ingestion and summary statistics
- basic multithreaded processing validation with GoogleTest
- modern CMake build setup with strict compiler warnings

---

## Repository Overview

This project is organized as a static library (`market_data_lib`) plus:

- **application binary**: `market_data_processor`
- **test binary**: `market_data_tests`

### Directory Structure

```text
.
├── CMakeLists.txt
├── main.cpp
├── include/
│   ├── DataFeedSimulator.hpp
│   ├── EventType.hpp
│   ├── MarketDataProcessor.hpp
│   └── MarketEvent.hpp
├── src/
│   ├── DataFeedSimulator.cpp
│   ├── EventType.cpp
│   ├── MarketDataProcessor.cpp
│   └── MarketEvent.cpp
└── tests/
    └── test_market_data_processor.cpp
```

---

## Core Components

### 1) `EventType`
- Declares supported event categories through a centralized macro list.
- Provides conversions/comparisons between enum values and strings.
- Exposes `all_event_types` and `event_type_count` helpers.

### 2) `MarketEvent`
- Data model representing one event:
  - `symbol`
  - `price`
  - `volume`
  - `type`
- Includes equality/inequality operators for event comparisons.

### 3) `DataFeedSimulator`
- Simulates incoming events using random distributions:
  - prices in a configured range
  - volumes in a configured range
  - random symbol selection from an internal symbol list
  - random event type selection
- Supports runtime symbol management (`add_symbol`, `remove_symbol`).

### 4) `MarketDataProcessor`
- Receives events and stores them in memory.
- Routes handling by event type (`TRADE` / `QUOTE`).
- Tracks processed event count.
- Provides utility queries:
  - latest price by symbol
  - unique symbol extraction
  - average price by symbol
  - full event list access
- Prints a compact processing summary to stdout.

---

## Runtime Flow

`main.cpp` drives a simple end-to-end loop:

1. Create `MarketDataProcessor` and `DataFeedSimulator`.
2. Generate 20 simulated events with a short delay between events.
3. Process each event immediately.
4. Print a final summary (total processed + latest price per symbol).

---

## Build Instructions

### Prerequisites

- CMake 3.20+
- C++20 compiler (GCC/Clang/MSVC with C++20 support)
- pthread-compatible environment (for threaded code/tests on Unix-like systems)

### Configure and Build

```bash
mkdir -p build
cd build
cmake ..
cmake --build .
```

This produces:

- `build/bin/market_data_processor`
- `build/tests/market_data_tests` (when tests are enabled)

---

## Run the Application

From the `build` directory:

```bash
./bin/market_data_processor
```

Expected output includes per-event logs and a final summary section.

---

## Run Tests

Tests are enabled by default (`BUILD_TESTS=ON`).

```bash
mkdir -p build
cd build
cmake -DCMAKE_BUILD_TYPE=Debug ..
cmake --build .
ctest --output-on-failure
```

The test suite currently focuses on concurrent event processing behavior.

---

## Implementation Notes

- The build uses strict warning flags (`-Wall`, `-Wextra`, `-Wpedantic`, and additional safety-focused warnings).
- GoogleTest is discovered via `find_package(GTest)` and fetched automatically via `FetchContent` when unavailable.
- The processor stores all events in memory, which is suitable for demonstration and small workloads.

---

## Current Scope and Extension Ideas

### Current scope
- Simulated data feed only (no external market data source).
- In-memory storage only.
- Console output for observability.

### Possible extensions
- add timestamps and sequence numbers to events
- compute richer analytics (VWAP, rolling windows, per-type metrics)
- ingest events from files, sockets, or streaming middleware
- persist processed events to a database
- add benchmarks and broader correctness tests