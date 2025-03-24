# VSDSquadronFMresearch

# Task 1- VSDSquadron FPGA Mini - Verilog Integration Task

## Objective
Understand and document the provided Verilog code, create the necessary PCF file, and integrate the design with the VSDSquadron FPGA Mini board using the provided datasheet. Additionally, install the required tools as explained in the datasheet.

---

## Step 1: Understanding the Verilog Code

### Access the Verilog Code
The Verilog code is available at: [top.v](https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/top.v)
```bash
//----------------------------------------------------------------------------
//                                                                          --
//                         Module Declaration                               --
//                                                                          --
//----------------------------------------------------------------------------
module top (
  // outputs
  output wire led_red  , // Red
  output wire led_blue , // Blue
  output wire led_green , // Green
  input wire hw_clk,  // Hardware Oscillator, not the internal oscillator
  output wire testwire
);

  wire        int_osc            ;
  reg  [27:0] frequency_counter_i;

  assign testwire = frequency_counter_i[5];
 
  always @(posedge int_osc) begin
    frequency_counter_i <= frequency_counter_i + 1'b1;
  end


//----------------------------------------------------------------------------
//                                                                          --
//                       Counter                                            --
//                                                                          --
//----------------------------------------------------------------------------

//----------------------------------------------------------------------------
//                                                                          --
//                       Internal Oscillator                                --
//                                                                          --
//----------------------------------------------------------------------------
  SB_HFOSC #(.CLKHF_DIV ("0b10")) u_SB_HFOSC ( .CLKHFPU(1'b1), .CLKHFEN(1'b1), .CLKHF(int_osc));


//----------------------------------------------------------------------------
//                                                                          --
//                       Instantiate RGB primitive                          --
//                                                                          --
//----------------------------------------------------------------------------
  SB_RGBA_DRV RGB_DRIVER (
    .RGBLEDEN(1'b1                                            ),
    .RGB0PWM (1'b0), // red
    .RGB1PWM (1'b0), // green
    .RGB2PWM (1'b1), // blue
    .CURREN  (1'b1                                            ),
    .RGB0    (led_red                                       ), //Actual Hardware connection
    .RGB1    (led_green                                       ),
    .RGB2    (led_blue                                        )
  );
  defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";
  defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";
  defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";

endmodule
```

### Module Overview
This Verilog module implements a simple design that utilizes an internal high-frequency oscillator to drive a frequency counter and control an RGB LED. The module includes a test signal output for debugging purposes.

### Inputs and Outputs
- **Inputs:**
  - `hw_clk`: Hardware oscillator clock input (not used in the module but available for external clocking if needed).
- **Outputs:**
  - `led_red`, `led_blue`, `led_green`: Control signals for an RGB LED.
  - `testwire`: Debugging signal derived from the internal frequency counter.

### Internal Components
- **Internal Oscillator (`SB_HFOSC`)**: Provides a clock signal to drive the counter.
- **Frequency Counter**: A 28-bit counter that increments on each clock cycle, with bit 5 (`frequency_counter_i[5]`) used as a test output.
- **RGB LED Driver (`SB_RGBA_DRV`)**: Controls an RGB LED, with only the blue LED activated (`RGB2PWM = 1'b1`). The current settings are predefined.

---
**1. Internal Oscilliator (*SB_HFOSC*)**
- Purpose: This generates a stable internal clock signal
- Configuration: Uses CLKHF_DIV = "0b10" (binary 2) for clock division
- Control Signals:
    1. *CLKHFPU = 1'b1* : Enables power-up
    2. *CLKHFEN = 1'b1* : Enables oscillator
    3. *CLKHF* : Output connected to internal *int_osc* signal

**2. Frequency Counter Logic**
- Implementation: 28-bit register (*frequency_counter_i*)
- Operation: Increments on every positive edge of *int_osc*
- Test functionality: Bit 5 is routed to *testwire* for monitoring
- Purpose: Provides a way to verify oscillator operation and timing

**3. RGB LED Driver (*SB_RGBA_DRV*)**

- Configuration:
    1. *RGBLEDEN = 1'b1* : Enables LED operation
    2. *RGB0PWM = 1'b0* : Red LED minimum brightness
    3. *RGB1PWM = 1'b0* : Green LED minimum brightness
    4. *RGB2PWM = 1'b1* : Blue LED maximum brightness
    5. *CURREN = 1'b1* : Enables current control
- Current settings: All LEDs set to "0b000001" (minimum current)
 - Output connections:
    1. *RGB0* → *led_red*
    2. *RGB1* → *led_green*
    3. *RGB2* → *led_blue*

## Step 2: Creating the PCF File

### Access the PCF File
The PCF file can be found at: [VSDSquadronFM.pcf](https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/VSDSquadronFM.pcf)

### Pin Assignments
| Signal      | Pin |
|------------|----|
| `led_red`  | 39 |
| `led_blue` | 40 |
| `led_green`| 41 |
| `hw_clk`   | 20 |
| `testwire` | 17 |

Cross-reference these pin assignments with the VSDSquadron FPGA Mini board datasheet to verify correctness.
[Datasheet](https://www.vlsisystemdesign.com/wp-content/uploads/2025/01/VSDSquadronFMDatasheet.pdf)
### Significance of Connections
- **RGB LEDs**: Connected to FPGA output pins to enable color control.
- **`hw_clk`**: External clock input for flexibility in timing.
- **`testwire`**: Allows observation of internal signal behavior for debugging.

---

## Step 3: Integrating with the VSDSquadron FPGA Mini Board

### Reviewing the Datasheet
Refer to the VSDSquadron FPGA Mini board datasheet to understand:
- Features and pinouts
- Board connections and signal routing

### Connecting the Board
- Connect the FPGA board to a computer using a USB-C cable.
- Ensure the FTDI connection is properly recognized.

### Building and Flashing the Verilog Code
The Makefile is available at: [Makefile](https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/Makefile)

#### Steps to Program the FPGA:
1. Run `make clean` to clear previous builds.
2. Run `make build` to compile the design.
3. Run `sudo make flash` to program the FPGA.
4. Observe the RGB LED behavior to confirm successful programming.


https://github.com/user-attachments/assets/8dea0a45-c348-4dd7-bc76-1f52246cf4b3


---


## License


## Author
[Aaryan Sharma](https://github.com/AaryanSharma03)
