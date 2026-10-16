# Wired Communication


### Pin Table
| Signal | GPIO Used | Board`s Label |
|---|---|---|
| UART TX | 17 |	D17 |
| UART RX | 16 |	D16 |
| I²C SDA |	21 |	D21 |
| I²C SCL |	22 |	D22 |
| SPI SCK	| 18 |	D18 |
| SPI MOSI	| 23 |	D23 |
| SPI MISO	| 19 |	D19 |
| SPI CS | 	27 |	D27 |

### Wiring Diagram
<img width="4096" height="3072" alt="image" src="https://github.com/user-attachments/assets/e3c1eed5-acae-4844-a9e0-bdbaa463f3ba" />

### Serial Monitor output

uart loopback
<img width="1920" height="1080" alt="uartloopback" src="https://github.com/user-attachments/assets/794426f0-fe1c-4ecf-9668-831ce43ea581" />

i²c scanner
<img width="1920" height="1080" alt="i2c_scanner" src="https://github.com/user-attachments/assets/a682511f-2ad2-43cc-8115-95d59c7d7e74" />

spi loopback
<img width="1920" height="1080" alt="spi_lookback" src="https://github.com/user-attachments/assets/617f9985-3e0a-4a10-a94c-b2d4a07bdc43" />

### Expected vs Observed table
| Test | Expected | Observed |
| :--- | :--- | :--- |
| UART with jumper | Prints `PING` every 1 second | Prints `PING` every 1 second |
| UART without jumper | Nothing prints | Nothing prints |
| I²C with module | `Found device at 0x..` | `Found device at 0x..` |
| I²C without module | `No I2C devices found` | `No I2C devices found` |
| SPI with jumper | `Sent 0xA5, received 0xA5` | `Sent 0xA5, received 0xA5` |
| SPI without jumper | Received not `A5` (e.g., `FF` or `0`) | Received not `A5` (e.g., `FF` or `0`) |
| Sketch at 9600, monitor at 115200 | Garbled characters | Garbled characters |
| Sketch at 9600, monitor at 9600 | Clear `PING` | Clear `PING` |

### Questions
1. Why does UART need both sides to use the same baud rate, while SPI uses a clock line?
UART does not have a clock signal, so the receiver samples bits based on its own timing, which is why both ends must use the exact same baud rate. SPI uses an SCK line from the master that tells the slave when to sample, so matching timing settings are not required.

2. Why can two devices with the same address not share one I²C bus?
The master selects a device using its address, so if two devices share the same address, both will respond and drive the SDA line at the same time. This corrupts the data and makes it impossible to distinguish which device is being communicated with.

3. Why is CS set HIGH between SPI transfers?
Setting CS HIGH deselects the slave, causing it to ignore the bus and reset its transfer state. Toggling CS LOW and HIGH marks the start and end of each transfer, allowing multiple slaves to share the same bus.
