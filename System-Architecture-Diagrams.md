# 📊 Diagrams — เอกสารการออกแบบสถาปัตยกรรมระบบคิว

---

## 1.3 Diagram โครงสร้างข้อมูล (Data Structure)

```mermaid
flowchart LR
    classDef arrayNode fill:#f9d0c4,stroke:#333,stroke-width:2px;
    classDef listNode fill:#d4edda,stroke:#333,stroke-width:1px;
    classDef ptrNode fill:#fff3cd,stroke:#333,stroke-dasharray: 5 5;

    subgraph "Array (Priority Index)"
        A0[Index 0: VIP]:::arrayNode
        A1[Index 1: Normal]:::arrayNode
    end

    subgraph "Linked Lists (Queue State ใน Memory)"
        V1[Node: V-001]:::listNode --> V2[Node: V-002]:::listNode
        V2 --> V_Tail((Tail)):::ptrNode

        N1[Node: N-001]:::listNode --> N2[Node: N-002]:::listNode
        N2 --> N3[Node: N-003]:::listNode
        N3 --> N_Tail((Tail)):::ptrNode
    end

    A0 -- "Head Pointer" --> V1
    A1 -- "Head Pointer" --> N1
```

---

## 2.2 Diagram สถาปัตยกรรมระบบภาพรวม (System Architecture Overview)

```mermaid
flowchart TD
    classDef producer fill:#e1f5fe,stroke:#0288d1;
    classDef broker fill:#fff3e0,stroke:#f57c00;
    classDef consumer fill:#e8f5e9,stroke:#388e3c;
    classDef db fill:#f3e5f5,stroke:#7b1fa2;

    subgraph "Phase 1: Enqueue Node"
        App["Desktop App (Flutter)<br/>- Local SQLite (Offline Buffer)"]:::producer
    end

    subgraph "Phase 2: Message Broker"
        RMQ[("RabbitMQ<br/>(Persistent Queue)")]:::broker
    end

    subgraph "Phase 3: Processing Node"
        Worker["Backend Worker (Go)<br/>- Database Transaction"]:::consumer
        MainDB[(Main Database)]:::db
    end

    App -- "1. Publish (Fire & Forget)" --> RMQ
    RMQ -- "2. Push Message" --> Worker
    Worker -- "3. Execute Business Logic" --> MainDB
```

---

## 3.3 Master Diagram: วัฏจักรการรับมือวิกฤตของฝั่ง Enqueue (Crisis Management Sequence)

```mermaid
sequenceDiagram
    autonumber
    actor Staff as พนักงานหน้าเคาน์เตอร์
    participant App as Desktop App (Flutter)
    participant LocalDB as Local SQLite (ในเครื่อง)
    participant API as Backend (Go API)
    participant MQ as RabbitMQ
    participant Printer as เครื่องพิมพ์

    rect rgb(240, 248, 255)
        Note over Staff, Printer: 🟢 PHASE 1: BOOT & RECOVERY (เมื่อเปิดแอปใหม่ / ไฟเพิ่งมา)
        Staff->>App: เปิดโปรแกรม (Boot)
        activate App
        App->>API: ขอเลขคิวล่าสุด (เช่น V-020)
        alt 🟢 Server ปกติ
            API-->>App: คืนค่า V-020
            App->>LocalDB: อัปเดตข้อมูลในเครื่องให้ตรงกัน
        else 🔴 Server ล่ม / เน็ตล่ม
            API--xApp: Timeout!
            App->>LocalDB: Fallback: อ่านค่าล่าสุดที่จำไว้ในเครื่อง
            LocalDB-->>App: คืนค่า V-020
            Staff->>App: Manager กดยืนยันการใช้ข้อมูล Local
        end
        App-->>Staff: ปลดล็อกหน้าจอ พร้อมให้บริการ
        deactivate App
    end

    rect rgb(255, 250, 240)
        Note over Staff, Printer: 🟡 PHASE 2: NORMAL OPERATION & OFFLINE MODE (ขณะออกคิว)
        Staff->>App: กดปุ่ม "ออกคิว VIP"
        activate App
        App->>LocalDB: บันทึก V-021 ลงเครื่อง (สถานะ: Pending_Sync)
        App->>Printer: พิมพ์สลิป V-021 ให้ลูกค้า (ทำงานทันที!)

        alt 🟢 เน็ตปกติ
            App-)MQ: ยิงข้อมูลเข้า Queue
            MQ-->>App: ส่ง ACK กลับ
            App->>LocalDB: อัปเดตสถานะเป็น "Synced"
        else 🔴 เน็ตหลุด / สายแลนขาด
            App-xMQ: Connection Failed!
            App->>App: เข้าสู่โหมด Offline (ดองข้อมูลไว้ใน LocalDB อย่างปลอดภัย)
        end
        deactivate App
    end

    rect rgb(232, 245, 233)
        Note over Staff, Printer: 🔵 PHASE 3: SELF-HEALING (เยียวยาตัวเองเมื่อเน็ตกลับมา)
        Note over App: ตรวจพบการเชื่อมต่อ (Network Restored)
        activate App
        App->>LocalDB: ดึงข้อมูลที่ยัง Pending_Sync ทั้งหมด
        loop Background Syncing
            App-)MQ: ทยอยส่งข้อมูลที่ตกค้างเข้าระบบ
            MQ-->>App: รับ ACK
            App->>LocalDB: เปลี่ยนเป็น "Synced"
        end
        deactivate App
    end
```

---

## 4. Diagram ภาพรวมระบบครบวงจร (Full System Overview)

```mermaid
flowchart TD
    classDef producer fill:#e1f5fe,stroke:#0288d1,color:#01579b
    classDef broker fill:#fff3e0,stroke:#f57c00,color:#e65100
    classDef consumer fill:#e8f5e9,stroke:#388e3c,color:#1b5e20
    classDef db fill:#f3e5f5,stroke:#7b1fa2,color:#4a148c
    classDef decision fill:#fffde7,stroke:#f9a825,color:#f57f17
    classDef ok fill:#e8f5e9,stroke:#22c55e,color:#1b5e20
    classDef err fill:#ffebee,stroke:#ef4444,color:#b71c1c

    subgraph PROD["🖥️  Phase 1 — Flutter Desktop App (Producer)"]
        UI["Main UI Thread\n• กดออกคิว\n• พิมพ์สลิปทันที"]:::producer
        MEM["Memory Queue\nArray of Linked Lists\n[ VIP idx:0 | Normal idx:1 ]\nEnqueue / Dequeue = O(1)"]:::producer
        SQ["Local SQLite\nOffline Buffer\nPending_Sync ↔ Synced"]:::producer
        ISO["Background Isolate\n(Thread แยก — UI ไม่ค้าง)"]:::producer
    end

    subgraph BROKER["📨  Phase 2 — RabbitMQ (Message Broker)"]
        MQ[("Persistent Queue\nบันทึกลง Disk ทันที\nรองรับ ACK / NACK / Re-queue")]:::broker
    end

    subgraph GOZONE["⚡  Phase 3 — Go Backend (Consumer)"]
        GW["goroutine Worker\nManual ACK Mode"]:::consumer
        IDEM{"Idempotency Key\nเคยประมวลผลแล้ว?"}:::decision
        TX["DB Transaction\nBEGIN → Business Logic → COMMIT"]:::consumer
        RB["ROLLBACK\n(ไม่ส่ง ACK)"]:::err
    end

    subgraph DBZONE["🗄️  Main Database"]
        DB[("queue_id · queue_number\npriority_level · status\ncreated_at")]:::db
    end

    SLIP["🖨️ สลิปลูกค้า\n(ได้ทันที ไม่ต้องรอ Server)"]:::ok
    ACK["✅ ACK\nกลับมายัง App"]:::ok
    DONE["✓ Pipeline Complete\nSQLite → Synced"]:::ok

    UI -->|"Enqueue O(1) tail append"| MEM
    UI -->|"1. บันทึกก่อนเสมอ"| SQ
    UI -->|"2. พิมพ์ทันที"| SLIP

    MEM -->|"Dequeue: VIP ก่อน Normal"| ISO
    SQ -->|"rows ที่ยัง Pending_Sync"| ISO

    ISO -->|"Publish JSON + Idempotency Key\n(Fire & Forget)"| MQ

    MQ -->|"Push Message\n(Unacked)"| GW
    GW -->|"ตรวจสอบ Key"| IDEM

    IDEM -->|"ใช่ — ซ้ำ → Skip\nส่ง ACK ทันที"| ACK
    IDEM -->|"ไม่ใช่ — ประมวลผลต่อ"| TX

    TX -->|"สำเร็จ → COMMIT"| DB
    TX -->|"ล้มเหลว (DB Crash)"| RB
    RB -->|"Re-queue อัตโนมัติ\nWorker ตัวใหม่รับต่อ"| MQ

    DB -->|"COMMIT สำเร็จ\nส่ง ACK"| ACK
    ACK -->|"อัปเดต SQLite"| DONE
```
