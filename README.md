# Sequence-Detector
Aim
To design and simulate a sequence detector using both Moore and Mealy state machine models in Verilog HDL, and verify their functionality through a testbench using the Vivado 2023.1 simulation environment. The objective is to detect a specific sequence of bits (e.g., 1011) and compare the Moore and Mealy designs.

Apparatus Required
Vivado 2023.1 or equivalent Verilog simulation tool.
Computer system with a suitable operating system.
Procedure
Launch Vivado 2023.1:

Open Vivado and create a new project.
Write the Verilog Code for Sequence Detector (Moore and Mealy FSM):

Design two Verilog modules: one for a Moore FSM and another for a Mealy FSM to detect a sequence such as 1011.
Create the Testbench:

Write a testbench to apply input sequences and verify the output of both FSM designs.
Add the Verilog Files:

Add the Verilog code for both FSMs and the testbench to the Vivado project.
Run Simulation:

Run the simulation and observe the output to check if the sequence is detected correctly.
Observe the Waveforms:

Analyze the waveform to ensure both the Moore and Mealy machines detect the sequence as expected.
Save and Document Results:

Capture the waveforms and include the results in the final report.

Verilog Code for Sequence Detector Using Moore FSM

module MOORE( input clk, input reset, input seq_in, output reg detected );

localparam S0 = 3'b000, S1 = 3'b001, S2 = 3'b010, S3 = 3'b011, S4 = 3'b100; reg [2:0] current_state, next_state;

always @(posedge clk or posedge reset)
begin 
if (reset) 
current_state <= S0; 
else 
current_state <= next_state; 
end

always @(*) 
begin 
case (current_state) 
S0: 
begin 
if (seq_in) 
next_state = S1; 
else next_state = S0; 
end 
S1: begin 
if (seq_in) 
next_state = S1; 
else next_state = S2; 
end 
S2: begin 
if (seq_in) 
next_state = S3; 
else 
next_state = S0; 
end 
S3: begin 
if (seq_in) 
next_state = S4; 
else next_state = S2; 
end S4: 
begin if (seq_in) 
next_state = S1; 
else next_state = S0; 
end default: next_state = S0; 
endcase 
end
always @(posedge clk or posedge reset) 
begin 
if (reset) 
detected <= 0; 
else if (current_state == S4) 
detected <= 1; 
else 
detected <= 0; 
end
endmodule
  OUTPUT :![MOORE-SD](https://github.com/user-attachments/assets/486e85d7-29a3-427c-b826-92f167095787)


Verilog Code for Sequence Detector Using Mealy FSM

module MEALY(input clk, input reset, input seq_in, output reg detected );

localparam 
S0 = 3'b000, 
S1 = 3'b001, 
S2 = 3'b010, 
S3 = 3'b011, 
S4 = 3'b100; 
reg [2:0] current_state, next_state;

always @(posedge clk or posedge reset) 
begin if (reset) 
current_state <= S0; 
else 
current_state <= next_state; 
end
always @(*) 
begin 
next_state = current_state; 
detected = 0; 
case (current_state) 
S0: 
begin 
if (seq_in) 
next_state = S1; 
else 
next_state = S0; 
end 
S1: begin 
if (seq_in) 
next_state = S1; 
else 
next_state = S2; 
end 
S2: begin 
if (seq_in) 
next_state = S3; 
else next_state = S0; 
end 
S3: begin 
if (seq_in) 
begin 
next_state = S1; 
detected = 1; 
end 
else 
next_state = S2; 
end
    default: 
        next_state = S0;
endcase
end endmodule
 OUTPUT:![MEALY-SD](https://github.com/user-attachments/assets/8c5e4ed5-a947-4813-8265-296e20196d62)


Testbench for Sequence Detector 
MEALY SEQUENCE DETECTOR:

module MEALY_TB; 
    reg clk; 
    reg reset; 
    reg seq_in; 
    wire detected; 

    MEALY dut ( 
        .clk(clk), 
        .reset(reset), 
        .seq_in(seq_in), 
        .detected(detected) 
    ); 

    always #5 clk = ~clk; 

    initial begin 
        clk = 0; 
        reset = 1; 
        seq_in = 0; 
        #10 reset = 0; 

        #10 seq_in = 1; 
        #10 seq_in = 0; 
        #10 seq_in = 1; 
        #10 $display("Sequence 101 detected: %b", detected); 

        #10 seq_in = 0; 
        #10 seq_in = 1; 
        #10 seq_in = 0; 
        #10 $display("No sequence detected: %b", detected); 

        #10 seq_in = 1; 
        #10 seq_in = 0; 
        #10 seq_in = 0; 
        #10 $display("Sequence partially detected: %b", detected); 

        #10 $finish; 
    end 
endmodule

OUTPUT:![MEALY-SD-TB](https://github.com/user-attachments/assets/501086b5-3828-493a-a383-18d7b76f560e)


MOORE SEQUENCE DETECTOR:

module MOORE_TB; 
    reg clk; 
    reg reset; 
    reg seq_in; 
    wire detected; 

    MOORE uut ( 
        .clk(clk), 
        .reset(reset), 
        .seq_in(seq_in), 
        .detected(detected) 
    ); 

    initial begin 
        clk = 0; 
        forever #5 clk = ~clk; 
    end 

    initial begin 
        reset = 1; 
        seq_in = 0; 
        #10; 
        reset = 0; 
        #10 seq_in = 1; 
        #10 seq_in = 0; 
        #10 seq_in = 1; 
        #10 seq_in = 1; 
        #10 seq_in = 0; 
        #10 seq_in = 1; 
        #10 seq_in = 0; 
        #10 seq_in = 1; 
        #10 seq_in = 1; 
        #50 $finish; 
    end 

    initial begin 
        $monitor("Time=%0t | reset=%b | seq_in=%b | detected=%b", $time, reset, seq_in, detected); 
    end 

endmodule

OUTPUT:
![MOORE-SD-TB](https://github.com/user-attachments/assets/b8b65e11-b578-4ed6-91f7-31b8c0df8e7e)

Conclusion
In this experiment, Moore and Mealy FSMs were successfully designed and simulated to detect the sequence 1011. Both designs worked as expected, with the main difference being that the Moore FSM generated the output based on the current state, while the Mealy FSM generated the output based on both the current state and input. The testbench verified the functionality of both FSMs, demonstrating that the Verilog HDL can effectively model both types of state machines for sequence detection tasks.
