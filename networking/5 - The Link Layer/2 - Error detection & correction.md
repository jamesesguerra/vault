Like the L4, L2 tries to detect if errors occurred in the bits by including **error detection and correction bits (EDC)**. At the receiver side, the has the job of determining whether the payload of the frame is the same using the EDC. There's also an important difference between error detection and error occurrence as there are instances where the L2 cannot detect an error as more sophisticated error detection techniques incur larger overheads.

![[bit-errors.png]]

#### Parity Checks
The simplest form of error detection is the use of a single parity bit. More sophisticated schemes are needed since the percentage of errors being detected using parity bits is high.

#### Checksumming
Checksumming is another technique similar to parity checks, except it groups the bits into a set of bytes and computes sums using 1s complement from there. They are also not error prone.

#### CRC
Cyclic Redundancy Check (CRC) codes are the least error prone as they can detect burst errors, which is something the previous 2 methods can't.