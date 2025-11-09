# Comprehensive Notes on Behavioral Modeling in Verilog

## Overview
Behavioral modeling in Verilog uses **procedural assignment statements** to describe hardware functionality at a higher level of abstraction. It focuses on **what the circuit does** rather than how it's structurally built, making it similar to software programming.

## Key Characteristics

### **USING PROCEDURAL ASSIGNMENT STATEMENTS - ALWAYS**
- Uses `always` blocks for describing behavior
- Executes statements sequentially (like software)
- Can model both combinational and sequential logic
- Higher level of abstraction than dataflow modeling

---

## Basic Structure

### 1. **Simple Multiplexer Example (2:1 MUX)**

```verilog
module mux_2x1(a, b, sel, out);
    input a, b, sel;
    output out;
    
    always @(a or b or sel)  // Sensitivity list
    begin
        if (sel == 1)
            out = a;
        else
            out = b;
    end
endmodule
```

**Key Components:**
- `always @(...)`: Sensitivity list - executes when these signals change
- `begin...end`: Block statement (required for multiple statements)
- `if...else`: Conditional execution
- Sequential execution within the block

### 2. **OR Gate Example**

```verilog
module or_gate(x, y, z);
    input x, y;
    output y;
    reg y;
    
    always @(x1 or x2)
    begin
        y = x1 | x2;
    end
endmodule
```

**Important Notes:**
- Output declared as `reg` type (required for procedural assignments)
- Sensitivity list includes all inputs
- Uses blocking assignment `=`

---

## Register Types in Behavioral Modeling

### **Output Declaration: `reg` Keyword**

```verilog
module sample_gate(a, b, c, d);
    input a, b, c;
    output d;
    reg d;  // MUST be declared as reg for behavioral assignments
    
    always @(a or b or c)
    begin
        d = (a & b) | c;
    end
endmodule
```

**Why `reg`?**
- In behavioral modeling, outputs assigned in `always` blocks MUST be declared as `reg`
- `reg` doesn't necessarily mean a physical register/flip-flop
- It's a variable type that can hold values in procedural blocks

---

## THE TWO TYPES OF DELAYS

Your notes clearly distinguish between two critical delay types in Verilog:

### 1. **INTER-STATEMENT DELAY (Delayed Execution)**

**Definition:** Delay BEFORE executing the statement

**Syntax:** `#delay_value statement;`

**Example:**
```verilog
module sample_gate(A, B, C, Y);
    input A, B, C;
    output Y;
    reg Y;
    
    always @(A or B or C)
    begin
        #2 Y = (A & B) ~ (A & C);  // Wait 2 time units, THEN execute
    end
endmodule
```

**Characteristics:**
- Delay occurs **before** the statement executes
- Format: `#time_value` before assignment
- **SUM = A ∧ B** (from your notes)
- **#2 CARRY = A & B** - waits 2 time units before executing

**Timing Diagram Representation:**
```
Time:  0    1    2    3    4
       |----|----|----|----|
Input change at t=0
              ↓
         Delay #2
              ↓
         Execute at t=2
```

### 2. **INTRA-STATEMENT DELAY (Delayed Assignment)**

**Definition:** Evaluate NOW, assign LATER

**Syntax:** `variable = #delay_value expression;`

**Example:**
```verilog
module sample_gate(A, B, C, Y);
    input A, B, C;
    output Y;
    reg Y;
    
    always @(A or B or C)
    begin
        Y = #2 (A & B) ~ (A & C);  // Evaluate NOW, assign after 2 time units
    end
endmodule
```

**Characteristics:**
- Expression evaluated **immediately** when triggered
- Result stored and assigned **after** the delay
- Format: `#time_value` after the `=` sign
- **SUM = A ∧ B;**
- **CARRY = #2 A & B;** - evaluates immediately, assigns after 2 time units

**Timing Diagram Representation:**
```
Time:  0    1    2    3    4
       |----|----|----|----|
Input change & evaluate at t=0
              ↓
       Hold value
              ↓
       Assign at t=2
```

---

## KEY DIFFERENCES: Inter vs Intra Statement Delay

| Aspect | Inter-Statement Delay | Intra-Statement Delay |
|--------|----------------------|----------------------|
| **Syntax** | `#2 Y = expression;` | `Y = #2 expression;` |
| **Evaluation** | After delay | Immediately |
| **Assignment** | After delay | After delay |
| **Use Case** | Delay entire operation | Capture value, delay assignment |
| **Your Notes** | `#2 CARRY = A & B;` | `CARRY = #2 A & B;` |

---

## Behavioral Statements

### 1. **Conditional Statements**

**IF Statement:**
```verilog
if (condition)
    statement;
else
    statement;
```

**IF-ELSE-IF Ladder:**
```verilog
if (condition1)
    statement1;
else if (condition2)
    statement2;
else
    statement3;
```

**CASE Statement:**
```verilog
case (expression)
    value1: statement1;
    value2: statement2;
    default: statement3;
endcase
```

### 2. **Loop Statements**

**WHILE Loop:**
```verilog
while (condition)
begin
    statements;
end
```

**FOR Loop:**
```verilog
for (initialization; condition; increment)
begin
    statements;
end
```

---

## Execution Types

### **INITIAL Block - EXECUTE ONCE**
```verilog
initial
begin
    // Executes only once at simulation start (time 0)
    // Used for initialization and testbenches
end
```

**Characteristics:**
- Executes once at time 0
- Mainly used in testbenches
- Not synthesizable for hardware

### **ALWAYS Block - EXECUTE IN LOOP (Continuous)**
```verilog
always @(sensitivity_list)
begin
    // Executes whenever signals in sensitivity list change
    // Runs throughout simulation
end
```

**Characteristics:**
- Executes repeatedly whenever triggered
- Used for actual hardware description
- Synthesizable

---

## Sensitivity Lists

### **Explicit Sensitivity List:**
```verilog
always @(a or b or sel)  // Triggers when a, b, or sel changes
```

### **Shorthand (Verilog-2001):**
```verilog
always @*  // Automatically includes all signals read in the block
always @(*)  // Same as above
```

### **Edge Sensitivity (Sequential Logic):**
```verilog
always @(posedge clk)  // Triggers on rising edge of clock
always @(negedge reset)  // Triggers on falling edge of reset
always @(posedge clk or negedge reset)  // Multiple edge triggers
```

---

## Complete Examples from Your Notes

### **Example 1: Basic AND-OR Gate with Delay**
```verilog
module sample_gate(A, B, C, Y);
    input A, B, C;
    output Y;
    reg Y;
    
    always @(A or B or C)
    begin
        Y = (A & B) ~ (A & C);
    end
endmodule
```

### **Example 2: Half Adder with Inter-Statement Delay**
```verilog
module half_adder(A, B, SUM, CARRY);
    input A, B;
    output SUM, CARRY;
    reg SUM, CARRY;
    
    always @(A or B)
    begin
        SUM = A ^ B;
        #2 CARRY = A & B;  // Inter-statement delay
    end
endmodule
```

### **Example 3: Half Adder with Intra-Statement Delay**
```verilog
module half_adder(A, B, SUM, CARRY);
    input A, B;
    output SUM, CARRY;
    reg SUM, CARRY;
    
    always @(A or B)
    begin
        SUM = A ^ B;
        CARRY = #2 A & B;  // Intra-statement delay
    end
endmodule
```

---

## Circuit Diagram from Your Notes

The circuit shows a combination of:
- Three inputs: A, B, C
- Logic gates: AND, OR gates
- Output: Y
- Implements: `Y = (A & B) | (A & C)` or similar logic

---

## Best Practices

1. **Always use `reg` for outputs** in `always` blocks
2. **Include all inputs in sensitivity list** for combinational logic
3. **Use `begin...end`** for multiple statements
4. **Choose appropriate delay type:**
   - Inter-statement when you want to delay the entire operation
   - Intra-statement when you want to capture current values but delay assignment
5. **Use `always @*`** for automatic sensitivity list (Verilog-2001+)
6. **Blocking `=` for combinational**, Non-blocking `<=` for sequential

---

## Comparison: Modeling Styles

### **Dataflow vs Behavioral**

**Dataflow:**
```verilog
assign out = (a & b) | c;
```
- Concurrent execution
- Continuous assignment
- Good for simple combinational logic

**Behavioral:**
```verilog
always @(a or b or c)
begin
    out = (a & b) | c;
end
```
- Sequential execution within block
- More control with if/else, loops
- Good for complex logic and sequential circuits

---

## Common Applications

- **Combinational Logic:** ALUs, multiplexers, encoders
- **Sequential Logic:** Flip-flops, counters, state machines
- **Memory Elements:** Registers, RAM, ROM
- **Complex Controllers:** CPUs, communication protocols
- **Testbenches:** Simulation and verification

---

## Summary

Behavioral modeling provides the highest level of abstraction in Verilog, using procedural statements similar to software programming. The two types of delays—**inter-statement** (delay before execution) and **intra-statement** (evaluate now, assign later)—give precise control over timing behavior. Understanding these concepts is crucial for both simulation accuracy and hardware synthesis.

**Key Takeaway:** Use `reg` types, `always` blocks, and understand delay semantics to create powerful, flexible hardware descriptions that are easier to write and maintain than lower-level modeling styles.
