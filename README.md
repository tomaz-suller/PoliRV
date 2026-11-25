# PoliRV

<img src="github/pcs.png" style="height: 1em; vertical-align: middle;" align="right" />

Simplified, five-stage pipelined version of a RISC-V processor
designed by the Department of Computer and Digital Systems
Engineering (PCS) at Escola Politécnica da Universidade de São
Paulo (Poli-USP) and implemented in VHDL for Computer Architectures II.

The pipeline follows the classic IF/ID/EX/MEM/WB organisation,
with data forwarding, load-use stalls and flushing of taken
branches (resolved in the MEM stage).

## Requirements
* [GHDL](https://github.com/ghdl/ghdl)
* [hdlmake](https://hdlmake.readthedocs.io/en/master/)
* [GTKWave](http://gtkwave.sourceforge.net/) (optional, used for visualising generated waveforms as shown above)

## Build
The build process once hdlmake is installed is straightforward:

1. Clone this repository
```bash
git clone https://github.com/tomaz-suller/PoliRV.git
```

2. Enter the `simulation` directory and build with hdlmake
```bash
cd simulation
hdlmake makefile
make
```
The default options will run `complete` and automatically
open GTKWave to show the generated waveform using the
configuration file supplied (`pipeline.gtkw`). The loaded
program is selected by the `FOLDER` constant in
`modules/parameters.vhd`. Simulation and GTKWave behaviour
can be altered by editing `simulation/Manifest.py` and by then
recreating the `Makefile` as described previously.

## Usage

### Running other software
PoliRV can only execute a small subset of the RISC-V
instructions. Namely, it can only execute the following
instructions, with their respective formats and opcodes for
reference. More detailed information can be found in the Green
Card of RISC-V, with the `beq` caveat described below.

Instruction | Format | opcode  | funct3 | funct7[5] |
------------|--------|---------|--------|-----------|
lw          | I      | 0000011 | 010    | —         |
sw          | S      | 0100011 | 010    | —         |
beq         | S      | 1100011 | 000    | —         |
add         | R      | 0110011 | 000    | 0         |
sub         | R      | 0110011 | 000    | 1         |
and         | R      | 0110011 | 111    | 0         |
or          | R      | 0110011 | 110    | 0         |

`beq` does **not** follow the RISC-V SB format: the immediate
is packed as in the S format and shifted by 2 bits (rather than
1) before being added to the PC. A standard RISC-V assembler
will therefore not produce correct binaries for this processor.

Therefore, any software must be specifically designed to only
use these instructions. As this processor was developed for
educational purposes, the supplied software was handwritten in
RISC-V Assembly and then manually converted to machine code.
The programs exercise the pipeline (forwarding, load-use stalls
and taken or not-taken branches).

### Copying standalone components
Any component is available for use in other projects
**if and only if** these projects comply with this software's
LICENSE. Most importantly, it is worth noting that your work
**must disclose its full source** if it uses any part
whatsoever of this software.

It is also worth noting this repository is **NOT** meant to
provide ready-made implementations for the course's projects.
Therefore, this software
**cannot be submitted as proof of your own work, and any known attempts to do so will be reported immediately without exception**.

## Repository Structure
Directories were set up according to hdlmake's example setup.
* `github`: media content used in the README;
* `modules`: hardware descriptions of components used to build
    the processor, including the `parameters.vhd` package (word
    widths and which program is loaded for simulation), pipeline
    registers and hazard units under `pipeline/`, and a dummy
    toplevel implementation for testing purposes;
* `simulation`: files related to simulation with GHDL (
    hdlmake's `Manifest.py` and GTKWave configuration files);
* `software`: content of both the instruction memory
    (`rom.dat`) and the data memory (`ram.dat`). Each folder
    holds a pipeline test program (`basic`, `complete`,
    `alu-after-load`, `alu-after-load-2`, `ex-forward-a`,
    `ex-forward-b`, `ex-double-forward`, `mem-double-forward`,
    `forward-data-hazard`, `beq-taken` and `beq-nottaken`);
* `testbenches`: testbenches for hardware descriptions following
    the directory structure of `modules`. Note that all
    components have testbenches with assertions (i.e.
    automatically produce a pass or fail message on execution)
    except for `polilegsc`, `datapath`, `toplevel` (whose
    testbench only runs the simulation for a fixed number of
    cycles) and the components under `pipeline/`.

## References
D. Patterson and J. Hennessy.
*Computer Organization and Design RISC-V Edition: The Hardware Software Interface.*
The Morgan Kaufmann Series in Computer Architecture
and Design. Elsevier Science, 2017.
