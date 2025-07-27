# Sequence Diagram

```text

User         Frontend App       Backend Server        Fraud Detection Service     Payment Gateway

 |                |                   |                           |                         |
 |-- Add to Cart ->|                  |                           |                         |
 |                |-- POST /checkout ->|                          |                         |
 |                |                   |-- Validate Cart & User -->|                         |
 |                |                   |                           |                         |
 |                |                   |-- Call FDS API ---------->|                         |
 |                |                   |   (Order, User, IP, etc.) |                         |
 |                |                   |<-- ALLOW / BLOCK / FLAG --|                         |
 |                |                   |                           |                         |
 |                |                   |--[If ALLOW] Call Payment  ------------------------->|
 |                |                   |                           |                         |
 |                |                   |<-------- Payment Status ----------------------- ----|
 |                |                   |                          |                          |
 |                |<------ Order Confirmation -------------------|                          |
 |<-- Success ----|                   |                          |                          |

```