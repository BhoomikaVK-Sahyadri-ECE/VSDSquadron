# VSDSquadron FPGA Mini – RGB LED Blinking Project

This project demonstrates how to blink **Red**, **Green**, and **Blue LEDs** on the VSDSquadron FPGA Mini board using an internal oscillator and Verilog logic.

---

## Objective

- Use the internal oscillator of the FPGA to generate a signal.
- Drive RGB LED colors (Red, Green, Blue) based on counter logic.
- Observe color changes as a result of toggling LED channels using the internal clock.

---

## How It Works

### Counter Logic

- A 28-bit counter `frequency_counter_i` counts every clock pulse from the internal oscillator.
- This counter is used to generate patterns to control LED blinking.

### Internal Oscillator

- We use the `SB_HFOSC` primitive (high-frequency internal oscillator).
- Frequency is divided using `CLKHF_DIV = "0b10"` → typically gives **12 MHz**.
- Always turned ON using `CLKHFPU = 1`, `CLKHFEN = 1`.

### RGB LED Control

- The `SB_RGBA_DRV` primitive is used to control onboard Red, Green, and Blue LEDs.
- Each channel (R, G, B) is turned ON/OFF using bits from the counter to create visible blinking.

---

## Test Wire Output

- `testwire` is connected to bit 5 of the counter.
- It toggles periodically and is helpful for debugging or probing on an oscilloscope.

---

## Pin Mapping – `VSDSquadronFM.pcf`

| Signal     | FPGA Pin | Purpose                                 |
|------------|----------|------------------------------------------|
| `led_red`  | 39       | Connected to Red LED                    |
| `led_blue` | 40       | Connected to Blue LED                   |
| `led_green`| 41       | Connected to Green LED                  |
| `hw_clk`   | 20       | Reserved for external clock (unused)    |
| `testwire` | 17       | Output for debugging                    |

The video is shown below

https://github.com/BhoomikaVK-Sahyadri-ECE/VSDSquadron/blob/3bbcef859c46e74dd6e1c0b71b8a9d35e2d9b656/Task%201/Task1_video.mp4

# Integrating with the VSDSquadron FPGA Mini Board:
cd
cd VSDSquadron_FM
cd RGB

lsusb

make clean

make build

sudo make flash
## Final code:

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
    
    .RGB0PWM (frequency_counter_i[24]&frequency_counter_i[23] ),
    
    .RGB1PWM (frequency_counter_i[24]&~frequency_counter_i[23]),
    
    .RGB2PWM (~frequency_counter_i[24]&frequency_counter_i[23]),
    
    .CURREN  (1'b1                                            ),
    
    .RGB0    (led_red                                       ), //Actual Hardware connection
    
    .RGB1    (led_green                                       ),
    
    .RGB2    (led_blue                                        )
  
  );
  
  defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";
  
  defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";
  
  defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";

endmodule
```pcf
set_io led_red   39
set_io led_blue  40
set_io led_green 41
set_io hw_clk    20
set_io testwire  17
