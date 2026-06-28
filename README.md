# Asynchronous FIFO Design in Verilog

This repository contains a robust, synthesizable implementation of an **Asynchronous FIFO (First-In, First-Out)** memory buffer using Verilog. It is designed to safely transfer data between two distinct, asynchronous clock domains (Read Clock and Write Clock) without encountering metastability issues.

## 🚀 Features
* **Dual Clock Domains:** Completely independent read (`rd_clk`) and write (`wr_clk`) clock management.
* **Metastability Mitigation:** Uses Multi-stage (2-stage) Flip-Flop Synchronizers.
* **Gray Code Pointer Conversion:** Pointers are converted to Gray code before synchronization to ensure only 1 bit changes at a time, preventing erroneous full/empty flag generation.
* **Accurate Flag Generation:** Fully synchronous generation of Full (`wfull`) and Empty (`rempty`) flags.

---

## 📂 Repository Structure

The core Design Under Test (DUT) consists of the following modular files:

* **`asynchfifo_top.v`** – The top-level module integrating the memory array, pointers, and synchronizers.
* **`memory.v`** – The dual-port memory array handling structural read and write operations.
* **`wrptr_full.v`** – Handles write pointer logic, binary-to-gray conversion, and generates the `wfull` flag.
* **`rdptr_empty.v`** – Handles read pointer logic, binary-to-gray conversion, and generates the `rempty` flag.
* **`synchronizer.v`** – The 2-stage flip-flop synchronizer array used to pass Gray-coded pointers across clock domains safely.

---

## 🛠️ Architecture Block Diagram

The design follows the standard Clifford E. Cummings architecture for multi-clock FIFO designs:

```text
       Write Domain (wr_clk)                Read Domain (rd_clk)
     +-----------------------+            +-----------------------+
     |                       |            |                       |
---->| Write Pointer Logic   |--Gray Ptr->| 2-Stage Synchronizer  |
     | (wrptr_full.v)        |            | (synchronizer.v)      |
     +-----------------------+            +-----------------------+
                |                                     |
                |                                 Sync Gray Ptr
                |                                     v
                |                         +-----------------------+
                |                         | Read Pointer Logic    |<----
                |                         | (rdptr_empty.v)       |
                |                         +-----------------------+
                v                                     |
     +-----------------------+                    Gray Ptr
     | Dual-Port Memory      |                        |
---->| Array                 |                        v
     | (memory.v)            |            +-----------------------+
     +-----------------------+            | 2-Stage Synchronizer  |
                |                         | (synchronizer.v)      |
                v                         +-----------------------+
                                                      |
                                                 Sync Gray Ptr
                                                      v
                                            [Used by Write Logic]
