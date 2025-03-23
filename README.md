# ITCash Bank System Design

## 🏗️ System Architecture
```
┌──────────────────────┐       ┌──────────────────────┐
│      Client UI       │       │    Admin Portal      │
│ (Next.js Frontend)   │       │  (Next.js Frontend)  │
└──────────┬───────────┘       └──────────┬───────────┘
           │                              │
           │                              │
           ▼                              ▼
┌───────────────────────────────────────────────────┐
│             API Gateway (Express.js)              │
│               ┌─────────────────────┐             │
│               │   Authentication    │             │
│               │      Service        │             │
│               └─────────────────────┘             │
│                           ▲                       │
│                           │                       │
│ ┌─────────────┐  ┌────────┴─────────┐  ┌─────────────┐
│ │ Banking Core │  │ Transaction Engine │  │ Card Management │
│ │  Service     │  │                  │  │ Service      │
│ └──────┬───────┘  └───────┬─────────┘  └──────┬──────┘
│        │                  │                   │       │
└────────┼──────────────────┼───────────────────┼───────┘
         ▼                  ▼                   ▼
┌───────────────────────────────────────────────────┐
│                PostgreSQL Cluster                │
│         (HA Configuration with Read Replicas)    │
└───────────────────────────────────────────────────┘
```

## 🔄 Core Data Flows
1. **User Authentication Flow**
```
Client → API Gateway → Auth Service → Database
       (JWT Creation)      (Session Management)
```

2. **Money Transfer Flow**
```
Client → API Gateway → Transaction Engine → Banking Core
                          ↓           ↑
                    (Fraud Check)  (Balance Update)
                          ↓
                     Database Log
```

3. **Card Management Flow**
```
Admin → API Gateway → Card Service → Banking Core
                              ↓
                         Card Processor
                              ↓
                          Database Update
```


## 🔐 Security Architecture
```
                   ┌──────────────────────┐
                   │   HSM Integration    │
                   │ (Key Management)     │
                   └──────────┬───────────┘
                              │
┌─────────────┐      ┌────────▼────────┐      ┌─────────────┐
│  Frontend   │──────▶ API Gateway     │──────▶   Backend   │
│ (Next.js)   │◀─────┤ (TLS 1.3)       │◀─────┤ Services    │
└─────────────┘      │ Rate Limiting   │      └──────┬──────┘
                     │ Request Validation            │
                     └────────▲────────┘             │
                              │                      │
                     ┌────────┴────────┐     ┌───────▼───────┐
                     │  Auth Service   │     │   Database    │
                     │ (JWT/OAuth2)    │     │ (AES-256 GCM) │
                     └─────────────────┘     └───────────────┘
```

## Key Design Principles
1. **Financial Integrity**
   - Double-entry bookkeeping system
   - ACID-compliant transaction processing
   - End-of-day reconciliation jobs

2. **Scalability**
   - Horizontal scaling of stateless services
   - Database read replicas for reporting
   - Redis cache for frequently accessed data

3. **Auditability**
   - Immutable transaction records
   - Temporal tables for historical data
   - Blockchain-style hash chaining for critical tables

4. **Fault Tolerance**
   - Circuit breakers in service communication
   - Queue-based retry mechanism for payments
   - Geographic redundancy for disaster recovery

## 🔄 Event-Driven Components
```
User Action → API Gateway → Kafka → [Services]
                              │
                              ├──▶ Fraud Detection
                              ├──▶ Notification Service
                              └──▶ Analytics Engine
```


This design emphasizes financial system fundamentals while maintaining modern cloud-native patterns, without exposing implementation-specific details.
