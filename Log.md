# Project Log - pinetime_plus

## Overview
This log documents the development process for modifying InfiniTime firmware to support 30Hz accelerometer sampling and data collection for Python analysis packages (pyActigraphy and agcounts).

## Session 1 - Initial Setup and Investigation

### Goals
- Fork and clone InfiniTime repository
- Investigate accelerometer sampling rates
- Set up build environment
- Test compilation

### Accomplishments

#### 1. Repository Setup ✅
- **Forked InfiniTime repository**: Created fork at `kelvinlim/InfiniTime`
- **Cloned locally**: Successfully cloned to `/home/kelvin/Projects/pinetime_plus`
- **Added upstream remote**: Set up tracking of original InfiniTime repository
- **Initialized submodules**: Downloaded required dependencies (arduinoFFT, littlefs, lvgl)

#### 2. Accelerometer Investigation ✅
- **Analyzed BMA421 driver**: Found current sampling rate configuration in `src/drivers/Bma421.cpp`
- **Current rate**: 100 Hz (`BMA4_OUTPUT_DATA_RATE_100HZ`)
- **Available rates**: Investigated all supported sampling rates:
  - 0.78 Hz, 1.56 Hz, 3.12 Hz, 6.25 Hz, 12.5 Hz
  - **25 Hz** (`BMA4_OUTPUT_DATA_RATE_25HZ`) - **Closest to 30Hz target**
  - 50 Hz, 100 Hz (current), 200 Hz, 400 Hz, 800 Hz, 1600 Hz
- **Recommendation**: Use 25 Hz as closest available rate to 30 Hz target

#### 3. Build Environment Setup ✅
- **Docker/Podman**: Used InfiniTime's official build image `infinitime/infinitime-build`
- **SELinux fix**: Resolved permission issues with `:Z` flag for volume mounts
- **Successful compilation**: Built `pinetime-app` target successfully
- **Generated files**:
  - `build/src/pinetime-app-1.15.0.bin` - Binary firmware
  - `build/src/pinetime-app-1.15.0.hex` - Intel HEX format
  - `build/src/pinetime-app-1.15.0.out` - ELF object file
  - `build/src/pinetime-app-1.15.0.map` - Memory map

#### 4. Project Documentation ✅
- **Created ProjectPlan.md**: Documented project goals and phases
- **Added to git**: Committed with proper InfiniTime conventions
- **Pushed to GitHub**: Available in fork repository

### Key Files Identified
- **Accelerometer driver**: `src/drivers/Bma421.cpp` (line 87 - sampling rate config)
- **Motion controller**: `src/components/motion/MotionController.cpp`
- **BLE service**: `src/components/ble/MotionService.cpp`
- **Build system**: Uses CMake with Docker container

### Technical Findings
- **Architecture**: FreeRTOS-based firmware for nRF52 microcontroller
- **UI**: LittleVGL/LVGL for display interface
- **BLE**: NimBLE stack for communication
- **Current polling**: MotionController polls at 10Hz (not sensor sampling rate)
- **Data flow**: BMA421 → MotionController → MotionService → BLE

### Next Steps
1. **Implement 25 Hz sampling rate** (simple one-line change)
2. **Design data storage mechanism** for accelerometer data
3. **Explore BLE service** for data transfer capabilities
4. **Create Python desktop application** for data collection

### Commands Used
```bash
# Repository setup
git clone https://github.com/kelvinlim/InfiniTime.git .
git remote add upstream https://github.com/InfiniTimeOrg/InfiniTime.git
git submodule update --init

# Build environment
podman pull docker.io/infinitime/infinitime-build:latest
podman run --rm -v ${PWD}:/sources:Z infinitime/infinitime-build /opt/build.sh pinetime-app

# Git operations
git add ProjectPlan.md
git commit -m "docs: Add project plan for accelerometer modifications"
git push origin main
```

### Issues Resolved
- **Permission denied**: Fixed with SELinux `:Z` flag for Podman volume mounts
- **Missing submodules**: Resolved with `git submodule update --init`
- **Build environment**: Successfully used Docker container approach

### Current Status
✅ **Build environment working**  
✅ **Accelerometer investigation complete**  
✅ **Project plan documented**  
🔄 **Ready for implementation phase**

---
*Log created: $(date)*
*Next session: Implement 25Hz sampling rate change*
