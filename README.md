# APB-Protocol
The Advanced Peripheral Bus (APB) is part of the AMBA (Advanced Microcontroller Bus Architecture) protocol family, introduced by ARM. It is a low-cost, low-power interconnect optimized for connecting low-bandwidth peripherals—such as UARTs, timers, GPIOs, SPI, and I2C—to a high-performance system bus (like the Advanced High-performance Bus, AHB, or Advanced Extensible Interface, AXI) via a bridge.

Unlike pipelined, high-speed buses, APB uses a non-pipelined, synchronous protocol with a simplified signal interface. This keeps power consumption and silicon area minimal.

Key Characteristics of APB
Low Power & Low Complexity: Fewer signals mean fewer routing tracks and reduced dynamic power.

Non-Pipelined: Every transfer takes a minimum of two clock cycles (Setup and Access phases).

Unidirectional Signal Path (primarily): Addresses and control signals are driven by the bridge/master.

Bridge Architecture: Peripherals do not connect directly to the main system bus; they sit behind an APB Bridge that translates high-performance bus transactions into APB transactions.

System Signals
PCLK: The master clock driving all APB transfers.

PRESETn: Active-LOW system and peripheral reset.

Bridge (Master) Signals
PADDR: Address bus (up to 32 bits).

PSELx: Slave select line; goes HIGH when a specific peripheral is targeted.

PENABLE: Timing signal that defines the active access phase.

PWRITE: Direction control (HIGH for write, LOW for read).

PWDATA: Write data bus (32 bits).

PSTRB: Byte strobes for sparse writes (AMBA 4).

PPROT: Protection and security type indicator (AMBA 4).

Peripheral (Slave) Signals
PRDATA: Read data bus (32 bits), driven by the slave during reads.

PREADY: Flow control signal; driven LOW by the slave to insert wait states (AMBA 3+).

PSLVERR: Error indicator driven HIGH if a transfer fails (AMBA 3+).

1. IDLE State
This is the default state of the APB.

PSELx is LOW, and PENABLE is LOW.

No data transfer is taking place.

2. SETUP Phase (T1)
When a transfer is required, the bridge enters the SETUP phase.

PADDR, PWRITE, PWDATA (if write), and PSELx are driven to valid levels.

PENABLE remains LOW.

The peripheral must sample the address and control signals on the rising edge of PCLK. The bus stays in this state for only one clock cycle.

3. ACCESS Phase (T2 onwards)
On the next rising clock edge, the bridge asserts PENABLE (HIGH).

PSELx remains HIGH, and address/control signals remain stable.

This phase defines the active window for the peripheral to read or write data.

Wait States (PREADY): If the peripheral cannot complete the transfer immediately, it drives PREADY LOW. The transfer remains in the ACCESS phase, extending for as many cycles (T2, T3, etc.) as needed until the peripheral drives PREADY HIGH.

Advantages and Limitations
Pros:

Extremely easy to design, verify, and interface with simple register blocks.

Low gate-count overhead.

Predictable deterministic timing for low-speed register accesses.

Cons:

Not suitable for high-bandwidth data transfers (e.g., direct memory access bursts or high-speed cache lines) because it lacks native burst-transfer capabilities (though APB extensions like APB burst can be implemented via custom logic, it is non-standard).

Single master per bridge limits throughput compared to multi-master AXI interconnects
