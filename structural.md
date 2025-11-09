# Comprehensive Notes on Structural (Gate-Level) Modeling in Verilog

## Overview
**Structural modeling** (also called **Gateway modeling** or **Gate-level modeling**) describes circuits by **instantiating predefined or user-defined primitive gates** such as `and`, `or`, `not`, etc. It provides a **textual representation of a schematic diagram**, showing how individual logic gates are interconnected to form the desired hardware.

---

## PRELIM EXAM CONTENT: 3 Model Styles of Verilog

1. **Structural (Gate-Level) Modeling** ← Focus of these notes
2. **Dataflow Modeling**
3. **Behavioral Modeling**

---

## Program Structure

### **Module Definition**
A module can be specified:
- **Behaviourally** (using `always`, `initial`)
- **Structurally** (using gate instantiations)
- **Combination of both**

---

## Structure of a Module (Structural Focus)

### **1. MODULE NAME**

**Definition:** The `<module name>` is an identifier that uniquely names the module.

**Structure:**
- Each module definition includes the keyword `module`
- Followed by the module name
- Terminated by the `endmodule` statement

**Example:**
```verilog
module samplegate (a, b, c, y);
    // module contents
endmodule
```

**Key Points:**
- Must be a unique identifier
- Follows Verilog naming conventions (alphanumeric, underscore, must start with letter)
- Case-sensitive

---

### **2. PORT**

**Definition:** The **port** defines the **interface of the module**. It specifies the signals through which the module communicates with other modules or the external environment.

**Characteristics:**
- Ports are **listed inside parentheses** after the module name
- Later declared as **inputs, outputs, or in-outs** within the module
- Acts as the **connection points** for the module

**Syntax:**
```verilog
module module_name (port1, port2, port3, ...);
```

**Example from Your Code:**
```verilog
module samplegate (a, b, c, y);
    // a, b, c, y are ports
```

**Port Types:**
- **input**: Signals coming INTO the module
- **output**: Signals going OUT of the module
- **inout**: Bidirectional signals (rarely used)

---

### **3. DECLARES**

**Definition:** The **declares section** defines all internal and external signals used inside the module.

**What It Includes:**
- **Input declarations**: Define which ports are inputs
- **Output declarations**: Define which ports are outputs
- **Inout declarations**: Define bidirectional ports
- **Internal wires**: Internal connections between gates
- **Registers**: Storage elements (for behavioral code)
- **Parameters**: Constants used in the module

**Purpose:** Declarations describe the **data type, direction, and role** of each signal within the module.

**Example:**
```verilog
module samplegate (a, b, c, y);
    input a, b, c;    // Port direction declarations
    output y;         // Output declaration
    wire w1;          // Internal wire declaration
```

**Declaration Types in Structural Modeling:**

| Declaration | Purpose | Example |
|------------|---------|---------|
| `input` | Signals entering module | `input a, b, c;` |
| `output` | Signals leaving module | `output y;` |
| `wire` | Internal connections | `wire w1, w2;` |
| `inout` | Bidirectional ports | `inout data_bus;` |

---

### **4. MODULE ITEMS**

**Definition:** The **module items** are the **internal components or statements** that describe the functionality or structure of the module.

**In Structural Modeling, Module Items Include:**
- **Continuous assignments** (`assign`)
- **Procedural blocks** (`always`, `initial`)
- **Gate instantiations** ← **PRIMARY FOCUS for Structural Modeling**
- **Submodule instantiations**

These define **how the circuit behaves or is interconnected**.

---

## Gate Instantiations (Core of Structural Modeling)

### **Gate Instantiation Syntax**

```verilog
gate_type instance_name (connections);
```

**Components:**
1. **`gate_type`**: The primitive gate (e.g., `and`, `or`, `not`, `nand`, `nor`, `xor`, `xnor`)
2. **`instance_name`**: A unique identifier (instantiation label) for this specific gate
3. **`connections`**: Port connections in the order (output first, then inputs)

---

### **Complete Example Breakdown**

```verilog
module samplegate (a, b, c, y);
    input a, b, c;
    output y;
    wire w1;
    
    and U1 (w1, a, b);
    or  U2 (y, w1, c);
endmodule
```

**Detailed Analysis:**

| Part | Example | Description |
|------|---------|-------------|
| **Module Name** | `samplegate` | Identifier that uniquely names this module. Begins with `module`, ends with `endmodule` |
| **Port** | `(a, b, c, y)` | Connection points for signals. Listed in parentheses after module name |
| **Declares** | `input a, b, c;`<br>`output y;`<br>`wire w1;` | Define direction (input/output), type (wire), and role of all signals |
| **Module Items** | `and U1 (w1, a, b);`<br>`or U2 (y, w1, c);` | Internal components (gate instantiations) that describe structure and connectivity |

---

## Instance Names and Instantiation Labels

### **Understanding Instance Names**

**From the code:**
```verilog
and U1 (w1, a, b);
or  U2 (y, w1, c);
```

**U1** and **U2** are **instance names** (also called **instantiation labels**):
- They are **identifiers** given to each gate instance
- Used to **uniquely name** them inside the module
- **Required** for structural modeling to distinguish between multiple instances of the same gate type

**Breakdown:**
- **`and`**: Gate type (built-in primitive gate)
- **`U1`**: Instance name for the AND gate
- **`(w1, a, b)`**: Connections
  - **`w1`**: Output (always first)
  - **`a, b`**: Inputs (in order)

---

## Built-in Primitive Gates

Verilog provides several **built-in primitive gates**:

| Gate Type | Symbol | Function | Verilog Keyword |
|-----------|--------|----------|-----------------|
| AND | ![AND gate symbol] | Y = A · B | `and` |
| OR | ![OR gate symbol] | Y = A + B | `or` |
| NOT | ![NOT gate symbol] | Y = Ā | `not` |
| NAND | ![NAND gate symbol] | Y = (A · B)' | `nand` |
| NOR | ![NOR gate symbol] | Y = (A + B)' | `nor` |
| XOR | ![XOR gate symbol] | Y = A ⊕ B | `xor` |
| XNOR | ![XNOR gate symbol] | Y = (A ⊕ B)' | `xnor` |
| Buffer | ![Buffer symbol] | Y = A | `buf` |
| Tri-state | ![Tri-state symbol] | Controlled buffer | `bufif0`, `bufif1` |

---

## Gate Instantiation Rules

### **Port Connection Order**
**CRITICAL:** Output port ALWAYS comes first, then inputs

```verilog
gate_type instance_name (output, input1, input2, ...);
```

**Examples:**

**✓ CORRECT:**
```verilog
and U1 (w1, a, b);     // w1 is output, a and b are inputs
or  U2 (y, w1, c);     // y is output, w1 and c are inputs
not U3 (out, in);      // out is output, in is input
```

**✗ INCORRECT:**
```verilog
and U1 (a, b, w1);     // Wrong order! Output must be first
```

---

## Complete Structural Modeling Examples

### **Example 1: Basic AND-OR Circuit**

**Circuit Function:** Y = (A · B) + C

```verilog
module samplegate (a, b, c, y);
    input a, b, c;
    output y;
    wire w1;          // Internal connection
    
    and U1 (w1, a, b);    // w1 = a AND b
    or  U2 (y, w1, c);    // y = w1 OR c
endmodule
```

**Schematic Representation:**
```
    a ─┐
       ├─[AND U1]─── w1 ─┐
    b ─┘                  ├─[OR U2]─── y
    c ────────────────────┘
```

---

### **Example 2: Half Adder (Structural)**

**Logic:**
- SUM = A ⊕ B (XOR)
- CARRY = A · B (AND)

```verilog
module half_adder (a, b, sum, carry);
    input a, b;
    output sum, carry;
    
    xor X1 (sum, a, b);      // sum = a XOR b
    and A1 (carry, a, b);    // carry = a AND b
endmodule
```

---

### **Example 3: Full Adder (Structural)**

**Logic:**
- SUM = A ⊕ B ⊕ Cin
- CARRY_OUT = (A · B) + (B · Cin) + (A · Cin)

```verilog
module full_adder (a, b, cin, sum, cout);
    input a, b, cin;
    output sum, cout;
    wire w1, w2, w3, w4;
    
    // Sum logic
    xor X1 (w1, a, b);
    xor X2 (sum, w1, cin);
    
    // Carry logic
    and A1 (w2, a, b);
    and A2 (w3, b, cin);
    and A3 (w4, a, cin);
    or  O1 (cout, w2, w3, w4);
endmodule
```

---

### **Example 4: 4-bit Ripple Carry Adder (Hierarchical)**

```verilog
module ripple_carry_adder_4bit (a, b, cin, sum, cout);
    input [3:0] a, b;
    input cin;
    output [3:0] sum;
    output cout;
    wire c1, c2, c3;
    
    // Instantiate 4 full adders
    full_adder FA0 (a[0], b[0], cin, sum[0], c1);
    full_adder FA1 (a[1], b[1], c1,  sum[1], c2);
    full_adder FA2 (a[2], b[2], c2,  sum[2], c3);
    full_adder FA3 (a[3], b[3], c3,  sum[3], cout);
endmodule
```

---

### **Example 5: 2-to-4 Decoder (Structural)**

```verilog
module decoder_2to4 (a, b, y0, y1, y2, y3);
    input a, b;
    output y0, y1, y2, y3;
    wire a_bar, b_bar;
    
    // Generate complemented inputs
    not N1 (a_bar, a);
    not N2 (b_bar, b);
    
    // Generate outputs
    and A0 (y0, a_bar, b_bar);  // y0 = a'b'
    and A1 (y1, a_bar, b);      // y1 = a'b
    and A2 (y2, a, b_bar);      // y2 = ab'
    and A3 (y3, a, b);          // y3 = ab
endmodule
```

---

### **Example 6: 4:1 Multiplexer (Structural)**

```verilog
module mux_4to1 (i0, i1, i2, i3, s0, s1, out);
    input i0, i1, i2, i3, s0, s1;
    output out;
    wire s0_bar, s1_bar;
    wire w0, w1, w2, w3;
    
    // Complement select lines
    not N1 (s0_bar, s0);
    not N2 (s1_bar, s1);
    
    // AND gates for each input
    and A0 (w0, i0, s1_bar, s0_bar);
    and A1 (w1, i1, s1_bar, s0);
    and A2 (w2, i2, s1, s0_bar);
    and A3 (w3, i3, s1, s0);
    
    // OR gate for output
    or O1 (out, w0, w1, w2, w3);
endmodule
```

---

## Hierarchical Design (Module Instantiation)

Structural modeling also allows **instantiation of user-defined modules** (submodules), not just primitive gates.

### **Syntax:**
```verilog
module_name instance_name (port_connections);
```

### **Example: 4-bit Adder Using Half Adders**

```verilog
// First define the half adder
module half_adder (a, b, sum, carry);
    input a, b;
    output sum, carry;
    
    xor X1 (sum, a, b);
    and A1 (carry, a, b);
endmodule

// Use half adders to build a full adder
module full_adder_from_ha (a, b, cin, sum, cout);
    input a, b, cin;
    output sum, cout;
    wire s1, c1, c2;
    
    // Instantiate two half adders
    half_adder HA1 (a, b, s1, c1);
    half_adder HA2 (s1, cin, sum, c2);
    
    // OR gate for final carry
    or O1 (cout, c1, c2);
endmodule
```

---

## Key Characteristics of Structural Modeling

### **Advantages:**
1. **Exact hardware representation** - mirrors physical schematic
2. **Clear gate-level view** - easy to visualize circuit structure
3. **Technology mapping** - can target specific gate libraries
4. **Fine control** - precise control over implementation
5. **Educational** - excellent for learning digital logic

### **Disadvantages:**
1. **Verbose** - requires many lines for complex circuits
2. **Tedious** - manual connection of all wires
3. **Error-prone** - easy to make wiring mistakes
4. **Hard to modify** - structural changes require rewiring
5. **Not scalable** - impractical for large designs

---

## Comparison of Three Modeling Styles

| Aspect | Structural | Dataflow | Behavioral |
|--------|-----------|----------|------------|
| **Abstraction** | Lowest | Medium | Highest |
| **Description** | Gate instantiations | Continuous assignments | Procedural statements |
| **Keywords** | Gate primitives (`and`, `or`, etc.) | `assign` | `always`, `initial` |
| **Focus** | Physical structure | Data flow | Functionality/behavior |
| **Best For** | Small circuits, exact control | Combinational logic | Complex logic, algorithms |
| **Example** | `and U1 (out, a, b);` | `assign out = a & b;` | `always @(*) out = a & b;` |

---

## Best Practices for Structural Modeling

1. **Use meaningful instance names** (e.g., `AND_GATE1`, `MUX_A` instead of `U1`, `U2`)
2. **Declare all internal wires** explicitly
3. **Follow consistent naming conventions**
4. **Comment complex interconnections**
5. **Draw schematic first**, then code
6. **Verify port order** (output first, then inputs)
7. **Use hierarchical design** for complex circuits
8. **Group related gates** with comments

---

## Common Mistakes to Avoid

### **❌ Mistake 1: Wrong Port Order**
```verilog
and U1 (a, b, out);  // WRONG! Output must be first
```
**✓ Correct:**
```verilog
and U1 (out, a, b);  // Correct
```

### **❌ Mistake 2: Undeclared Wires**
```verilog
and U1 (w1, a, b);   // ERROR if w1 not declared as wire
```
**✓ Correct:**
```verilog
wire w1;
and U1 (w1, a, b);
```

### **❌ Mistake 3: Duplicate Instance Names**
```verilog
and U1 (w1, a, b);
or  U1 (y, w1, c);   // ERROR! U1 already used
```
**✓ Correct:**
```verilog
and U1 (w1, a, b);
or  U2 (y, w1, c);   // Unique instance names
```

---

## Summary

**Structural (Gate-Level) Modeling** is one of three fundamental modeling styles in Verilog. It describes circuits using **instantiations of predefined or user-defined primitive gates**, providing a **textual representation of schematic diagrams**.

### **Key Components:**
1. **Module Name**: Unique identifier for the module
2. **Port**: Interface defining inputs/outputs
3. **Declares**: Signal definitions (input, output, wire)
4. **Module Items**: Gate instantiations and interconnections

### **Structure:**
```verilog
module module_name (ports);
    // Declares
    input ...;
    output ...;
    wire ...;
    
    // Module Items (gate instantiations)
    gate_type instance_name (output, inputs);
endmodule
```

Structural modeling is essential for understanding hardware at the gate level and forms the foundation for more abstract modeling styles. It's the closest representation to actual hardware implementation, making it invaluable for learning digital design fundamentals.
