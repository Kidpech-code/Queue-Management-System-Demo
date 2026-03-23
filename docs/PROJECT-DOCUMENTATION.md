# 📖 เอกสารโปรเจ็ก Queue Management System Demo

**ระบบจัดการคิว — Queue System Architecture Demo**
**เวอร์ชัน:** 3.0 | **ผู้เขียนเอกสาร:** Kidpech P.

---

## สารบัญ

1. [โครงสร้างโปรเจ็ก (Project Structure)](#1-โครงสร้างโปรเจ็ก-project-structure)
2. [Tech Stack](#2-tech-stack)
3. [กระบวนการทำงาน วิธีการ และคู่มือใช้งาน](#3-กระบวนการทำงาน-วิธีการ-และคู่มือใช้งาน)

---

## 1. โครงสร้างโปรเจ็ก (Project Structure)

### 1.1 ภาพรวมไฟล์ในโปรเจ็ก

```
Queue-Management-System-Demo/
├── queue-system-dashboard.html              ← ✅ ไฟล์หลัก (เวอร์ชันภาษาไทย)
├── queue-system-dashboard.backup.html       ← 📦 ไฟล์สำรอง (เวอร์ชันภาษาอังกฤษ)
├── System-Architecture-Design-Document      ← 📄 เอกสารออกแบบสถาปัตยกรรมระบบ
└── .git/                                    ← 🔧 Git version control
```

### 1.2 รายละเอียดไฟล์แต่ละตัว

| ไฟล์                                  | ขนาด (บรรทัด) | คำอธิบาย                                                                                                   |
| ------------------------------------- | :-----------: | ---------------------------------------------------------------------------------------------------------- |
| `queue-system-dashboard.html`         |     1,191     | **ไฟล์หลัก** — Demo Dashboard เป็นภาษาไทย พร้อม Pipeline Indicator บน Header, UI สวยงามด้วย Noto Sans Thai |
| `queue-system-dashboard.backup.html`  |     1,088     | **ไฟล์สำรอง** — เวอร์ชันภาษาอังกฤษ (Architecture Debugger v3) สำหรับนักพัฒนาต่างชาติ                       |
| `System-Architecture-Design-Document` |      207      | **เอกสารออกแบบ** — อธิบายโครงสร้างข้อมูล, สถาปัตยกรรม, กลไกรับมือวิกฤต พร้อม Mermaid Diagram               |

### 1.3 สถาปัตยกรรมไฟล์ (Single-File Architecture)

โปรเจ็กนี้ใช้แนวคิด **Self-Contained Single HTML File** — ทุกอย่างรวมในไฟล์ `.html` เดียว ไม่ต้องติดตั้ง ไม่ต้อง build เปิดใน Browser ได้ทันที

```
queue-system-dashboard.html
├── <head>
│   ├── CSS Styles (~196 บรรทัด)       ← Dark theme UI, Animations, Grid Layout
│   └── Tailwind CDN                    ← Utility classes เสริม
├── <body>
│   ├── Header Section                  ← Logo, Pipeline Progress Bar, Status
│   ├── Main Grid (4 คอลัมน์ × 2 แถว)
│   │   ├── Col 1, Row 1: Memory Queue Panel        ← แสดง Array[2] of Linked Lists
│   │   ├── Col 2, Row 1: Flutter Producer Panel     ← UI Thread, SQLite, Background Isolate
│   │   ├── Col 3, Row 1: RabbitMQ + Go Worker Panel ← Broker + Consumer
│   │   ├── Col 4, Row 1-2: Control Panel           ← ปุ่มควบคุม, Breakpoints, Chaos
│   │   ├── Col 1, Row 2: Main Database Panel        ← ตารางฐานข้อมูลหลัก
│   │   └── Col 2-3, Row 2: Event Console            ← บันทึกเหตุการณ์ Real-time
│   └── Animated Elements               ← Packet animation, Slip tooltip
└── <script>
    ├── System State (S object)          ← Global state management
    ├── Utility Functions                ← uuid(), sleep(), esc()
    ├── Async State Machine              ← pause(), resume(), stepOver()
    ├── Console Logger (clog)            ← XSS-safe event logging
    ├── Render Functions                 ← renderMem(), renderSQ(), renderBR(), renderDB()
    ├── Main Pipeline (processQueue)     ← 6-Phase async pipeline
    ├── Drain/Dequeue Logic              ← Priority-based dequeue
    ├── Background Sync                  ← Offline-First self-healing
    ├── Public Actions                   ← addQueue(), toggleNet(), chaos*()
    └── Pipeline Indicator               ← setPipeline(), resetPipeline()
```

### 1.4 ความแตกต่างระหว่าง 2 เวอร์ชัน

| คุณสมบัติ        | `queue-system-dashboard.html` (หลัก) | `queue-system-dashboard.backup.html` (สำรอง) |
| ---------------- | ------------------------------------ | -------------------------------------------- |
| **ภาษา UI**      | ภาษาไทย                              | ภาษาอังกฤษ                                   |
| **ฟอนต์**        | Noto Sans Thai + JetBrains Mono      | JetBrains Mono (Monospace only)              |
| **Pipeline Bar** | ✅ มี (6 ขั้นตอนบน Header)           | ❌ ไม่มี                                     |
| **สีธีม**        | `#0f172a` (Slate 950)                | `#0f1117` (เข้มกว่า)                         |
| **ไอคอน**        | Emoji-based (🏆, 📝, 📱)             | Unicode symbols (▣, ◈, ⬡)                    |
| **Logic/JS**     | เหมือนกัน (มีเพิ่ม setPipeline)      | เหมือนกัน                                    |

---

## 2. Tech Stack

### 2.1 ภาพรวม Technology Stack

```
┌──────────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                        │
│  ┌────────────┐  ┌──────────────┐  ┌───────────────────┐     │
│  │   HTML5    │  │  CSS3 Custom │  │  Tailwind CSS     │     │
│  │  (Layout)  │  │  (Dark Theme)│  │  (CDN / Utility)  │     │
│  └────────────┘  └──────────────┘  └───────────────────┘     │
├──────────────────────────────────────────────────────────────┤
│                    APPLICATION LAYER                         │
│  ┌────────────────────────────────────────────────────────┐  │
│  │              Vanilla JavaScript (ES2017+)              │  │
│  │  • Async/Await State Machine                           │  │
│  │  • DOM Manipulation (createElement API)                │  │
│  │  • CSS Animation + JS Transition Control               │  │
│  └────────────────────────────────────────────────────────┘  │
├──────────────────────────────────────────────────────────────┤
│              SIMULATED BACKEND ARCHITECTURE                  │
│  ┌──────────┐  ┌───────────┐  ┌──────────┐  ┌───────────┐    │
│  │ Flutter   │  │  SQLite   │  │ RabbitMQ │  │ Go Worker │   │
│  │(Producer) │  │ (Buffer)  │  │ (Broker) │  │(Consumer) │   │
│  └──────────┘  └───────────┘  └──────────┘  └───────────┘    │
│  ┌──────────────────────────────────────────────────────────┐│
│  │                    Main Database                         ││
│  └──────────────────────────────────────────────────────────┘│
└──────────────────────────────────────────────────────────────┘
```

### 2.2 เทคโนโลยีที่ใช้จริงในโปรเจ็ก (Client-Side Demo)

| เทคโนโลยี        | เวอร์ชัน/แหล่งที่มา              | หน้าที่                                                                |
| ---------------- | -------------------------------- | ---------------------------------------------------------------------- |
| **HTML5**        | -                                | โครงสร้างหน้าเว็บ, Semantic layout                                     |
| **CSS3**         | Custom + CSS Variables           | Dark theme, Grid layout 4×2, Animations (keyframes), Scrollbar styling |
| **Tailwind CSS** | CDN (`cdn.tailwindcss.com`)      | Utility classes เสริม (ml-auto, flex, items-center)                    |
| **JavaScript**   | ES2017+ (Vanilla)                | Application logic ทั้งหมด, Async/Await, DOM API                        |
| **Google Fonts** | Noto Sans Thai (400,600,700,800) | ฟอนต์ภาษาไทย (เวอร์ชันหลักเท่านั้น)                                    |

### 2.3 เทคโนโลยีที่จำลอง (Simulated Architecture)

โปรเจ็กนี้เป็น **Interactive Architecture Demo** ที่จำลองระบบ Production ทั้งหมดใน Browser:

| Component ที่จำลอง      | เทคโนโลยีจริง             | สิ่งที่จำลองใน Demo                                              |
| ----------------------- | ------------------------- | ---------------------------------------------------------------- |
| **Flutter Desktop App** | Flutter + Dart            | Panel "แอปหน้าเคาน์เตอร์" — UI Thread, Queue Counter             |
| **SQLite (Local DB)**   | SQLite Database           | `S.sqlite[]` array — ตาราง Local database ในเครื่อง              |
| **Background Isolate**  | Dart Isolate (Thread)     | `isolateLog()` + `triggerBgSync()` — Background Sync mechanism   |
| **RabbitMQ**            | AMQP 0-9-1 Message Broker | `S.broker[]` array — Message queue, Persistent storage, ACK/NACK |
| **Go Backend Worker**   | Go + Goroutines           | Transaction block UI (BEGIN → CHECK → LOGIC → COMMIT/ROLLBACK)   |
| **Main Database**       | PostgreSQL / MySQL        | `S.db[]` array — Main relational database                        |
| **Idempotency Key**     | UUID v4                   | `S.processedKeys` Set — ป้องกันการประมวลผลซ้ำ                    |
| **Network Layer**       | TCP/IP + TLS              | `S.net` boolean — Toggle online/offline                          |

### 2.4 Design Patterns & Techniques ที่ใช้

| Pattern / Technique     | ที่ใช้                                  | รายละเอียด                                                         |
| ----------------------- | --------------------------------------- | ------------------------------------------------------------------ |
| **Async State Machine** | `pause()`, `resumeExec()`, `stepOver()` | Promise-based breakpoint system, ใช้ `_resolver` เป็น continuation |
| **State Management**    | `const S = {...}`                       | Single source of truth — Global state object                       |
| **Observer Pattern**    | `renderMem()`, `renderSQ()`, etc.       | เมื่อ State เปลี่ยน → เรียก render function อัปเดต UI              |
| **XSS Prevention**      | `esc()` function                        | ใช้ `textContent` แทน `innerHTML` สำหรับ dynamic data              |
| **Idempotency**         | `S.processedKeys` (Set)                 | ป้องกันการประมวลผลซ้ำด้วย UUID-based key                           |
| **Priority Queue**      | Array[2] of Arrays                      | VIP (index 0) dequeue ก่อน Normal (index 1) เสมอ                   |
| **Offline-First**       | `triggerBgSync()`                       | บันทึกใน SQLite ก่อน → Sync ขึ้น Server ทีหลัง                     |
| **Manual ACK**          | Transaction block                       | COMMIT สำเร็จ → ส่ง ACK / ล้มเหลว → ROLLBACK + Re-queue            |
| **Chaos Engineering**   | `chaosDbCrash()`, `chaosGhost()`        | จำลองวิกฤตเพื่อพิสูจน์ความทนทานของระบบ                             |

### 2.5 CSS Architecture

```
CSS Custom Properties (Variables)
├── --bg         #0f172a    (พื้นหลังหลัก)
├── --panel      #1e293b    (พื้นหลัง Panel)
├── --border     #334155    (ขอบ)
├── --dim        #94a3b8    (ข้อความเทา)
├── --vip        #f59e0b    (สีส้ม — VIP)
├── --norm       #3b82f6    (สีน้ำเงิน — Normal)
├── --ok         #22c55e    (สีเขียว — สำเร็จ)
├── --warn       #f97316    (สีส้มเตือน)
├── --proc       #a855f7    (สีม่วง — กำลังทำ)
├── --done       #10b981    (สีเขียว — เสร็จ)
├── --broker     #ec4899    (สีชมพู — RabbitMQ)
├── --go         #06b6d4    (สีฟ้า — Go Worker)
└── --err        #ef4444    (สีแดง — ข้อผิดพลาด)
```

---

## 3. กระบวนการทำงาน วิธีการ และคู่มือใช้งาน

### 3.1 วิธีเปิดใช้งาน

#### ขั้นตอนที่ 1: เปิดไฟล์ใน Browser

```bash
# วิธีที่ 1: ดับเบิลคลิกไฟล์
เปิด Finder → ดับเบิลคลิก queue-system-dashboard.html

# วิธีที่ 2: เปิดผ่าน Terminal
open queue-system-dashboard.html        # macOS
xdg-open queue-system-dashboard.html    # Linux
start queue-system-dashboard.html       # Windows
```

> ⚠️ **ไม่ต้องติดตั้งอะไรเพิ่ม** — ไม่ต้อง npm install, ไม่ต้อง server, ไม่ต้อง build
> แค่เปิดไฟล์ HTML ใน Browser ก็ใช้งานได้ทันที (ต้องเชื่อมต่ออินเทอร์เน็ตเพื่อโหลด Tailwind CDN)

#### ขั้นตอนที่ 2: ทำความรู้จักหน้าจอ

หน้าจอแบ่งเป็น **Grid Layout 4 คอลัมน์ × 2 แถว**:

```
┌─────────────────┬───────────────────┬─────────────────────┬──────────────┐
│                 │                   │  RabbitMQ (Broker)  │              │
│  คิวในหน่วย       │  แอปหน้าเคาน์เตอร์   │─────────────────────│  แผงควบคุม    │
│  ความจำ         │  (Flutter Producer) │  Go Worker        │  (Control    │
│  (Memory Queue) │                   │  (Consumer)         │   Panel)     │
├─────────────────┼───────────────────┴─────────────────────┤              │
│  ฐานข้อมูลหลัก     │  บันทึกเหตุการณ์ (Event Log Console)        │              │
│  (Main DB)      │                                         │              │
└─────────────────┴─────────────────────────────────────────┴──────────────┘
```

---

### 3.2 การทำงานทีละขั้น: Pipeline 6 ขั้นตอน

เมื่อกดปุ่มเพิ่มคิว ระบบจะทำงานตาม Pipeline 6 ขั้นตอนนี้:

```
ขั้น 1         ขั้น 2          ขั้น 3         ขั้น 4          ขั้น 5         ขั้น 6
กดคิว  ──→  บันทึก SQLite  ──→  ส่ง Broker  ──→  ประมวลผล  ──→  บันทึก DB  ──→  เสร็จ ✓
```

#### 📍 ขั้นตอนที่ 1: กดคิว (Enqueue)

**สิ่งที่เกิดขึ้น:**

1. ผู้ใช้กดปุ่ม `[+ เพิ่มคิว VIP]` หรือ `[+ เพิ่มคิวปกติ]`
2. ระบบสร้างเลขคิวอัตโนมัติ (V-001, V-002... หรือ N-001, N-002...)
3. สร้าง **Idempotency Key** (UUID) เพื่อป้องกันการซ้ำ
4. เพิ่ม Node เข้าใน Memory Queue ตามลำดับความสำคัญ

**ทำไมถึงออกแบบแบบนี้:**

- VIP จะอยู่ใน Array Index 0, ปกติอยู่ Index 1
- ระบบดึงคิวจาก VIP ก่อนเสมอ (Priority Queue)
- ทั้ง Enqueue และ Dequeue ทำงาน O(1) — เร็วมาก

**สิ่งที่เห็นบนจอ:**

- Panel **"คิวในหน่วยความจำ"** จะแสดง Node ใหม่ที่ TAIL
- Node แสดง HEAD → [Node] → [Node] → ... → TAIL → ∅

---

#### 📍 ขั้นตอนที่ 2: บันทึก SQLite

**สิ่งที่เกิดขึ้น:**

1. บันทึกข้อมูลคิวลง **Local SQLite** ในเครื่อง (สถานะ: `Pending_Sync`)
2. อัปเดตตัวนับคิว (VIP / ปกติ)
3. **พิมพ์สลิปทันที** — ลูกค้าได้สลิปโดยไม่ต้องรอ Server

**ทำไมถึงออกแบบแบบนี้:**

- **Offline-First Architecture** — บันทึกในเครื่องก่อน → Sync ทีหลัง
- ลูกค้าไม่ต้องรอ Server ตอบกลับ
- แม้เน็ตหลุด ก็ออกคิวได้ตามปกติ

**สิ่งที่เห็นบนจอ:**

- ตาราง **"ฐานข้อมูลในเครื่อง (SQLite)"** จะแสดงแถวใหม่ สถานะ `Pending_Sync` (สีส้ม)
- Popup **"🖨️ พิมพ์สลิปแล้ว!"** จะปรากฏที่ Panel Flutter
- ตัวเลข "คิวล่าสุด" จะอัปเดต

---

#### 📍 ขั้นตอนที่ 3: ส่ง Broker (Network Transit)

**สิ่งที่เกิดขึ้น:**

1. **Background Isolate** (Thread แยก) เริ่มทำงาน — UI ไม่ค้าง
2. ส่ง Payload (JSON) จาก Flutter → RabbitMQ ผ่านเครือข่าย
3. RabbitMQ รับข้อความและ**บันทึกลง Disk ทันที** (Persistent)

**ทำไมถึงออกแบบแบบนี้:**

- Sync ทำใน Background Thread → Main UI Thread ไม่ถูกบล็อก
- RabbitMQ เก็บข้อมูลใน Disk → แม้ Broker restart ก็ไม่หาย
- ใช้ **Fire & Forget** pattern — ไม่ต้องรอ response

**สิ่งที่เห็นบนจอ:**

- **Animation** — กล่องข้อมูลเคลื่อนจาก Flutter Panel → Broker Panel
- Panel **"ตัวจัดการคิวกลาง (RabbitMQ)"** จะแสดง Message Card ใหม่
- สถานะ Background Isolate เปลี่ยนเป็น "Syncing..."
- ตัวเลข READY ของ Broker เพิ่มขึ้น

---

#### 📍 ขั้นตอนที่ 4: ประมวลผล (Go Worker)

**สิ่งที่เกิดขึ้น:**

1. Go Worker ดึงข้อความจาก RabbitMQ (ตัวเลข UNACKED เพิ่มขึ้น)
2. เปิด **Database Transaction** (`BEGIN`)
3. ตรวจสอบ **Idempotency Key** — เคยทำไปแล้วหรือยัง?
4. ทำ **Business Logic** (ประมวลผลคิว)
5. `COMMIT` → ส่ง `ACK` ยืนยันให้ RabbitMQ

**ทำไมถึงออกแบบแบบนี้:**

- **Manual ACK** — จะส่ง ACK ก็ต่อเมื่อ COMMIT สำเร็จเท่านั้น
- หาก DB ล่มกลางคัน → `ROLLBACK` + ไม่ส่ง ACK → RabbitMQ คืนคิวอัตโนมัติ
- Idempotency Key ป้องกันการทำซ้ำ (Exactly-once semantics)

**สิ่งที่เห็นบนจอ:**

- Panel **"ตัวประมวลผล (Go Worker)"** จะแสดง Transaction steps:
  - ◉ เปิด Transaction → เขียว (active)
  - 🔑 ตรวจ Idempotency Key → เขียว (active)
  - ⚙ ประมวลผล Business Logic → เขียว (active)
  - ✓ COMMIT → ส่ง ACK → เขียว (active)
- Progress Bar ค่อยๆ เติมจาก 0% → 100%

---

#### 📍 ขั้นตอนที่ 5: บันทึก DB (Main Database)

**สิ่งที่เกิดขึ้น:**

1. COMMIT ข้อมูลลงฐานข้อมูลหลัก (Main DB)
2. ลบ Message ออกจาก RabbitMQ (READY ลดลง)
3. ส่ง **ACK กลับ**ไปยัง Flutter App

**สิ่งที่เห็นบนจอ:**

- Panel **"ฐานข้อมูลหลัก"** จะแสดงแถวใหม่ สถานะ `processing` → `completed`
- **Animation** — กล่อง ACK เคลื่อนจาก Broker Panel → Flutter Panel
- ตัวเลข READY ของ Broker ลดลง

---

#### 📍 ขั้นตอนที่ 6: เสร็จ ✓

**สิ่งที่เกิดขึ้น:**

1. Flutter App ได้รับ ACK
2. อัปเดต SQLite จาก `Pending_Sync` → `Synced`
3. ลบ Node ออกจาก Memory Queue (Dequeue)
4. Pipeline เสร็จสมบูรณ์

**สิ่งที่เห็นบนจอ:**

- ตาราง SQLite: สถานะเปลี่ยนเป็น `Synced` (สีเขียว)
- Memory Queue: Node ถูกลบออก
- Pipeline Bar บน Header แสดงเครื่องหมาย ✓ ทุกขั้นตอน
- Event Log แสดง "✅ Pipeline complete"

---

### 3.3 คู่มือใช้งานแผงควบคุม (Control Panel)

#### 🎮 ส่วนที่ 1: เพิ่มคิวใหม่

| ปุ่ม                  | การทำงาน                                                   |
| --------------------- | ---------------------------------------------------------- |
| **🏆 + เพิ่มคิว VIP** | สร้างคิว VIP (V-001, V-002, ...) — เลขจะเพิ่มขึ้นอัตโนมัติ |
| **📝 + เพิ่มคิวปกติ** | สร้างคิวปกติ (N-001, N-002, ...) — เลขจะเพิ่มขึ้นอัตโนมัติ |

> 💡 **เคล็ดลับ:** กดเพิ่มหลายคิวรวดได้! ระบบจะจัดคิวเองตามลำดับ VIP ก่อนเสมอ

---

#### 🔴 ส่วนที่ 2: จุดหยุด (Breakpoints)

Breakpoints ช่วยให้คุณ **"หยุดเวลา"** เพื่อดูกระบวนการทีละขั้น:

| Breakpoint | ตำแหน่ง                | สิ่งที่จะเห็นเมื่อหยุด                                        |
| ---------- | ---------------------- | ------------------------------------------------------------- |
| **BP1**    | ก่อนบันทึก SQLite      | Flutter Panel สว่าง — เข้าใจว่าข้อมูลจะถูกบันทึกลงเครื่องก่อน |
| **BP2**    | ส่งข้อมูลไป Broker     | Flutter Panel สว่าง — เตรียมส่งข้อมูลผ่านเครือข่าย            |
| **BP3**    | ข้อมูลเข้า Broker แล้ว | RabbitMQ Panel สว่าง — ข้อมูลอยู่ใน Queue แล้ว                |
| **BP4**    | Worker ก่อน COMMIT     | Go Worker Panel สว่าง — กำลังจะบันทึกลงฐานข้อมูลหลัก          |
| **BP5**    | ACK กลับมาที่แอป       | RabbitMQ Panel สว่าง — ACK กำลังเดินทางกลับ                   |
| **BP6**    | ก่อนอัปเดตเป็น Synced  | Flutter Panel สว่าง — กำลังจะทำเครื่องหมาย "ซิงค์แล้ว"        |

**วิธีใช้ Breakpoints:**

1. ✅ ติ๊กเลือก Breakpoint ที่ต้องการ (เลือกได้หลายตัว)
2. กดเพิ่มคิว
3. ระบบจะ **หยุดอัตโนมัติ** ที่จุด Breakpoint นั้น
4. Panel ที่เกี่ยวข้องจะ **สว่างขึ้น** (ขอบสีชมพู) พร้อมแสดง "BREAKPOINT"
5. กดปุ่มเพื่อไปต่อ:

| ปุ่ม                       | การทำงาน                                       |
| -------------------------- | ---------------------------------------------- |
| **▶ เล่นต่อ (Resume)**     | วิ่งต่อจนถึง Breakpoint ถัดไป หรือจนจบ         |
| **⇥ ทีละขั้น (Step Over)** | เดินไปแค่จุดหยุดถัดไป (ไม่ว่าจะติ๊กไว้หรือไม่) |

---

#### ⚡ ส่วนที่ 3: เครือข่าย (Network Toggle)

| สถานะ          | ปุ่ม                      | พฤติกรรม                                       |
| -------------- | ------------------------- | ---------------------------------------------- |
| 🟢 **ONLINE**  | `สลับเครือข่าย [ONLINE]`  | ข้อมูลส่งไป Broker ได้ตามปกติ                  |
| 🔴 **OFFLINE** | `สลับเครือข่าย [OFFLINE]` | ส่งไม่ได้ → ข้อมูลเก็บใน SQLite (Pending_Sync) |

**สาธิตการทำงาน Offline-First:**

1. กดเปลี่ยนเป็น **OFFLINE**
2. กดเพิ่มคิว 2-3 ตัว → สลิปพิมพ์ได้ปกติ / SQLite เก็บเป็น Pending_Sync
3. กดเปลี่ยนกลับเป็น **ONLINE**
4. ดู **Background Sync** ทำงานอัตโนมัติ — ทยอยส่งข้อมูลที่ค้างไว้

---

#### 💥 ส่วนที่ 4: จำลองวิกฤต (Chaos Engineering)

##### 💥 จำลอง DB ล่ม (Simulate DB Crash)

**วิธีทดสอบ:**

1. ✅ ติ๊ก **BP4** (Worker ก่อน COMMIT)
2. กดเพิ่มคิว → รอจนหยุดที่ BP4
3. กดปุ่ม **💥 จำลอง DB ล่ม**
4. ดูผลลัพธ์:
   - Go Worker แสดง **ROLLBACK** (สีแดง)
   - **ไม่ส่ง ACK** → RabbitMQ คืนข้อมูลกลับเข้าคิว (REQUEUED)
   - รอ ~2 วินาที → Worker ตัวใหม่มารับไปทำต่อ (Auto Recovery)
   - **ข้อมูลไม่สูญหาย!** ✅

> สิ่งนี้พิสูจน์ว่า: Manual ACK + Transaction = ไม่มีข้อมูลหาย

---

##### 👻 จำลองคิวผี (Simulate Ghost Queue)

**วิธีทดสอบ:**

1. ✅ ติ๊ก **BP5** (ACK กลับมาที่แอป)
2. กดเพิ่มคิว → รอจนหยุดที่ BP5
3. กดปุ่ม **👻 จำลองคิวผี**
4. ดูผลลัพธ์:
   - ACK ถูก **ดร็อป** ระหว่างทาง → แอปไม่รู้ว่าสำเร็จ
   - แอปส่งข้อมูลคิวเดิม**ซ้ำ**เข้า RabbitMQ
   - Go Worker ตรวจ **Idempotency Key** → พบว่าเคยทำแล้ว → **Skip!**
   - ส่ง ACK ใหม่กลับไป → SQLite อัปเดตเป็น Synced
   - **ไม่เกิดคิวซ้ำ!** ✅

> สิ่งนี้พิสูจน์ว่า: Idempotency Key = ป้องกัน Ghost Queue

---

### 3.4 สถานการณ์แนะนำสำหรับการสาธิต

#### 🟢 สถานการณ์ 1: การทำงานปกติ (Basic Flow)

```
1. เปิดไฟล์ในBrowser
2. กดปุ่ม [+ เพิ่มคิว VIP]
3. สังเกตข้อมูลไหลผ่านทุก Panel จนจบ Pipeline
4. กดปุ่ม [+ เพิ่มคิวปกติ]
5. สังเกตว่าข้อมูลทำงานเหมือนกัน แต่แยกสีชัดเจน
```

#### 🔵 สถานการณ์ 2: ดูทีละขั้น (Step-by-Step Debug)

```
1. ✅ ติ๊ก Breakpoint ทุกตัว (BP1-BP6)
2. กดเพิ่มคิว
3. ระบบหยุดที่ BP1 → อ่าน Event Log → กด [ทีละขั้น]
4. หยุดที่ BP2 → อ่าน Event Log → กด [ทีละขั้น]
5. ... ทำต่อจนจบ — เข้าใจทุกขั้นตอนอย่างละเอียด
```

#### 🟡 สถานการณ์ 3: Priority Queue Demo

```
1. กดเพิ่มคิวปกติ 3 ตัว (N-001, N-002, N-003)  ← กดเร็วๆ
2. กดเพิ่มคิว VIP 1 ตัว (V-001)
3. สังเกต: V-001 จะถูกประมวลผลก่อน N-001 ที่มาก่อน
4. → พิสูจน์ว่า VIP ได้สิทธิ์ก่อนเสมอ
```

#### 🔴 สถานการณ์ 4: Offline-First Demo

```
1. กดเปลี่ยนเป็น OFFLINE
2. กดเพิ่มคิว VIP 2 ตัว + คิวปกติ 1 ตัว
3. สังเกต: สลิปพิมพ์ได้ทุกตัว / SQLite เป็น Pending_Sync
4. กดเปลี่ยนกลับ ONLINE
5. ดู Background Sync ทำงานอัตโนมัติ
```

#### ⚠️ สถานการณ์ 5: จำลองวิกฤตครบทุกกรณี

```
1. ✅ ติ๊ก BP4 → เพิ่มคิว → กด [💥 DB ล่ม] → ดู Recovery อัตโนมัติ
2. ✅ ติ๊ก BP5 → เพิ่มคิว → กด [👻 คิวผี] → ดู Idempotency ทำงาน
3. → พิสูจน์ว่าระบบทนทานต่อทุกวิกฤต
```

---

### 3.5 การอ่าน Event Log (บันทึกเหตุการณ์)

Event Log Console คือ "สมองของระบบ" — ทุกสิ่งที่เกิดขึ้นจะถูกบันทึกไว้:

| สี              | ประเภท      | ความหมาย                                   |
| --------------- | ----------- | ------------------------------------------ |
| 🔵 **ฟ้า**      | `info`      | ข้อมูลทั่วไป — ขั้นตอนที่กำลังทำ           |
| 🟢 **เขียว**    | `ok`        | สำเร็จ — บันทึกเสร็จ, ACK ได้รับ           |
| 🟡 **เหลือง**   | `warn`      | เตือน — เน็ตหลุด, ข้อมูลค้าง               |
| 🟣 **ม่วงอ่อน** | `rationale` | เหตุผลเชิงวิศวกรรม — _ทำไมถึงออกแบบแบบนี้_ |
| 🩷 **ชมพู**     | `bp`        | Breakpoint — ระบบหยุดที่จุดนี้             |
| 🟣 **ม่วงเข้ม** | `dup`       | พบข้อมูลซ้ำ — Idempotency Key ทำงาน        |
| 🔴 **แดง**      | `err`       | ข้อผิดพลาด — DB ล่ม, เครือข่ายหลุด         |

**แต่ละบรรทัดแสดง:**

```
[เวลา]  [ขั้นตอน]  ข้อความอธิบาย
                     └── JSON payload (ถ้ามี)
```

---

### 3.6 สรุปสถาปัตยกรรมทั้งระบบ (End-to-End Flow)

```
 ลูกค้ามาถึง
      │
      ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 1: ENQUEUE (Flutter Desktop App)                 │
│  ┌────────┐    ┌──────────┐    ┌──────────────────┐    │
│  │ กดปุ่ม   │───→│ Memory   │───→│ SQLite (Local)   │    │
│  │ ออกคิว  │    │ Queue    │    │ Pending_Sync     │    │
│  └────────┘    │ VIP:idx0 │    └──────────────────┘    │
│                │ NOR:idx1 │    ┌──────────────────┐    │
│                └──────────┘    │ 🖨️ พิมพ์สลิป        │    │
│                                │ (ทันที O(1))       │    │
│                                └──────────────────┘    │
└────────────────────┬────────────────────────────────────┘
                     │  Background Isolate (Thread แยก)
                     ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 2: MESSAGE BROKER (RabbitMQ)                     │
│  ┌──────────────────────────────────────────────┐       │
│  │  Persistent Queue (เก็บใน Disk)               │       │
│  │  ┌─────┐ ┌─────┐ ┌─────┐                     │       │
│  │  │V-001│ │N-001│ │V-002│  ...                │       │
│  │  └─────┘ └─────┘ └─────┘                     │       │
│  └──────────────────────────────────────────────┘       │
└────────────────────┬────────────────────────────────────┘
                     │  Push Message
                     ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 3: PROCESSING (Go Worker)                        │
│  ┌─────────────────────────────────────────────────┐   │
│  │  goroutine[1] — Manual ACK Mode                 │   │
│  │                                                 │   │
│  │  BEGIN TRANSACTION                              │   │
│  │    → CHECK Idempotency Key (ป้องกันซ้ำ)            │   │
│  │    → EXECUTE Business Logic                     │   │
│  │    → COMMIT to Main DB                          │   │
│  │  SEND ACK → RabbitMQ ลบ message                 │   │
│  │                                                 │   │
│  │  ❌ หากล้มเหลว:                                  │   │
│  │    → ROLLBACK                                   │   │
│  │    → NO ACK → RabbitMQ re-queue อัตโนมัติ          │   │
│  └─────────────────────────────────────────────────┘   │
│                          │                             │
│                          ▼                             │
│  ┌──────────────────────────────────────┐              │
│  │  Main Database                       │              │
│  │  queue_id | queue_no | pri | status  │              │
│  │  ──────── | ──────── | ─── | ─────── │              │
│  │  uuid...  | V-001    |  0  | done    │              │
│  └──────────────────────────────────────┘              │
└────────────────────┬────────────────────────────────────┘
                     │  ACK กลับไปยัง App
                     ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 4: CONFIRMATION                                  │
│  SQLite: Pending_Sync → Synced ✅                       │
│  Memory Queue: Dequeue (ลบ Node ออก)                    │
│  Pipeline: Complete ✓                                   │
└─────────────────────────────────────────────────────────┘
```

---

### 3.7 Glossary (คำศัพท์สำคัญ)

| คำศัพท์                  | ความหมาย                                                           |
| ------------------------ | ------------------------------------------------------------------ |
| **Enqueue**              | การเพิ่มข้อมูลเข้าคิว (ต่อท้าย TAIL)                               |
| **Dequeue**              | การดึงข้อมูลออกจากคิว (ดึงจาก HEAD)                                |
| **Priority Queue**       | คิวที่มีลำดับความสำคัญ — VIP ถูกดึงก่อนเสมอ                        |
| **Linked List**          | โครงสร้างข้อมูลแบบโซ่ — แต่ละ Node ชี้ไปยัง Node ถัดไป             |
| **Producer**             | ฝ่ายผลิตข้อมูล (Flutter App ที่ออกคิว)                             |
| **Consumer**             | ฝ่ายรับข้อมูลมาประมวลผล (Go Worker)                                |
| **Message Broker**       | ตัวกลางจัดการข้อความ (RabbitMQ)                                    |
| **ACK (Acknowledgment)** | สัญญาณยืนยันว่าประมวลผลสำเร็จ                                      |
| **NACK**                 | สัญญาณปฏิเสธ — ไม่สำเร็จ                                           |
| **Idempotency Key**      | กุญแจป้องกันการทำงานซ้ำ (UUID ที่ไม่ซ้ำกัน)                        |
| **Ghost Queue**          | คิวผี — เกิดจากการส่งข้อมูลซ้ำเมื่อ ACK สูญหาย                     |
| **Offline-First**        | ออกแบบให้ทำงานได้แม้ไม่มีเน็ต — ข้อมูลเก็บในเครื่องก่อน            |
| **Background Sync**      | กระบวนการส่งข้อมูลที่ค้างไว้ขึ้น Server เมื่อเน็ตกลับมา            |
| **Manual ACK**           | โหมดยืนยันด้วยตนเอง — ไม่ส่ง ACK จนกว่า DB จะ COMMIT สำเร็จ        |
| **Re-queue**             | คืนข้อมูลกลับเข้าคิว — เกิดเมื่อ Worker ล้มเหลวและไม่ส่ง ACK       |
| **Breakpoint**           | จุดหยุด — ใช้สำหรับ Debug ดูการทำงานทีละขั้น                       |
| **Chaos Engineering**    | การจำลองวิกฤตเพื่อทดสอบความทนทานของระบบ                            |
| **O(1)**                 | ความเร็วคงที่ — ไม่ว่าข้อมูลจะมากแค่ไหน ก็ทำงานเร็วเท่าเดิม        |
| **Transaction**          | การรวมกลุ่มคำสั่ง DB — ต้องสำเร็จทั้งหมดหรือยกเลิกทั้งหมด (Atomic) |

---

_เอกสารนี้สร้างจากการวิเคราะห์ source code ทุกบรรทัดของโปรเจ็ก Queue Management System Demo v3.0_
