# 🚀 BidMatrix Pro — Automated Tender Quotation Evaluation & Best-Bidder Selection System
### High-Performance Desktop Software for Automated Procurement Comparative Statements (FCS)

[![Platform](https://img.shields.io/badge/Platform-Windows%2010%20%2F%2011%20Native%20Desktop-0078D6?logo=windows&logoColor=white)](desktop_app.py)
[![GUI](https://img.shields.io/badge/GUI-Native%20PyQt6%20%2F%20Qt%20Desktop-41CD52?logo=qt&logoColor=white)](desktop_app.py)
[![Engine](https://img.shields.io/badge/OCR%20Engine-RapidOCR%20%7C%20WinRT%20Native-00A86B)](#3-hybrid-pdf-extraction--ocr-engine)
[![Standards](https://img.shields.io/badge/Compliance-SPPRA%20%2F%20PPRA%20Rules-FF6F00)](#7-audit-integrity--compliance)
[![Database](https://img.shields.io/badge/Database-SQLite%203%20%28Embedded%29-003B57?logo=sqlite&logoColor=white)](#5-database-schema--architecture)

---

## 📌 Executive Summary for Procurement Directors & Managers

In public and corporate tenders governed by procurement authorities (e.g., **SPPRA Rule 15(B)**, PPRA), procurement evaluation committees spend dozens of manual hours cross-referencing vendor quotations across hundreds of Bill of Quantities (BOQ) line items.

Manual entry from scanned PDF quotations into Excel leads to:
1. **Critical Arithmetic Errors** (e.g., misinterpreting per-item vs per-box pricing, as seen in manual tender entries).
2. **Delayed Comparative Statements (FCS)** taking days to compile.
3. **Audit Vulnerabilities** during bid-security verification and lowest-bidder evaluations.

**BidMatrix Pro** solves this by providing a **super-fast, native Windows desktop software** that ingests multi-vendor PDF quotations (both digital and scanned), extracts pricing and specifications using a local AI-powered OCR pipeline, maps them across tender items, calculates automatic item-level rankings ($L_1, L_2, L_3 \dots$), and exports audit-ready, 100% Excel-compatible Financial Comparative Statements.

> [!TIP]
> **What is a BOQ (Bill of Quantities) & Is it Required?**
> A BOQ is the official tender item list published by the procuring agency (e.g., hospital or government authority).
> **In BidMatrix Pro, a separate BOQ file is COMPLETELY OPTIONAL.** You do **NOT** need a separate BOQ file to run comparisons. The software's AI extraction engine automatically builds the master item catalog, codes, descriptions, and quantities directly from your uploaded vendor quotation PDFs!

---

## 📊 End-to-End Visual Workflow

```mermaid
flowchart TD
    subgraph INGESTION ["1. Ingestion Phase (Simple 2-Step Workflow)"]
        STEP1["Step 1: Choose Multi-Vendor PDF Quotations<br/>(Digital text or scanned CamScanner PDFs)"]
        BOQ_OPT["Optional: Import Master BOQ Template<br/>(Not required - auto-extracted from PDFs)"]
        STEP2["Step 2: Click ⚡ PROCESS QUOTATIONS & SEE RESULTS"]
        STEP1 --> STEP2
        BOQ_OPT -.-> STEP2
    end

    subgraph EXTRACTION ["2. Hybrid Extraction & Neural OCR"]
        STEP2 --> E{"Document Type?"}
        E -- "Vector / Digital PDF" --> F["Native Fast Text Stream Extractor"]
        E -- "Scanned / Image PDF" --> G["High-Speed Neural OCR Engine<br/>RapidOCR / PyPDFium2"]
        F --> H["Item Parser & Arithmetic Verification"]
        G --> H
    end

    subgraph MAPPING ["3. Automatic Item Catalog Creation"]
        H --> I["Auto-Extract Code, Description, Qty, UOM"]
        I --> J["Arithmetic Cross-Check: Rate x Qty == Total"]
        J --> K[("Embedded SQLite Local Database")]
    end

    subgraph EVALUATION ["4. Deterministic Ranking Engine"]
        K --> L["Group by Item Code across all Bidders"]
        L --> M["Lowest Price Wins: Rank 1 L1 ★"]
        M --> N["Incremental Ranks: 2nd, 3rd, 4th"]
        N --> O["5% Bid Security Compliance Audit"]
    end

    subgraph OUTPUT ["5. Executive UI & Export"]
        O --> P["🖥️ Native Desktop Comparative Data Grid"]
        P --> Q["📊 KPI Metrics & UOM Conflict Alerts"]
        P --> R["📥 1-Click CSV & Excel Export"]
    end

    style INGESTION fill:#EBF5FB,stroke:#2980B9,stroke-width:2px;
    style EXTRACTION fill:#FEF9E7,stroke:#F39C12,stroke-width:2px;
    style MAPPING fill:#EAFAF1,stroke:#27AE60,stroke-width:2px;
    style EVALUATION fill:#F4ECF7,stroke:#8E44AD,stroke-width:2px;
    style OUTPUT fill:#FDEDEC,stroke:#C0392B,stroke-width:2px;
```

---

## 🏛️ System Architecture

```mermaid
graph TB
    subgraph PRESENTATION ["Presentation Layer (PyQt6 Native Desktop - Windows x64)"]
        UI_DASH["Executive KPI Dashboard - 5 Summary Cards"]
        UI_INGEST["Two-Step Ingestion: Choose PDFs -> ⚡ Process"]
        UI_FILTER["Multi-Criteria Filter & UOM Conflict Toolbar"]
        UI_GRID["9-Column Comparative Statement Data Grid"]
        UI_AUDIT["5% Bid Security & Arithmetic Audit Dialog"]
    end

    subgraph CONTROLLER ["UI Controllers & Event Bus"]
        SIGNALS["PyQt6 PySide Signals & Slots Architecture"]
        PROGRESS_SIG["Real-time Parsing Progress Reporter"]
    end

    subgraph CORE_SERVICES ["Core Engine Services (Background QThread Isolates)"]
        PDF_PARSER["Dual-Engine PDF Parser: Vector + PyPDFium2"]
        OCR_ENGINE["Local Neural OCR Engine<br/>RapidOCR / ONNX Runtime Native"]
        VALIDATOR["Sanity & Arithmetic Verification Engine"]
        RANK_CALC["Deterministic Bid Ranking Algorithm (L1, L2, L3...)"]
        EXPORTER["Excel & CSV Exporter (OpenPyXL / CSV)"]
    end

    subgraph PERSISTENCE ["Data Persistence Layer"]
        DB[("Local Embedded SQLite 3 Database")]
        SCHEMA[("Normalized Tender & Bid Comparative Schema")]
    end

    PRESENTATION <--> CONTROLLER
    CONTROLLER <--> CORE_SERVICES
    CORE_SERVICES <--> PERSISTENCE

    style PRESENTATION fill:#E8F8F5,stroke:#16A085,stroke-width:2px;
    style CONTROLLER fill:#EBF5FB,stroke:#3498DB,stroke-width:2px;
    style CORE_SERVICES fill:#FEF5E7,stroke:#E67E22,stroke-width:2px;
    style PERSISTENCE fill:#F5EEF8,stroke:#9B59B6,stroke-width:2px;
```

---

## 🔬 Hybrid PDF Extraction & OCR Strategy

Vendors submit quotations in varying formats: some generate crystal-clear digital PDFs directly from accounting software, while others submit physical printouts stamped and scanned via mobile apps (e.g., CamScanner). 

**BidMatrix Pro** incorporates a two-tier extraction pipeline:

```mermaid
sequenceDiagram
    autonumber
    actor User as Procurement Officer
    participant UI as PyQt6 Desktop Window
    participant Worker as Background QThread Worker
    participant Extractor as Text Extractor (pypdf/pypdfium2)
    participant OCR as Neural OCR (RapidOCR ONNX)
    participant DB as SQLite Storage

    User->>UI: Select Vendor PDFs (Ettihad, Prince, Haq, ZS)
    User->>UI: Click ⚡ PROCESS QUOTATIONS & SEE RESULTS
    UI->>Worker: Launch extraction in background thread
    loop For Each Vendor PDF
        Worker->>Extractor: Scan Page Text Stream
        alt Digital Text Present (Vector PDF)
            Extractor-->>Worker: Extract Item Codes, Descriptions, Quantities, Rates
        else Scanned / Mobile App Image (CamScanner)
            Worker->>OCR: High-Res Render (300 DPI) & OCR Token Recognition
            OCR-->>Worker: Return Bounding Boxes & Text Tokens
        end
        Worker->>Worker: Reconcile Arithmetic (Qty * Unit Rate == Total)
        Worker->>DB: Upsert Master Items & Vendor Quotation Records
        Worker-->>UI: Emit progress update signal (e.g. 50%, 75%, 100%)
    end
    Worker-->>UI: Ingestion Complete Signal
    UI->>DB: Query Evaluated Rankings & Best Bidder (L1) Badges
    UI->>User: Render 9-Column Comparative Grid with Emerald ★ 1st Badges
```

### Key Technical Safeguards:
1. **Never Blocks the UI**: All PDF extraction and OCR tasks run in separate background worker isolates/processes. The UI maintains 60 FPS smooth animation.
2. **Arithmetic Mismatch Alert**: If a vendor writes a unit rate of `4.60` for a box of 2,000 units with extended total `920.00` instead of `9,200.00` (as identified in actual tender audits), the software flags an **Arithmetic Warning Badge** for one-click verification.
3. **Offline & Air-Gapped**: Runs 100% on the local Windows machine without uploading sensitive tender documents to external cloud APIs, ensuring absolute procurement confidentiality.

---

## 🎯 9-Column Comparative Data Model (Excel Matching)

The output adheres strictly to the benchmark 9-column comparative format established in public procurement statements:

| Column # | Field Name | Data Type | Description |
|:---:|---|:---:|---|
| **1** | `Code` | String | Standard BOQ Code (e.g., `Pri0002`, `Pri0003`) |
| **2** | `Item Description` | String | Master Hospital / Institutional Item Name |
| **3** | `Tentative Qty.` | Integer | Required procurement tender quantity |
| **4** | `UOM` | String | Unit of Measurement (`Nos.`, `Sheet`, `Pad of 100 Pages`, `Box`) |
| **5** | `Firm Name` | String | Name of the bidding vendor (e.g., `M/s. Prince Enterprises`) |
| **6** | `Quoted Description` | String | Vendor's specific technical specs (e.g., `350 GSM Bleach Card`) |
| **7** | `Unit Price` | Decimal (2) | Quoted Unit Rate in PKR |
| **8** | `Total Price` | Decimal (2) | Extended Price (`Tentative Qty` $\times$ `Unit Price`) |
| **9** | `Ranking` | String | Automatically evaluated ranking (`1st`, `2nd`, `3rd`, `4th`) |

---

## ⚡ Automatic Ranking & Best-Bidder Algorithm

The software evaluates every item independently across all participating firms.

```mermaid
flowchart LR
    Start(["Item Price Evaluation"]) --> Group["Group All Bids by Item Code"]
    Group --> Filter["Filter Valid Bids<br/>Rate > 0 and Not Disqualified"]
    Filter --> Sort["Sort by Unit Price Ascending"]
    Sort --> RankAssign["Assign Rank 1 to Lowest Rate"]
    RankAssign --> CheckTie{"Tie Detected?<br/>Same Unit Price"}
    CheckTie -- "Yes" --> TieRule["Assign Same Rank e.g., 1st<br/>Flag Tie for Committee"]
    CheckTie -- "No" --> NextRank["Assign Incremental Ranks<br/>2nd, 3rd, 4th"]
    TieRule --> Save["Persist to Comparative Statement"]
    NextRank --> Save
    Save --> Summary["Update Best Bidder Award Analytics"]

    style Start fill:#E8EAF6,stroke:#3F51B5
    style RankAssign fill:#C8E6C9,stroke:#2E7D32,stroke-width:2px
    style CheckTie fill:#FFF9C4,stroke:#FBC02D
    style Summary fill:#E1F5FE,stroke:#0288D1
```

### Deterministic Ranking Rules:
* **Rule 1 (Lowest Cost Wins)**: The company with the lowest valid unit price receives **Rank 1 (`1st`)**.
* **Rule 2 (Independent Item Evaluation)**: A company ranked `1st` on item A is evaluated independently on item B. Each item is awarded strictly on its own merit.
* **Rule 3 (Tie Handling)**: If two companies quote identical rates (e.g. both quote PKR 2.50), both receive `1st (Tied)`, and a notification appears for sample quality or committee lottery resolution.
* **Rule 4 (Invalid / Zero Exclusion)**: Missing items or zero-priced bids are marked as `Not Quoted` and excluded from ranking rather than misinterpreting them as free items.

---

## 🎛️ Advanced Multi-Criteria Filtering & UOM Normalization

In real-world tenders, vendors occasionally quote items in different Units of Measurement (e.g., one vendor quotes per **Box of 2,000**, another per **Sheet** or **Pad of 100 Pages**). 

**BidMatrix Pro** provides an **Intelligent Filter & Normalization Engine**:

```mermaid
flowchart TD
    subgraph INPUT ["Quotation Stream"]
        Q1["Vendor A: PKR 9,200 / Box (2000 units)"]
        Q2["Vendor B: PKR 4.60 / Sheet (Per unit)"]
    end

    subgraph ENGINE ["Filter & Normalization Engine"]
        F1{"User Filter Applied?"}
        F1 -- "No Filter (Default)" --> DEF["Automatic Lowest Unit Price Ranking (L1, L2, L3...)"]
        F1 -- "Custom Criteria" --> FIL["Apply Multi-Criteria Rule:"]
        FIL --> C1["• Filter by Specific UOM (e.g. Only 'Box' or Only 'Sheet')"]
        FIL --> C2["• UOM Unit-Cost Normalization (Convert Box rate to Per-Sheet rate)"]
        FIL --> C3["• Filter by Specific Firm(s) or Price Threshold"]
        FIL --> C4["• Filter by Awarded / Shortlisted / Disqualified Status"]
    end

    subgraph RESULT ["Dynamic Real-Time Re-Ranking"]
        DEF --> RES["Recalculate Table & Instant Visual L1 Highlight"]
        C1 --> RES
        C2 --> RES
        C3 --> RES
        C4 --> RES
    end

    style INPUT fill:#EBF5FB,stroke:#2980B9
    style ENGINE fill:#FEF9E7,stroke:#F39C12
    style RESULT fill:#EAFAF1,stroke:#27AE60
```

### Filtering Capabilities:
1. **Default Auto-Ranking**: If no filter is active, the engine automatically sorts and ranks all bidders by lowest unit price across every single line item ($L_1, L_2, L_3 \dots$).
2. **UOM Conflict Isolation**: Instantly filter items where vendors quoted differing UOMs (e.g., `Box` vs `Pad` vs `Sheet`) so the procurement officer can examine unit parity.
3. **UOM Unit-Rate Normalization**: Toggle between *As-Quoted Unit Price* and *Normalized Per-Piece Unit Rate* to ensure apples-to-apples evaluation.
4. **Multi-Field Instant Search & Filter**: Filter by Firm Name, Item Code, Price Range, Ranking (`1st` only, `2nd`, etc.), or Approval Status with instant sub-millisecond response.

---

## 🗄️ Database Schema (SQLite)

```mermaid
erDiagram
    TENDER ||--o{ TENDER_ITEM : contains
    TENDER ||--o{ VENDOR : participates
    VENDOR ||--o{ QUOTATION_ENTRY : submits
    TENDER_ITEM ||--o{ QUOTATION_ENTRY : receives
    QUOTATION_ENTRY ||--o| BID_RANKING : evaluated_as

    TENDER {
        string tender_id PK
        string tender_title
        string tender_reference
        string epads_id
        datetime opening_date
        datetime created_at
    }

    TENDER_ITEM {
        string item_id PK
        string tender_id FK
        int item_sno
        string item_code
        string item_description
        double tentative_qty
        string uom
    }

    VENDOR {
        string vendor_id PK
        string tender_id FK
        string firm_name
        string contact_person
        string phone
        string email
        string ntn
        string gst
        double sum_offered_amount
        double required_security_5pct
        double attached_payorder_amount
        string payorder_number
        string bank_details
        string qualification_status
    }

    QUOTATION_ENTRY {
        string entry_id PK
        string item_id FK
        string vendor_id FK
        string quoted_description
        double unit_price
        double total_price
        boolean is_arithmetic_valid
        string sample_status
    }

    BID_RANKING {
        string ranking_id PK
        string entry_id FK
        int rank_number
        string rank_label
        boolean is_lowest_bidder
        string committee_decision
        string remarks
    }
```

---

## 🖥️ UI/UX Design Specifications (Modern Light Theme)

The UI is built as a **100% Native Windows Desktop Application** using PyQt6 / Qt6, following ergonomic enterprise design standards:

```
+------------------------------------------------------------------------------------------------------------------------------------------+
|  ⚡ BidMatrix Pro — Automated Tender Quotation Evaluation System  |  Tender Ref: PROC/SMBBIT/(PP-01)/2026-27  | EPADS: S-26090824871    |
+------------------------------------------------------------------------------------------------------------------------------------------+
|  [ + Choose PDF Quotations ]   [ ⚡ PROCESS QUOTATIONS & SEE RESULTS ]   [ 📋 Optional: Import BOQ ]   [ 🗑️ Clear All ]   [ 📥 Export CSV ] |
|  Selected: 4 quotation PDFs ready for ingestion                                 [ Progress: [====================================] 100% ]|
+------------------------------------------------------------------------------------------------------------------------------------------+
|  EXECUTIVE KPI DASHBOARD:                                                                                                                |
|  [ 📦 Total Items: 90 ]  [ 🏢 Bidders: 4 ]  [ 📝 Quoted Bids: 271 ]  [ 🏆 Rank 1 Awards: 45 ]  [ 🛡️ 5% Bid Security: Audit Details ]     |
+------------------------------------------------------------------------------------------------------------------------------------------+
|  ADVANCED FILTER & NORMALIZATION TOOLBAR:                                                                                                |
|  [ 🔍 Search Item / Code / Firm... ]  [ Firm: All Bidders v ]  [ UOM: All v ]  [ Rank: All v ]  [⚠️ Show Different UOM Quoted Only [ ]]   |
+------------------------------------------------------------------------------------------------------------------------------------------+
|  COMPARATIVE EVALUATION GRID (FCS View - 9 Columns matching Excel Benchmark):                                                           |
|                                                                                                                                          |
|  Code     | Item Description            | Tentative Qty | UOM   | Firm Name               | Quoted Description | Unit Price | Total Price | Rank  |
|  ---------+-----------------------------+---------------+-------+-------------------------+--------------------+------------+-------------+-------|
|  Pri0002  | White Confidential Medical  | 20,000        | Nos.  | M/s. Prince Enterprises | 350 GSM Bleach     |      50.00 |  1,000,000  | ★ 1st |
|  Pri0002  | White Confidential Medical  | 20,000        | Nos.  | M/s. Haq Enterprises    | 300 GSM Bleach     |      54.00 |  1,080,000  | 2nd   |
|  Pri0002  | White Confidential Medical  | 20,000        | Nos.  | M/s. Ettihad Traders    | Bleach Card        |      43.80 |    876,000  | Disq  |
|  ---------+-----------------------------+---------------+-------+-------------------------+--------------------+------------+-------------+-------|
|  Pri0003  | Physician Order Sheet       | 90,000        | Sheet | M/s. Prince Enterprises | 70 GSM Local       |       2.38 |    214,200  | ★ 1st |
|  Pri0003  | Physician Order Sheet       | 90,000        | Sheet | M/s. Haq Enterprises    | 68 GSM Form        |       2.50 |    225,000  | 2nd   |
|  Pri0003  | Physician Order Sheet       | 90,000        | Sheet | M/s. Z.S Traders        | Standard Paper     |       5.20 |    468,000  | 3rd   |
+------------------------------------------------------------------------------------------------------------------------------------------+
|  STATUS: Ready | Database Synced (bidmatrix_procurement.db) | 271 Quotation Records Loaded | 5% Security Verified | Offline & Air-Gapped|
+------------------------------------------------------------------------------------------------------------------------------------------+
```

### Color Palette Tokens:
- **Primary Canvas**: `#F8FAFC` (Slate Ultra Light)
- **Card & Grid Surface**: `#FFFFFF` (Pure White with subtle `#E2E8F0` borders)
- **Primary Brand / Action**: `#0284C7` (Sky Blue 600)
- **Winner / Rank 1 Highlight**: `#DCFCE7` (Emerald Soft Green) with `#15803D` text & gold star badge `★ 1st`
- **Runner Up (Rank 2)**: `#F1F5F9` (Slate 100)
- **Disqualification / Warning**: `#FEE2E2` (Rose Soft Red) with `#B91C1C` alert indicator
- **Typography**: Segoe UI / Inter modern sans-serif typography for maximum numerical legibility.

---

## 🛠️ Technology Stack & Dependencies

| Layer | Component | Selected Technology | Rationale |
|---|---|---|---|
| **Desktop GUI** | Native Window Framework | **PyQt6 (Qt 6.11 x64 Native)** | 100% native Windows window, 0 browser dependencies, no symlink issues |
| **Local Storage** | Database Engine | **SQLite 3 (`sqlite3` Embedded)** | Zero configuration, self-contained, lightning fast ACID transactions |
| **PDF Extraction** | Vector & Stream Engine | **`pypdf` & `pypdfium2`** | High-fidelity coordinate extraction for digital text PDFs |
| **OCR Pipeline** | Scanned PDF Parser | **`rapidocr-onnxruntime` Neural OCR** | Embedded high-speed OCR on Windows without cloud latency or API keys |
| **Export Engine** | Comparative Exporter | **Standard CSV & `openpyxl`** | 100% formatting fidelity matching standard institutional templates |
| **Windows Runner** | Instant Launcher | **`Launch_BidMatrix_Desktop.bat`** | Double-click instant execution on Windows 10 / 11 |

---

## 🚀 Quick Start / How to Run

### Method 1: One-Click Desktop Launcher
Simply double-click the included batch script in the project root:
```cmd
Launch_BidMatrix_Desktop.bat
```

### Method 2: Command Line (PowerShell or CMD)
Ensure Python 3.10+ is installed, then run:
```bash
python desktop_app.py
```

### 📖 Step-by-Step User Guide for Procurement Officers:
1. **Choose PDFs**: Click **`+ Choose PDF Quotations`** and select all vendor quotation PDFs for the tender.
2. **Process**: Click the prominent **`⚡ PROCESS QUOTATIONS & SEE RESULTS`** button. The software parses all vendor quotes, extracts item codes, descriptions, quantities, and rates, saves them to SQLite, and computes lowest-bidder ranks automatically.
3. **Optional BOQ**: A separate BOQ file is **NOT** needed because the software extracts all items directly from vendor quotes. If you have an institutional BOQ template, click **`📋 Optional: Import BOQ`**.
4. **Filter & Inspect**:
   - Filter by **Firm** to inspect a specific vendor's full submission.
   - Filter by **Rank** (e.g. choose `1st (Lowest Bidder Only)` to see only winning line items).
   - Check **`⚠️ Show Different UOM Quoted Only`** to immediately detect items where vendors quoted conflicting packaging (e.g., Box vs Sheet).
5. **Bid Security Audit**: Click the **`🛡️ 5% Bid Security Compliance`** KPI card to view the exact Pay Order vs required 5% guarantee audit table.
6. **Export**: Click **`📥 Export CSV`** to export the entire 9-column comparative statement ready for signing and archiving.

---

## 📅 Implementation Roadmap

```mermaid
gantt
    title BidMatrix Pro Implementation Roadmap
    dateFormat  YYYY-MM-DD
    axisFormat  %b %d

    section Phase 1 - Foundation
    Database Schema & SQLite Engine          :done, des1, 2026-10-01, 2026-10-03
    Master BOQ Catalog Support (Optional)    :done, des2, after des1, 2d

    section Phase 2 - PDF & Neural OCR
    Digital PDF Extraction Service           :done, des3, after des2, 3d
    Neural OCR Pipeline for Scanned Quotes   :done, des4, after des3, 3d
    Text Harmonization & Regex Matcher       :done, des5, after des4, 2d

    section Phase 3 - Core Logic
    Deterministic Bid Ranking Engine L1-L4   :done, des6, after des5, 2d
    Five Percent Bid Security Audit Engine   :done, des7, after des6, 2d

    section Phase 4 - Native Desktop UI
    PyQt6 Native Windows Executive Grid      :done, des8, after des7, 3d
    Multi-Criteria Filter & UOM Toolbar      :done, des9, after des8, 2d
    Excel & CSV Comparative Exporter         :done, des10, after des9, 2d
    End-to-End Tender Audit Verification     :done, des11, after des10, 2d
```

| Phase | Milestone | Deliverable | Status |
|:---:|---|---|:---:|
| **1** | **Data Foundation** | SQLite 3 Relational Tender & Quotation Schema | ✅ Completed |
| **2** | **Extraction & OCR** | Vector text extraction & RapidOCR neural pipeline for scanned PDFs | ✅ Completed |
| **3** | **Evaluation Engine** | Deterministic item-level ranking ($L_1, L_2, L_3 \dots$) & 5% Security Audit | ✅ Completed |
| **4** | **Native Desktop UI** | PyQt6 Windows Desktop GUI with 2-step ingestion & live filter toolbar | ✅ Completed |
| **5** | **Export & Compliance** | 9-Column Comparative Statement CSV / Excel export | ✅ Completed |

---

## ⚖️ Audit Integrity & Compliance Notice
This software is engineered to assist Procurement Committees, Evaluation Officers, and Finance Directors in assembling Financial Comparative Statements in compliance with Public Procurement Regulatory Frameworks. All extracted records, OCR tokens, and ranking determinations maintain full, verifiable traceability to the original uploaded PDF documents.

