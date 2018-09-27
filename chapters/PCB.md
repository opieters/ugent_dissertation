---
title: PCB Schematics and Boards
date:   "2018-09-08"
tags: [Dissertation]
type: "chapter"
author: "Olivier Pieters"
description: "description"
keywords: ["key", "words"]
topics: ["topic 1"]
tags: ["one", "two"]
chapter: A1
---

# Separate Detector and Laser Driver Board Designs

This design is the first prototype. Three different board have been designed. This way, it is easier to test and debug each of the circuits separately. If need be, a single board can be replaced, reducing the cost to replace a circuit if it does not function as intended.

![Detector schematic PCB layout (part 1).](/images/detector1_schematic.svg){#fig:PCB:detector1_schematic}

![Detector board PCB layout (part 1).](/images/detector1_board.svg){#fig:PCB:detector1_board}

![Detector schematic PCB layout (part 2).](/images/detector2_schematic.svg){#fig:PCB:detector2_schematic}

![Detector board PCB layout (part 2).](/images/detector2_board.svg){#fig:PCB:detector2_board}

![Driver schematic PCB layout.](/images/transmitter_schematic.svg){#fig:PCB:transmitter_schematic}

![Driver board PCB layout.](/images/transmitter_board.svg){#fig:PCB:transmitter_board}

# Combined Detector and Laser Driver Board Design with PIC-tail Connector

This board combines both the laser driver circuit and detector into a single board. This makes the interconnections simpler but offers less debugging capabilities. Test-pins have been added to the circuit to more easily test the different circuits. Additionally, the board also features a PIC-tail vertical connector. This allows to plug the board into the Explorer 16 development board.

![Combined driver and detector schematic PCB layout.](/images/all_in_one_schematic.svg){#fig:PCB:all_in_one_schematic}

![Combined driver and detector board PCB layout (part 2).](/images/all_in_one_board.svg){#fig:PCB:all_in_one_board}
