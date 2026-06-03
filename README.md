# CAPL Automated ECU Flashing and Diagnostics Test Suite

> **Note:** This repository is for **learning purposes only**. It is an active, working repository, and more test cases will be added in the future as the project evolves.

## Overview
This repository contains a CAPL (Communication Access Programming Language) test module designed for automated ECU validation using **Vector CANoe** and **Vector vFlash**. The test suite automates routine firmware flashing, conducts robustness and negative testing using corrupted software payloads, and verifies UDS (Unified Diagnostic Services) session transitions and read services.

## Features & Test Coverage
This test suite executes the following validation scenarios sequentially via the `MainTest()` execution block:

* **Standard Flashing (`Single_flash_test`)**: Automates a standard ECU firmware update using a designated `.vflashpack` with a defined timeout threshold (300s).
* **Flashing Robustness (`multi_flash_test`)**: Performs stress testing by flashing the ECU multiple consecutive times (e.g., 10 iterations) to ensure memory and bootloader stability.
* **Negative Flashing / Security (`flash_corrupt_signature_test` & `flash_corrupt_checksum_test`)**: Validates the ECU's security and integrity checks by attempting to flash corrupted software payloads (invalid signatures and invalid checksums) and ensuring the bootloader rejects them.
* **Diagnostic Read Services (`read_diagnostic_service_test`)**: Validates UDS read data by identifier (Service 0x22) for standard parameters such as ECU Identification and Serial Number.
* **State Machine & Session Transitions**: 
  * `appl_to_reprogramming_session`: Validates the UDS state machine by transitioning from Default (0x01) -> Extended (0x03) -> Programming (0x02) sessions.
  * `reprogramming_to_appl_session`: Ensures the ECU can gracefully exit the bootloader and return to the application state safely.

## Repository Structure

The project relies on a modular architecture separating test execution from underlying utility functions:

* **`Main_Test.can`** (or similar): The primary test module containing the test cases and the `MainTest()` execution sequence.
* **`VFlash_Utilities.cin`**: Includes reusable functions for interacting with the vFlash API (e.g., `singleTimeFlash`, `MultiTimeFlash`, `Flash_corrupted_SW`).
* **`Diag_Lib.cin`**: Contains UDS diagnostic wrappers and session control abstractions (e.g., `DiagRequest_via_object`, `extended_session`).
* **`Global_Variables.cin`**: Stores environmental variables, timer definitions, and configuration states used across the module.

## Prerequisites
To run this automation suite, you will need:
1. **Vector CANoe**: With the Test Module.
2. **Vector vFlash**: Configured and integrated with your CANoe environment.
3. **vFlashpacks**: Valid `.vflashpack` files (you must provide your own target `.vflashpack` and explicitly corrupted versions for the negative tests).
4. **Target Hardware**: An actual ECU or a simulated node configured to respond to the defined UDS requests.

## Setup and Usage
1. Clone this repository to your local machine.
2. Open your CANoe configuration (`.cfg`).
3. Add the main `.can` test module to a **Test Environment** / **Test Setup** window in CANoe.
4. Ensure the `.cin` include files are in the same directory as the main `.can` file, or update the `#include` paths accordingly.
5. Update the dummy `.vflashpack` file names in the code (e.g., `"temp_flashpack_name.vflashpack"`) to match the actual files provided by your software team.
6. Compile the CAPL script to ensure there are no syntax errors.
7. Start the CANoe measurement and execute the test module.

## Best Practices Noted
* **Non-blocking Delays:** The suite strictly uses `testWaitForTimeout()` for internal state transitions rather than blocking `sysSleep()` functions, ensuring CANoe's measurement thread remains responsive. 
* **Modular Design:** Diagnostic objects and vFlash interactions are abstracted, making it easy to swap out `vflashpack` targets without rewriting test logic.

## License
This project is licensed under the MIT License.
