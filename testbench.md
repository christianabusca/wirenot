# Comprehensive Guide to Verilog Testbenches
## Based on Structural, Dataflow, and Behavioral Modeling Notes

---

## What is a Testbench?

A **testbench** is a Verilog module used to **test your design**. Think of it as a "testing environment" that:
- Generates input signals for your circuit
- Monitors the output signals
- Verifies if your design works correctly
- **Does NOT have ports** (no inputs/outputs)
- **Is NOT synthesized** (simulation only)

---

## Basic Testbench Structure

```verilog
module testbench_name;
    // 1. Declare signals
    reg inputs;        // Use 'reg' for signals YOU control
    wire outputs;      // Use 'wire' for signals you monitor
    
    // 2. Instantiate your design (DUT - Device Under Test)
    design_module instance_name (ports);
    
    // 3. Generate test inputs
    initial begin
        // Your test cases here
    end
endmodule
```

---

## Key Differences: Design vs Testbench

| Design Module | Testbench |
|---------------|-----------|
| Has ports (input/output) | NO ports |
| Gets synthesized to hardware | Only for simulation |
| Uses `wire` for outputs | Uses `reg` to drive inputs |
| Describes circuit behavior | Tests the circuit |

---

## Example 1: Testbench for Structural Model

### Design (from your notes):
```verilog
module samplegate (a, b, c, y);
    input a, b, c;
    output y;
    wire w1;
    
    and U1 (w1, a, b);
    or  U2 (y, w1, c);
endmodule
```

### Testbench:
```verilog
module tb_samplegate;
    // 1. Declare signals
    reg a, b, c;      // We control these (inputs to design)
    wire y;           // We monitor this (output from design)
    
    // 2. Instantiate the design
    samplegate DUT (a, b, c, y);
    
    // 3. Generate test inputs
    initial begin
        // Test different input combinations
        a = 0; b = 0; c = 0;
        #10;  // Wait 10 time units
        
        a = 0; b = 0; c = 1;
        #10;
        
        a = 0; b = 1; c = 0;
        #10;
        
        a = 1; b = 1; c = 0;
        #10;
        
        a = 1; b = 1; c = 1;
        #10;
        
        $finish;  // End simulation
    end
    
    // 4. Monitor outputs (optional)
    initial begin
        $monitor("Time=%0t: a=%b b=%b c=%b | y=%b", $time, a, b, c, y);
    end
endmodule
```

---

## Example 2: Testbench for Dataflow Model

### Design (from your notes):
```verilog
module sample_gate(a, b, c, d);
    input a, b, c;
    output d;
    
    assign d = (a & b) | c;
endmodule
```

### Testbench:
```verilog
module tb_sample_gate;
    reg a, b, c;
    wire d;
    
    sample_gate DUT (a, b, c, d);
    
    initial begin
        $monitor("a=%b b=%b c=%b | d=%b", a, b, c, d);
        
        a = 0; b = 0; c = 0; #10;
        a = 0; b = 1; c = 0; #10;
        a = 1; b = 0; c = 0; #10;
        a = 1; b = 1; c = 0; #10;
        a = 0; b = 0; c = 1; #10;
        
        $finish;
    end
endmodule
```

---

## Example 3: Testbench for Behavioral Model

### Design (from your notes):
```verilog
module mux_2x1(a, b, sel, out);
    input a, b, sel;
    output out;
    reg out;
    
    always @(a or b or sel)
    begin
        if (sel == 1)
            out = a;
        else
            out = b;
    end
endmodule
```

### Testbench:
```verilog
module tb_mux_2x1;
    reg a, b, sel;
    wire out;
    
    mux_2x1 DUT (a, b, sel, out);
    
    initial begin
        $monitor("Time=%0t: sel=%b a=%b b=%b | out=%b", $time, sel, a, b, out);
        
        // Test sel=0 (should output b)
        sel = 0; a = 1; b = 0;
        #10;
        
        sel = 0; a = 0; b = 1;
        #10;
        
        // Test sel=1 (should output a)
        sel = 1; a = 1; b = 0;
        #10;
        
        sel = 1; a = 0; b = 1;
        #10;
        
        $finish;
    end
endmodule
```

---

## Example 4: Testbench for Design with Delays

### Design with Inter-Statement Delay (from your notes):
```verilog
module sample_gate(A, B, C, Y);
    input A, B, C;
    output Y;
    reg Y;
    
    always @(A or B or C)
    begin
        #2 Y = (A & B) | (A & C);  // Delay BEFORE execution
    end
endmodule
```

### Testbench:
```verilog
module tb_sample_gate_delay;
    reg A, B, C;
    wire Y;
    
    sample_gate DUT (A, B, C, Y);
    
    initial begin
        $monitor("Time=%0t: A=%b B=%b C=%b | Y=%b", $time, A, B, C, Y);
        
        A = 0; B = 0; C = 0;
        #10;  // Output changes 2 time units after input change
        
        A = 1; B = 1; C = 0;
        #10;
        
        A = 1; B = 0; C = 1;
        #10;
        
        $finish;
    end
endmodule
```

---

## Example 5: Testbench for Design with Intra-Statement Delay

### Design with Intra-Statement Delay (from your notes):
```verilog
module sample_gate(A, B, C, Y);
    input A, B, C;
    output Y;
    reg Y;
    
    always @(A or B or C)
    begin
        Y = #2 (A & B) | (A & C);  // Evaluate NOW, assign AFTER delay
    end
endmodule
```

### Testbench:
```verilog
module tb_sample_gate_intra;
    reg A, B, C;
    wire Y;
    
    sample_gate DUT (A, B, C, Y);
    
    initial begin
        $monitor("Time=%0t: A=%b B=%b C=%b | Y=%b", $time, A, B, C, Y);
        
        A = 0; B = 0; C = 0;
        #10;
        
        A = 1; B = 1; C = 0;
        #10;
        
        $finish;
    end
endmodule
```

---

## Important System Tasks

### `$monitor` - Automatic Display
```verilog
$monitor("format", variables);
```
- Prints **automatically** when any variable changes
- Only need to call it **once**

**Example:**
```verilog
$monitor("Time=%0t: a=%b b=%b out=%b", $time, a, b, out);
```

### `$display` - Manual Display
```verilog
$display("format", variables);
```
- Prints **only when executed**
- Need to call it **each time** you want output

**Example:**
```verilog
#10;
$display("At time 10: a=%b", a);
```

### `$finish` - End Simulation
```verilog
$finish;
```
- Stops the simulation

### `$time` - Current Simulation Time
```verilog
$time
```
- Returns current simulation time

---

## Signal Types in Testbench

| Type | Use | Example |
|------|-----|---------|
| `reg` | Signals YOU drive (inputs to DUT) | `reg a, b, sel;` |
| `wire` | Signals you monitor (outputs from DUT) | `wire out, sum;` |

**Simple Rule:**
- If you're **setting the value** → use `reg`
- If you're **reading the value** → use `wire`

---

## Time Delays in Testbench

```verilog
#10;  // Wait 10 time units
```

**Example:**
```verilog
initial begin
    a = 0; b = 0;
    #10;  // Wait 10 time units
    a = 1; b = 0;
    #10;  // Wait another 10 time units
    a = 1; b = 1;
end
```

**Timeline:**
```
Time:  0    10   20
       |----|----|
a:     0    1    1
b:     0    0    1
```

---

## Complete Example: Half Adder

### Design (Behavioral):
```verilog
module half_adder(A, B, SUM, CARRY);
    input A, B;
    output SUM, CARRY;
    reg SUM, CARRY;
    
    always @(A or B)
    begin
        SUM = A ^ B;
        CARRY = A & B;
    end
endmodule
```

### Testbench:
```verilog
module tb_half_adder;
    reg A, B;
    wire SUM, CARRY;
    
    // Instantiate half adder
    half_adder DUT (A, B, SUM, CARRY);
    
    // Monitor outputs
    initial begin
        $monitor("Time=%0t: A=%b B=%b | SUM=%b CARRY=%b", 
                 $time, A, B, SUM, CARRY);
    end
    
    // Generate test inputs
    initial begin
        // Test 0 + 0
        A = 0; B = 0;
        #10;
        
        // Test 0 + 1
        A = 0; B = 1;
        #10;
        
        // Test 1 + 0
        A = 1; B = 0;
        #10;
        
        // Test 1 + 1
        A = 1; B = 1;
        #10;
        
        $finish;
    end
endmodule
```

**Expected Output:**
```
Time=0:  A=0 B=0 | SUM=0 CARRY=0
Time=10: A=0 B=1 | SUM=1 CARRY=0
Time=20: A=1 B=0 | SUM=1 CARRY=0
Time=30: A=1 B=1 | SUM=0 CARRY=1
```

---

## Testing OR Gate (from your notes)

### Design:
```verilog
module or_gate(x1, x2, y);
    input x1, x2;
    output y;
    reg y;
    
    always @(x1 or x2)
    begin
        y = x1 | x2;
    end
endmodule
```

### Testbench:
```verilog
module tb_or_gate;
    reg x1, x2;
    wire y;
    
    or_gate DUT (x1, x2, y);
    
    initial begin
        $monitor("x1=%b x2=%b | y=%b", x1, x2, y);
        
        x1 = 0; x2 = 0; #10;
        x1 = 0; x2 = 1; #10;
        x1 = 1; x2 = 0; #10;
        x1 = 1; x2 = 1; #10;
        
        $finish;
    end
endmodule
```

---

## Summary

**3 Simple Steps to Write a Testbench:**

1. **Declare signals**
   - Use `reg` for inputs you control
   - Use `wire` for outputs you monitor

2. **Instantiate your design**
   ```verilog
   module_name instance_name (ports);
   ```

3. **Generate test inputs using `initial` block**
   ```verilog
   initial begin
       input1 = value;
       #delay;
       input2 = value;
       $finish;
   end
   ```

**That's it!** A testbench is just a way to feed different inputs to your design and see if the outputs are correct.
