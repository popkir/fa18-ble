# fa18-ble

[![Build Status](https://travis-ci.org/ucberkeley-ee290c/fa18-ble.svg?branch=master)](https://travis-ci.org/ucberkeley-ee290c/fa18-ble)

This Documentation is for Bluetooth Low Energy (BLE) Baseband group work in EE290C @ UC Berkeley 2018 fall semester.
<br><br><br>
### Overview
- Update PacketAssembler to new Chisel standard and connect to new RocketChip 

- Build the Chain of PacketAssembler and insert FIFOs for testing

- Construct C tests to verify the functionality
<br><br><br>

### Working Principle
1. &nbsp;&nbsp; In assembler.scala, the BLE baseband was updated to accommodate to new Chisel standard (wrap PA Block as lazyModule in PAChain). Another improvement is that, in the former implementation, each packet("PABundle") includes *trigger* (1 bit, denotes the beginning of a BLE packet), *data* (8 bits, the contents of BLE packets) *crc_seed* (24 bits), *white_seed* (7 bits), and *done* (1 bit, denotes the end of a BLE packet). Thus each time when transmitting, the effective payload takes up only 8 bits out of 41 and most parts are repetitive for a single BLE packet. We improved the Bundle structure to include only *trigger* and *data*. The transmission is achieved with a FSM which holds 6 stages: idle, preamble, access address, PDU_header, PDU_payload, CRC. Several modifications were done for proper state transitions.
2. &nbsp;&nbsp; In PAChain.scala, we basically integrated a complete chain for PacketAssembler testing. Two FIFOs were inserted on both input and output of the PacketAssembler. The PA bundle is transmitting by AXI4StreamNode. We made diplomatic TL node for regmap and used WriteQueue/ReadQueue to access the testing bundle.
3. &nbsp;&nbsp; In pa.c, we currently hard coded the testing case. The packet is generated by MATLAB code from David Burnett and there are detailed comments about how the packet is contructed. For now, our BLE baseband is transmitting *"SCUM3"*.
4. &nbsp;&nbsp; In top.scala, the PacketAssembler is connected to the RocketChip.
<br><br><br>

### TODO
- PacketDisassembler and the PDAChain

- Add-on features like FEC mentioned in Bluetooth 5 Spec.

- DMA implementation after C tests verifying the functionality
<br><br><br>


### Acknowledgement
Here is our appreciation to Prof. Borivoje Nikolic, Prof. Kristofer Pister and the GSI Paul Rigge for guiding us in this project. Their valuable suggestions and feedback help us move forward. Also the work from last semester's group inspired us greatly and here is their tape-out (https://github.com/tapeout/ble-baseband). Lastly, we would like to thank David Burnett and Rachel Zoll for helping us get on board and explain the former BLE stucture and tests.

