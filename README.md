# 4_1_Multiplexer
# EXP NO: 1.A SIMULATION AND IMPLEMENTATION OF 4:1 MULTIPLEXER
# AIM
To design and simulate a 4:1 Multiplexer (MUX) using Verilog HDL in four different modeling styles—Gate-Level, Data Flow, Behavioral, and Structural—and to verify its functionality through a testbench using the Vivado 2023.1 simulation environment. The experiment aims to understand how different abstraction levels in Verilog can be used to describe the same digital logic circuit and analyze their performance.

# APPARATUS REQUIRED
Vivado 2023.1

# Procedure
1.Open Vivado 2023.1.
2.Create a New RTL Project and give a name (e.g., Mux4_to_1).
3.Add/create your Verilog files and testbench.
4.Select an FPGA part (e.g., xc7a35ticsg324-1L).
5.Run Synthesis to check for errors.
6.Run Simulation → Run Behavioral Simulation.
7.Observe the waveforms of inputs and outputs.
8.Adjust simulation time if needed (e.g., 1000ns).
9.Save the project and take screenshots of results.
10.Close simulation.

# Logic Diagram

<img width="614" height="424" alt="Screenshot 2026-02-11 195225" src="https://github.com/user-attachments/assets/03cabe3f-914c-4163-bea7-2ba5257ed5a7" />


# Truthtable 

<img width="496" height="376" alt="Screenshot 2026-02-11 194234" src="https://github.com/user-attachments/assets/3530b20d-cc75-480c-bc14-d10b21a36376" />


# Verilog Code
# 4:1 MUX Gate-Level Implementation
```
module mux4_to_1_gate (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output wire Y
);
    wire not_S0, not_S1;
    wire A_and, B_and, C_and, D_and;

    not (not_S0, S0);
    not (not_S1, S1);

    and (A_and, A, not_S1, not_S0);
    and (B_and, B, not_S1, S0);
    and (C_and, C, S1, not_S0);
    and (D_and, D, S1, S0);

    or (Y, A_and, B_and, C_and, D_and);
endmodule

```
# 4:1 MUX Gate-Level Implementation- Testbench
```
`timescale 1ns/1ps

module mux4_to_1_gate_tb;

    // Inputs
    reg A, B, C, D;
    reg S0, S1;

    // Output
    wire Y;

    // Instantiate the DUT (Device Under Test)
    mux4_to_1_gate uut (
        .A(A),
        .B(B),
        .C(C),
        .D(D),
        .S0(S0),
        .S1(S1),
        .Y(Y)
    );

    initial begin
        // Monitor values
        $monitor("Time=%0t | S1=%b S0=%b | A=%b B=%b C=%b D=%b | Y=%b",
                  $time, S1, S0, A, B, C, D, Y);

        // Initialize inputs
        A=0; B=1; C=0; D=1;

        // Test all select line combinations
        S1=0; S0=0; #10; // Expect Y = A
        S1=0; S0=1; #10; // Expect Y = B
        S1=1; S0=0; #10; // Expect Y = C
        S1=1; S0=1; #10; // Expect Y = D

        // Change data inputs
        A=1; B=0; C=1; D=0;

        S1=0; S0=0; #10;
        S1=0; S0=1; #10;
        S1=1; S0=0; #10;
        S1=1; S0=1; #10;

        // Finish simulation
        $finish;
    end

endmodule
```
# Simulated Output Gate Level Modelling
<img width="1897" height="837" alt="image" src="https://github.com/user-attachments/assets/82824a60-d515-457c-93cb-e4dc0a6761cb" />



# 4:1 MUX Data flow Modelling
```
module mux4_to_1_dataflow (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output wire Y
);
    assign Y = (~S1 & ~S0 & A) |
               (~S1 & S0 & B) |
               (S1 & ~S0 & C) |
               (S1 & S0 & D);
endmodule
```
# 4:1 MUX Data flow Modelling- Testbench
```
`timescale 1ns/1ps

module mux4_to_1_dataflow_tb;

    // Inputs
    reg A, B, C, D;
    reg S0, S1;

    // Output
    wire Y;

    // Instantiate DUT
    mux4_to_1_dataflow uut (
        .A(A),
        .B(B),
        .C(C),
        .D(D),
        .S0(S0),
        .S1(S1),
        .Y(Y)
    );

    initial begin
        // Display header
        $display("Time | S1 S0 | A B C D | Y");
        $monitor("%0t   | %b  %b  | %b %b %b %b | %b",
                  $time, S1, S0, A, B, C, D, Y);

        // Initial values
        A=0; B=1; C=0; D=1;

        // Test select lines
        S1=0; S0=0; #10; // Y = A
        S1=0; S0=1; #10; // Y = B
        S1=1; S0=0; #10; // Y = C
        S1=1; S0=1; #10; // Y = D

        // Change inputs
        A=1; B=0; C=1; D=0;

        S1=0; S0=0; #10;
        S1=0; S0=1; #10;
        S1=1; S0=0; #10;
        S1=1; S0=1; #10;

        // Random test (optional but good practice)
        A=1; B=1; C=0; D=0;
        S1=0; S0=1; #10;
        S1=1; S0=0; #10;

        // End simulation
        $finish;
    end

endmodule
 
```
# Simulated Output Dataflow Modelling
<img width="1920" height="879" alt="image" src="https://github.com/user-attachments/assets/39ac3a82-2030-4b9e-b63d-82f1b6cdf59c" />




# 4:1 MUX Behavioral Implementation
```
module mux4_to_1_behavioral (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output reg Y
);
    always @(*) begin
        case ({S1, S0})
            2'b00: Y = A;
            2'b01: Y = B;
            2'b10: Y = C;
            2'b11: Y = D;
            default: Y = 1'bx;
        endcase
    end
endmodule
```
# 4:1 MUX Behavioral Modelling- Testbench
```
`timescale 1ns/1ps

module mux4_to_1_behavioral_tb;

    // Inputs
    reg A, B, C, D;
    reg S0, S1;

    // Output
    wire Y;

    // Instantiate DUT
    mux4_to_1_behavioral uut (
        .A(A),
        .B(B),
        .C(C),
        .D(D),
        .S0(S0),
        .S1(S1),
        .Y(Y)
    );

    initial begin
        // Display format
        $display("Time | S1 S0 | A B C D | Y");
        $monitor("%0t   | %b  %b  | %b %b %b %b | %b",
                  $time, S1, S0, A, B, C, D, Y);

        // Initial values
        A=0; B=1; C=0; D=1;

        // Test all select combinations
        S1=0; S0=0; #10; // Y = A
        S1=0; S0=1; #10; // Y = B
        S1=1; S0=0; #10; // Y = C
        S1=1; S0=1; #10; // Y = D

        // Change inputs and test again
        A=1; B=0; C=1; D=0;

        S1=0; S0=0; #10;
        S1=0; S0=1; #10;
        S1=1; S0=0; #10;
        S1=1; S0=1; #10;

        // Extra random cases (good practice)
        A=1; B=1; C=0; D=0;
        S1=1; S0=0; #10;
        S1=0; S0=1; #10;

        // End simulation
        $finish;
    end

endmodule
```
# Simulated Output Behavioral Modelling
<img width="1920" height="855" alt="image" src="https://github.com/user-attachments/assets/7e443a97-4738-4f03-beac-afe880faa2d5" />



# 4:1 MUX Structural Implementation
```

module mux2_to_1 (
    input wire A,
    input wire B,
    input wire S,
    output wire Y
);
    assign Y = S ? B : A;
endmodule

module mux4_to_1_structural (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output wire Y
);
    wire mux_low, mux_high;

    mux2_to_1 mux0 (.A(A), .B(B), .S(S0), .Y(mux_low));
    mux2_to_1 mux1 (.A(C), .B(D), .S(S0), .Y(mux_high));

    mux2_to_1 mux_final (.A(mux_low), .B(mux_high), .S(S1), .Y(Y));
endmodule
```
# Testbench Implementation
```
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
# Simulated Output Structural Modelling
<img width="1920" height="891" alt="image" src="https://github.com/user-attachments/assets/63cf0ad1-ff67-44a1-8669-8296d1cd6481" />



# CONCLUSION
In this experiment, a 4:1 Multiplexer was successfully designed and simulated using Verilog HDL across four different modeling styles: Gate-Level, Data Flow, Behavioral, and Structural.The simulation results verified the correct functionality of the MUX, with all implementations producing identical outputs for the given input conditions.



endmodule
