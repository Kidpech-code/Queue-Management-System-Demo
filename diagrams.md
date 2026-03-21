# 📊 Queue System — Diagrams for Eraser (Native DSL)

คัดลอก Code แต่ละบล็อกไปวางใน Eraser ได้เลยครับ (เป็น Eraser Native Syntax แล้ว ไม่เกิด error แน่นอน)
เลือกประเภท Diagram ตามที่ระบุไว้ในแต่ละหัวข้อ

---

## 1. โครงสร้างข้อมูล (Data Structure)

**Eraser type: Flow Chart**

```
Array {
  A0 [label: "Index 0 - VIP"]
  A1 [label: "Index 1 - Normal"]
}

Linked Lists {
  V1 [label: "V-001"]
  V2 [label: "V-002"]
  VTail [label: "Tail 1"]
  N1 [label: "N-001"]
  N2 [label: "N-002"]
  N3 [label: "N-003"]
  NTail [label: "Tail 2"]
}

V1 > V2
V2 > VTail
N1 > N2
N2 > N3
N3 > NTail
A0 > V1: Head Pointer
A1 > N1: Head Pointer
```

---

## 2. สถาปัตยกรรมระบบภาพรวม (System Architecture)

**Eraser type: Flow Chart**

```
P1 [label: "Phase 1 Enqueue Node"] {
  App [label: "Desktop App Flutter / Local SQLite"]
}

P2 [label: "Phase 2 Message Broker"] {
  RMQ [label: "RabbitMQ", icon: rabbitmq]
}

P3 [label: "Phase 3 Processing Node"] {
  Worker [label: "Backend Worker Go"]
  MainDB [label: "Main Database", icon: database]
}

App > RMQ: 1. Publish
RMQ > Worker: 2. Push Message
Worker > MainDB: 3. Execute Business Logic
```

---

## 3. วัฏจักรการรับมือวิกฤต (Crisis Management Sequence)

**Eraser type: Sequence**

```
Staff [icon: user]
App [label: "Desktop App", icon: monitor]
LocalDB [label: "Local SQLite", icon: database]
API [label: "Backend Go API", icon: server]
MQ [label: "RabbitMQ", icon: queue]
Printer [label: "Printer", icon: printer]

// PHASE 1 BOOT and RECOVERY
Staff > App: Boot
App > API: 1. Request latest queue number
API > App: Return V020, Server OK
App > LocalDB: Sync local data
API > App: Timeout, Server Down
App > LocalDB: Fallback read local data
LocalDB > App: Return V020
Staff > App: Manager confirm local data
App > Staff: Unlock screen

// PHASE 2 NORMAL and OFFLINE MODE
Staff > App: Press Issue VIP Queue
App > LocalDB: 2. Save V021 as Pending Sync
App > Printer: 3. Print slip V021
App > MQ: 4a. Publish to Queue
MQ > App: 5a. ACK
App > LocalDB: 6a. Update to Synced
App > MQ: 4b. Connection Failed
App > LocalDB: 5b. Enter Offline Mode

// PHASE 3 SELF HEALING
App > MQ: 7. Network Restored
App > LocalDB: 8. Get all Pending Sync rows
App > MQ: 9. Send pending data
MQ > App: 10. ACK
App > LocalDB: 11. Update to Synced
```

---

## 4. ภาพรวมระบบครบวงจร (Full System Overview)

**Eraser type: Flow Chart**

```
PROD [label: "Phase 1 Flutter Desktop App"] {
  UI [label: "Main UI Thread"]
  MEM [label: "Memory Queue"]
  SQ [label: "Local SQLite", icon: database]
  ISO [label: "Background Isolate"]
}

BROKER [label: "Phase 2 RabbitMQ"] {
  MQ [label: "Persistent Queue", icon: rabbitmq]
}

GOZONE [label: "Phase 3 Go Backend"] {
  GW [label: "goroutine Worker"]
  IDEM [label: "Idempotency Key?", shape: diamond]
  TX [label: "DB Transaction"]
  RB [label: "ROLLBACK"]
}

DBZONE [label: "Main Database"] {
  DB [label: "Main DB Table", icon: database]
}

SLIP [label: "Print Slip"]
ACK [label: "ACK back to App"]
DONE [label: "Pipeline Complete"]

UI > MEM: Enqueue O1
UI > SQ: 1. Save to SQLite first
UI > SLIP: 2. Print immediately

MEM > ISO: Dequeue
SQ > ISO: Pending Sync rows

ISO > MQ: Publish JSON and Key

MQ > GW: Push Message Unacked
GW > IDEM

IDEM > ACK: Duplicate, Skip
IDEM > TX: New, Process

TX > DB: Success COMMIT
TX > RB: Fail DB Crash
RB > MQ: Requeue auto

DB > ACK: COMMIT then ACK
ACK > DONE: Update SQLite
```

---

## 5. Entity Relationship — Database Schema

**Eraser type: Entity Relationship**

```
LOCAL_SQLITE {
  queue_number string [pk]
  status string
  idempotency_key string
  created_at datetime
}

RABBITMQ_MESSAGE {
  queue_number string
  priority_level int
  idempotency_key string
  published_at datetime
  requeued boolean
}

MAIN_DATABASE {
  queue_id uuid [pk]
  queue_number string
  priority_level int
  status string
  created_at datetime
}

IDEMPOTENCY_REGISTRY {
  idempotency_key string [pk]
  processed_at datetime
}

LOCAL_SQLITE > RABBITMQ_MESSAGE
RABBITMQ_MESSAGE > MAIN_DATABASE
MAIN_DATABASE - IDEMPOTENCY_REGISTRY
RABBITMQ_MESSAGE > IDEMPOTENCY_REGISTRY
```
