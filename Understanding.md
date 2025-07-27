# Understanding: Real-Time Fraud Detection System for E-Commerce

## Objective

Design and build a **high-performance fraud detection system** capable of:
- Detecting **fraudulent transactions** in **real time**.
- Responding and **blocking transactions** within **milliseconds**.
- Scaling across **millions of transactions per day** on a large e-commerce platform.

---

## What is a Fraudulent Transaction?

Fraudulent transactions refer to any unauthorized, malicious, or deceitful purchases that can result in:
- **Financial loss** to the platform or customers.
- **Chargebacks**, refund fraud, or inventory theft.
- **Account breaches** and customer trust issues.

### Common Types:
- Credit card fraud (stolen cards)
- Account takeover
- Friendly fraud (chargebacks)
- Refund/return abuse
- Synthetic identity fraud

---

## Key Requirements

### Functional Requirements
- Analyze each transaction in **real-time**.
- Assign a **fraud risk score or classification (fraud/non-fraud)**.
- Block or flag suspicious transactions immediately.
- Log and alert fraud events.
- Provide feedback loop for improving the model.

### Non-Functional Requirements
- **Low Latency**: Response within milliseconds (ideally < 100ms).
- **Scalability**: Handle peak loads (e.g., Black Friday traffic).
- **High Availability**: 24/7 with failover support.
- **Extensibility**: Pluggable model support for ML/heuristics.
- **Security**: Ensure data privacy and protection of sensitive info.

---

## High-Level Architecture

```plaintext
+---------------------+
|   E-commerce App    |
+---------+-----------+
          |
          v
+----------------------+
|  Fraud Detection API |
+---------+------------+
          |
          v
+-------------------------------+
|   Real-time Fraud Decisioning |
|           Engine              |
| - Rule-based filters          |
| - ML models                   |
+---------------+---------------+
                |
                v
       +------------------+
       |   Feature Store  |
       | + Transactional  |
       | + Behavioral     |
       | + Device/IP info |
       +------------------+
                |
                v
     +----------------------+
     |   Decision Outcome   |
     | - Approve            |
     | - Flag for review    |
     | - Block transaction  |
     +----------------------+

```
---

## Approach

### **Hybrid Approach**
- Rules for immediate red flags.
- ML for adaptive detection of complex patterns.

### 1. **Rule-Based Engine**
- Start with a set of fraud rules (e.g., high amount + mismatch address).
- Fast and explainable, but less adaptive.

### 2. **Machine Learning Model**
- Supervised model trained on historical transactions.
- Real-time scoring based on features like:
    - Transaction amount
    - IP, device fingerprint
    - User behavior patterns
    - Velocity checks
---

## Core Domain Entities (w.r.t fraud detection system)

| Entity              | Attributes                                      |
|---------------------|-------------------------------------------------|
| **Transaction**     | `transactionId`, `userId`, `amount`, `status`, `timestamp` |
| **User**            | `userId`, `email`, `accountStatus`             |
| **Device**          | `deviceId`, `ipAddress`                        |
| **Address**         | `type`, `fullAddress`                          |
| **OrderItem**       | `itemId`, `quantity`                           |
| **FraudCheckRequest** | `transactionId`, `userId`, `orderItems`     |
| **FraudDecision**   | `transactionId`, `decision`                    |
| **FraudAlert**      | `alertId`, `transactionId`, `status`           |



## Other E-Commerce Core Entities

| Entity         | Attributes                                                                 |
|----------------|----------------------------------------------------------------------------|
| **Product**    | `productId`, `name`, `description`, `price`, `category`, `availabilityStatus` |
| **Inventory**  | `inventoryId`, `productId`, `quantityAvailable`, `warehouseLocation`       |
| **Cart**       | `cartId`, `userId`, `items`                                                |
| **CartItem**   | `itemId`, `productId`, `quantity`                                          |
| **Order**      | `orderId`, `userId`, `orderItems`, `totalAmount`, `status`                |
| **Payment**    | `paymentId`, `orderId`, `amount`, `method`, `status`                      |
| **Shipment**   | `shipmentId`, `orderId`, `carrier`, `trackingNumber`, `status`            |
| **Review**     | `reviewId`, `productId`, `userId`, `rating`, `comment`                    |
| **Wishlist**   | `wishlistId`, `userId`, `items`                                            |
| **Category**   | `categoryId`, `name`, `parentCategoryId`                                   |


---

## Technology Stack

| Layer                | Tools/Tech                                          |
|---------------------|-----------------------------------------------------|
| Ingestion            | Kafka                                |
| Real-Time Scoring    | Java/Spring Boot / Node.js / Python FastAPI         |
| Rule Engine          | Drools / Custom DSL                                 |
| ML Model Deployment  | TensorFlow Serving / Seldon / AWS SageMaker Endpoint |
| Feature Store        | Redis                                               |
| Monitoring/Alerts    | Prometheus / Grafana / ELK                          |

---

## Challenges & Considerations

- **Latency trade-offs** (accuracy vs speed)
- **Cold start for new users** (lack of history)
- **Model drift & feedback loop** (continuous learning)
- **Data privacy regulations** (GDPR/PCI compliance)
- **False positives** (impacting good customers)

---

## How will I keep latency low (Performance optimization)?
1. Use a Fast In-Memory Feature Store
2. Deploy ML Model as a Low-Latency Microservice
3. Async + Sync Mode Hybrid Design
   - **Synchronous**: For high-risk operations (e.g., payment), the fraud system is called before confirming the transaction.
   - **Asynchronous**: For lower-risk steps (like account creation or item addition), fraud checks can run post-event, flagging for review.
4. Precompute Risk Signals / Features
5. Risk Scoring Instead of Rule Evaluation



