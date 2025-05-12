
# ECE 251a Homework 08

## 1.a What is the clock cycle time in a pipelined and non-pipelined processor?

Pipelined: 350 ps 

Non-pipelined: 1250 ps 

## 1.b What is the total latency of an `lw` instruction in a pipelined and non-pipelined processor?

Pipelined: 5 cycles × 350 ps = 1750 ps

Non-pipelined: 1250 ps

## 1.c If we can split one stage of the pipelined datapath into two new stages, each with half the latency of the original stage, which stage would you split and what is the new clock cycle time of the processor?

Split ID stage (350 ps) → two stages of 175 ps

New max stage = MEM = 300 ps

New pipelined clock cycle time: 300 ps

## 1.d Assuming there are no stalls or hazards, what is the utilization of the data memory?

Load (20%) + Store (15%) = 35%

## 1.e Assuming there are no stalls or hazards, what is the utilization of the write-register port of the “Registers” unit?

ALU (45%) + Load (20%) = 65%

## 2. Assume that $s0 is initialized to 11 and $s1 is initialized to 22. Suppose you executed the code below on a version of the pipeline from Section 4.6 that does not handle data hazards (i.e., the programmer is responsible for addressing data hazards by inserting NOP instructions where necessary). What would the final values of registers $s2 and $s3 be?

addi $s0, $s1, 5       # $s0 = 22 + 5 = 27

add $s2, $s0, $s1      # Uses old $s0 = 11 → $s2 = 33

addi $s3, $s0, 15      # Uses old $s0 = 11 → $s3 = 26


Final $s2 = 33

Final $s3 = 26


## 3. Add NOP instructions to the code below so that it will run correctly on a pipeline that does not handle data hazards.

addi $s0, $s1, 5

nop

nop

add $s2, $s0, $s1

nop

nop

addi $s3, $s0, 15

nop

nop

add $s4, $s2, $s1

### 4. Problems in this exercise refer to the following sequence of instructions, and assume that it is executed on a five-stage pipelined datapath:

### 4.a  If there is no forwarding or hazard detection, insert NOPs to ensure correct execution.
add $s3, $s1, $s0

nop

nop

lw $s2, 4($s3)

lw $s1, 0($s4)

nop

nop

or $s2, $s3, $s2

sw $s2, 0($s3)

### 4.b Now, change and/or rearrange the code to minimize the number of NOPs needed. You can assume register $t0 can be used to hold temporary values in your modified code.

add $s3, $s1, $s0

lw $t0, 0($s4)

lw $s2, 4($s3)

or $s2, $s3, $s2

sw $s2, 0($s3)


### 4.c If the processor has forwarding, but we forgot to implement the hazard detection unit, what happens when the original code executes?
Load-use hazard still causes incorrect execution

### 4.d If there is forwarding, for the first seven cycles during the execution of this code, specify which signals are asserted in each cycle by hazard detection and forwarding units in Figure 4.59.
- ForwardA, ForwardB values change by cycle 
- No stall if hazard unit is missing, but results may be wrong

### 4.e  If there is no forwarding, what new input and output signals do we need for the hazard detection unit in Figure 4.59? Using this instruction sequence as an example, explain why each signal is needed.
- `ID/EX.MemRead`
- `ID/EX.RegisterRt` vs `IF/ID.RegisterRs/Rt`

### 5. This exercise explores how exception handling affects pipeline design. Refer to the following two instructions: Instruction 1 = beqz $s0, LABEL Instruction 2 = ld $s0, 0($s1)

### 5.a Which exceptions can each of these instructions trigger? For each of these exceptions, specify the pipeline stage in which it is detected.
- beqz $s0, LABEL:
  - Instruction fetch error / misalignment 
- ld $s0, 0($s1):
  - Memory alignment, access, or page fault 

### 5.b If the second instruction is fetched immediately after the first instruction, describe what happens in the pipeline when the first instruction causes the first exception you listed in Exercise 4.30.1. Show the pipeline execution diagram from the time the first instruction is fetched until the time the first instruction of the exception handler is completed.

Cycle 1: IF (beqz)

Cycle 2: ID (beqz), IF (ld)

Cycle 3: Exception detected, flush pipeline

...
Then: Fetch first instruction of exception handler

- Pipeline is flushed
- Handler starts from IF stage
