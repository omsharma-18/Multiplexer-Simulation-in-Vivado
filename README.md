# SIMULATION AND IMPLEMENTATION OF MULTIPLEXER

## AIM
To design and simulate a 4:1 Multiplexer (MUX) using Verilog HDL in four different modeling styles—Gate-Level, Data Flow, Behavioral, and Structural—and to verify its functionality through a testbench using the Vivado 2023.1 simulation environment. The experiment aims to understand how different abstraction levels in Verilog can be used to describe the same digital logic circuit and analyze their performance.

## APPARATUS REQUIRED
- **Vivado 2023.1**

## Procedure
### Launch Vivado
- Open Vivado 2023.1 from the Start menu or desktop icon.

### Create a New Project
1. Click **"Create Project"**.
2. Enter the project name (e.g., `Mux4_to_1`) and choose a location. Click **Next**.
3. Select **RTL Project** and click **Next**.
4. Add Verilog files (e.g., `mux4_to_1_gate.v`, `mux4_to_1_dataflow.v`) and enable **"Copy sources into project"**. Click **Next**.
5. Skip the Constraints step and proceed.
6. Select a default FPGA part (e.g., `xc7a35ticsg324-1L`). Click **Next**, then **Finish**.

### Add Source Files (if needed)
- In the Sources window, right-click on **"Design Sources"** → **Add Sources**.
- Add Verilog files and the testbench (`mux4_to_1_tb.v`).

### Check Syntax
- In the Flow Navigator, under **Synthesis**, click **"Run Synthesis"**.
- Correct any errors or warnings, then re-run the synthesis.

### Simulate the Design
- In the Flow Navigator, under **Simulation**, click **"Run Behavioral Simulation"**.

### Analyze Simulation Results
- View waveform signals (e.g., `S1`, `S0`, `A`, `B`, `C`, `D`, etc.).
- Use the waveform controls to zoom, scroll, and analyze results.

### Adjust Simulation Time
- Click **"Simulation"** → **"Simulation Settings"** to modify the run time (e.g., 1000ns).

### Generate Report
- Right-click the simulation window → **"Export Simulation Results"**.

### Save and Document
- Click **File** → **Save Project**.
- Capture screenshots of the waveform window for your lab report.

### Close Simulation
- Go to **"Simulation"** → **"Close Simulation"** when finished.

---

## Logic Diagram
![image](https://github.com/user-attachments/assets/d4ab4bc3-12b0-44dc-8edb-9d586d8ba856)

---

## Truth Table
![image](https://github.com/user-attachments/assets/c850506c-3f6e-4d6b-8574-939a914b2a5f)

---

## Verilog Code

### 4:1 MUX Gate-Level Implementation

![Image](https://github.com/user-attachments/assets/e1e3d5cb-2e76-41bc-84a6-84da7e8356fb)


```verilog

module mux4_to_1_behavioral (s0,s1,a,b,c,d,Y);
input s0,s1,a,b,c,d;
output Y;
wire w1,w2,w3,w4,w5,w6;
not g1(w1,s1);
not g2(w2,s0);
and g3(w3,w1,w2,a);
and g4(w4,w1,s0,b);
and g3(w5,s1,w2,c);
and g3(w6,s1,s0,d);
or g7(Y,w3,w4,w5,w6);
endmodule
```

### 4:1 MUX Data Flow Implementation

![Image](https://github.com/user-attachments/assets/ec22a85a-e61b-49da-9e11-e297c7d72fe4)


```verilog
module multiplexer41(s0,s1,a,b,c,d,Y);
input s0,s1,a,b,c,d;
output Y;
wire w1,w2,w3,w4;
assign w1=~s1&~s0&a;
assign w2=~s1&s0&b;
assign w3=s1&~s0&c;
assign w4=s1&s0&d;
assign Y=w1|w2|w3|w4;
endmodule
```

### 4:1 MUX Behavioral Implementation
```verilog
module mux4_to_1_behavioral (i,s,out);
input [4:1]i;
input [1:0]s;
output out;
always @(i,s,out)
begin
if(s[1]==0 & s[0]==0)
    out=i[1];
else if(s[1]==0 & s[0]==1)
    out=i[2];
else if(s[1]==1 & s[0]==0)
    out=i[3];
else
    out=i[4];
end
endmodule
```
### 4:1 MUX Structural Implementation

![image](https://github.com/user-attachments/assets/eea81c2c-7dfa-43aa-9cea-1ab4ed54db6c)


```verilog
module mux21(s,a,b,y);
input s,a,b;
output y;
assign y=(~s&a)|(s&b);
endmodule

module mux41(I,s,y);
input [4:1]I;
input [1:0]s;
output y;
wire w1,w2;
mux21 m1(s[1],I[1],I[3],w1);
mux21 m2(s[1],I[2],I[4],w2);
mux21 m3(s[0],w1,w2,y);
endmodule
```
### Testbench Implementation
```verilog
`timescale 1ns / 1ps

module mux4_to_1_tb;
    reg A, B, C, D, S0, S1;
    wire Y_gate, Y_dataflow, Y_behavioral, Y_structural;

    mux4_to_1_gate uut_gate (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_gate));
    mux4_to_1_dataflow uut_dataflow (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_dataflow));
    mux4_to_1_behavioral uut_behavioral (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_behavioral));
    mux4_to_1_structural uut_structural (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_structural));

    initial begin
        A = 0; B = 0; C = 0; D = 0; S0 = 0; S1 = 0;

        #10 {S1, S0, A, B, C, D} = 6'b00_0001;
        #10 {S1, S0, A, B, C, D} = 6'b01_0010;
        #10 {S1, S0, A, B, C, D} = 6'b10_0100;
        #10 {S1, S0, A, B, C, D} = 6'b11_1000;
        #10 $stop;
    end

    initial begin
        $monitor("Time=%0t | S1=%b S0=%b | Y_gate=%b | Y_dataflow=%b | Y_behavioral=%b | Y_structural=%b",
                 $time, S1, S0, Y_gate, Y_dataflow, Y_behavioral, Y_structural);
    end
endmodule
```
### SAMPLE OUTPUT
```verilog
Time=0 | S1=0 S0=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=10 | S1=0 S0=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=20 | S1=1 S0=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
```

**CONCLUSION**

In this experiment, a 4:1 Multiplexer was successfully designed and simulated using Verilog HDL across four different modeling styles: Gate-Level, Data Flow, Behavioral, and Structural.The simulation results verified the correct functionality of the MUX, with all implementations producing identical outputs for the given input conditions.

