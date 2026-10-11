# 🚀 BidMatrix Pro — Automated Tender Quotation Evaluation & Best-Bidder Selection System
### High-Performance Desktop Software for Automated Procurement Comparative Statements (FCS)

[![Platform](https://img.shields.io/badge/Platform-Windows%20Desktop-0078D6?logo=windows&logoColor=white)](https://flutter.dev/desktop)
[![Framework](https://img.shields.io/badge/Framework-Flutter%203.x-02569B?logo=flutter&logoColor=white)](https://flutter.dev)
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

**BidMatrix Pro** solves this by providing a **super-fast, automated desktop software** that ingests multi-vendor PDF quotations (both digital and scanned), extracts pricing and specifications using a local AI-powered OCR pipeline, maps them against master BOQ items, calculates automatic item-level rankings ($L_1, L_2, L_3 \dots$), and exports audit-ready, 100% Excel-compatible Financial Comparative Statements.

---

## 📊 End-to-End Visual Workflow

```mermaid
flowchart TD
    subgraph INGESTION ["1. Ingestion Phase"]
        A[📁 Tender Master BOQ<br/>Excel / CSV Template] --> D[Core Processing Queue]
        B[📄 Vendor A PDF Quote<br/>Digital Text PDF] --> D
        C[📑 Vendor B & C PDF Quotes<br/>Scanned CamScanner PDFs] --> D
    end

    subgraph EXTRACTION ["2. Hybrid Extraction & OCR Engine"]
        D --> E{Document Type?}
        E -- Vector / Text PDF --> F[Fast Native Text Stream Extractor]
        E -- Scanned Image / Raster --> G[High-Speed Neural OCR Engine<br/>RapidOCR / WinRT]
        F --> H[Regex & Token Boundary Normalizer]
        G --> H
    end

    subgraph MAPPING ["3. Smart Item Matching & Validation"]
        H --> I[BOQ Item Code Matcher<br/>Pri0001, Pri0002...]
        I --> J[Quantity & UOM Sanity Check]
        J --> K[Arithmetic Verification<br/>Unit Price x Qty == Total Price]
        K --> L[(Embedded SQLite Database)]
    end

    subgraph EVALUATION ["4. Ranking & Comparative Engine"]
        L --> M[Item-by-Item Price Sorting]
        M --> N[Lowest Price Detection: Rank 1 L1]
        N --> O[Equal Price Tie-Breaking Logic]
        O --> P[Bid Security 5% Compliance Verification]
    end

    subgraph OUTPUT ["5. Executive UI & Export"]
        P --> Q[🖥️ Real-Time Interactive Data Grid]
        Q --> R[📊 Executive Summary Dashboard]
        Q --> S[📥 1-Click CSV & Excel Export<br/>Matching SMBBIT FCS Standard]
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
    subgraph PRESENTATION ["Presentation Layer (Flutter Desktop - Windows x64)"]
        UI_DASH[Executive KPI Dashboard]
        UI_UPLOAD[Drag & Drop Multi-PDF Ingestion Zone]
        UI_VERIFY[Data Verification & Correction Modal]
        UI_GRID[High-Performance Comparative Data Table]
        UI_FILTER[Multi-Column Filtering & Global Search]
    end

    subgraph STATE ["State Management & Controllers"]
        BLOC[Quotation Bloc / Riverpod Providers]
        EVENT_BUS[Async Event Stream & Progress Notifier]
    end

    subgraph CORE_SERVICES ["Core Engine Services (Background Isolates)"]
        PDF_PARSER[PDF Parsing Service]
        OCR_ENGINE[Local OCR Engine<br/>ONNX Runtime / WinRT Native]
        VALIDATOR[Sanity & Calculation Validator]
        RANK_CALC[Deterministic Bid Ranking Algorithm]
        EXPORTER[CSV / Excel OpenPyXL Engine]
    end

    subgraph PERSISTENCE ["Data Persistence Layer"]
        DB[(Local SQLite Database)]
        AUDIT_LOG[(Append-Only Audit Trail)]
    end

    PRESENTATION <--> STATE
    STATE <--> CORE_SERVICES
    CORE_SERVICES <--> PERSISTENCE

    style PRESENTATION fill:#E8F8F5,stroke:#16A085,stroke-width:2px;
    style STATE fill:#EBF5FB,stroke:#3498DB,stroke-width:2px;
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
    participant UI as Flutter Desktop UI
    participant Worker as Background Isolate
    participant Extractor as Text Extractor (pypdf/native)
    participant OCR as OCR Subsystem (RapidOCR/WinRT)
    participant DB as SQLite Storage

    User->>UI: Drop 4 Vendor PDFs (Ettihad, Prince, Haq, ZS)
    UI->>Worker: Dispatch PDF files
    loop For Each PDF
        Worker->>Extractor: Probe Page Stream for Text Layer
        alt Text layer found (>= 80% confidence)
            Extractor-->>Worker: Stream extracted structured tokens
        else Scanned / Raster image detected
            Worker->>OCR: Render Page Image (300 DPI)
            OCR-->>Worker: Return Bounding Boxes & Confidence Text
        end
        Worker->>Worker: Parse Item Code, Description, Qty, Rate, Total
        Worker->>Worker: Reconcile Arithmetic (Qty * Rate)
        Worker->>DB: Insert Clean Tender Line Items
        Worker-->>UI: Update Progress Bar (e.g. 75% complete)
    end
    UI->>User: Display Complete Comparative Statement with L1 Highlighted
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
    Start([Item Prize Evaluation]) --> Group[Group All Bids by Item Code]
    Group --> Filter[Filter Valid Bids<br/>Rate > 0 and Not Disqualified]
    Filter --> Sort[Sort by Unit Price Ascending]
    Sort --> RankAssign[Assign Rank 1 to Lowest Rate]
    RankAssign --> CheckTie{Tie Detected?<br/>Same Unit Price}
    CheckTie -- Yes --> TieRule[Assign Same Rank e.g., 1st<br/>Flag Tie for Committee]
    CheckTie -- No --> NextRank[Assign Incremental Ranks<br/>2nd, 3rd, 4th]
    TieRule --> Save[Persist to Comparative Statement]
    NextRank --> Save
    Save --> Summary[Update Best Bidder Award Analytics]

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
        F1 -- No Filter (Default) --> DEF["Automatic Lowest Unit Price Ranking (L1, L2, L3...)"]
        F1 -- Custom Criteria --> FIL["Apply Multi-Criteria Rule:"]
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

The UI is built with Flutter Desktop following modern, ergonomic enterprise guidelines:

```
+----------------------------------------------------------------------------------------------------------------------+
|  ⚡ BidMatrix Pro  |  Tender Ref: PROC/SMBBIT/(PP-01)/2026-27  |  EPADS: S-26090824871              [⚙ Settings] [Help]  |
+----------------------------------------------------------------------------------------------------------------------+
|  [📁 Import BOQ]  [📄 Upload Vendor PDFs]  [⚡ Run Auto-Analysis]  [📥 Export Excel / CSV]      [ Search Item / Firm... ]|
+----------------------------------------------------------------------------------------------------------------------+
|  DASHBOARD METRICS:                                                                                                  |
|  [ 90 Total Items ]    [ 4 Participating Bidders ]    [ 271 Quoted Bids ]    [ 12 Committee Awarded ]    [ 1 Alert ] |
+----------------------------------------------------------------------------------------------------------------------+
|  COMPARATIVE EVALUATION GRID (FCS View):                                                                             |
|  Filter by: [ All Items v ]  [ Lowest Bidders Only v ]  [ Show Disqualified Firms [x] ]                              |
|                                                                                                                      |
|  Code     | Item Description            | Qty    | UOM   | Firm Name               | Quoted Spec    | Rate   | Total  | Rank |
|  ---------+-----------------------------+--------+-------+-------------------------+----------------+--------+--------+------|
|  Pri0002  | White Confidential Medical  | 20,000 | Nos.  | M/s. Prince Enterprises | 350 GSM Bleach |  50.00 | 1.00M  | 1st★ |
|  Pri0002  | White Confidential Medical  | 20,000 | Nos.  | M/s. Haq Enterprises    | 300 GSM Bleach |  54.00 | 1.08M  | 2nd  |
|  Pri0002  | White Confidential Medical  | 20,000 | Nos.  | M/s. Ettihad Traders    | Bleach Card    |  43.80 | 876.0K | Disq |
|  ---------+-----------------------------+--------+-------+-------------------------+----------------+--------+--------+------|
|  Pri0003  | Physician Order Sheet       | 90,000 | Sheet | M/s. Prince Enterprises | 70 GSM Local   |   2.38 | 214.2K | 1st★ |
|  Pri0003  | Physician Order Sheet       | 90,000 | Sheet | M/s. Haq Enterprises    | 68 GSM Form    |   2.50 | 225.0K | 2nd  |
|  Pri0003  | Physician Order Sheet       | 90,000 | Sheet | M/s. Z.S Traders        | Standard Paper |   5.20 | 468.0K | 3rd  |
+----------------------------------------------------------------------------------------------------------------------+
|  STATUS BAR: Ready | SQLite Database Synced | All 4 PDF Quotations Processed in 4.2s | Audit Log: 0 Mismatches Unresolved   |
+----------------------------------------------------------------------------------------------------------------------+
```

### Color Palette Tokens:
- **Primary Canvas**: `#F8FAFC` (Slate Ultra Light)
- **Card & Grid Surface**: `#FFFFFF` (Pure White with subtle `#E2E8F0` borders)
- **Primary Brand / Action**: `#0284C7` (Sky Blue 600)
- **Winner / Rank 1 Highlight**: `#DCFCE7` (Emerald Soft Green) with `#15803D` text & gold star badge `★`
- **Runner Up (Rank 2)**: `#F1F5F9` (Slate 100)
- **Disqualification / Warning**: `#FEE2E2` (Rose Soft Red) with `#B91C1C` alert indicator
- **Typography**: Inter / Outfit modern sans-serif typography for maximum numerical legibility.

---

## 🛠️ Technology Stack & Dependencies

| Layer | Component | Selected Technology | Rationale |
|---|---|---|---|
| **Frontend UI** | Desktop Framework | **Flutter Desktop (Windows C++)** | Native 60 FPS, multi-window support, pixel-perfect rendering |
| **Local Storage** | Database Engine | **SQLite 3 (`sqflite_common_ffi`)** | Zero configuration, self-contained, lightning fast ACID transactions |
| **PDF Extraction** | Vector Text Stream | **`syncfusion_flutter_pdf` / `pdfx`** | High-fidelity coordinate extraction for digital text PDFs |
| **OCR Pipeline** | Scanned PDF Parser | **Embedded High-Speed ONNX Engine** | Instant page-level OCR on Windows without cloud latency |
| **Export Engine** | Comparative Exporter | **`csv` & `excel` / `openpyxl`** | 100% formatting fidelity matching standard institutional templates |

---

## 📅 Implementation Roadmap

```mermaid
gantt
    title BidMatrix Pro Implementation Schedule
    dateFormat  YYYY-MM-DD
    section Phase 1: Foundation
    Database Schema & SQLite Engine       :done,    des1, 2026-10-11, 2d
    Master BOQ Template Importer           :active,  des2, after des1, 2d
    section Phase 2: PDF & OCR
    Digital PDF Extraction Service         :         des3, after des2, 3d
    Scanned OCR Preprocessing Pipeline     :         des4, after des3, 3d
    Data Harmonization & Regex Matcher     :         des5, after des4, 2d
    section Phase 3: Core Logic
    Ranking & Tie-Breaking Engine          :         des6, after des5, 2d
    Bid Security & Sanity Checker          :         des7, after des6, 2d
    section Phase 4: UI & Desktop Polish
    Responsive Grid & Executive Dashboard  :         des8, after des7, 3d
    CSV / Excel Export with 9-Column FCS   :         des9, after des8, 2d
    End-to-End Tender Audit Verification  :         des10, after des9, 2d
```

---

## ⚖️ Audit Integrity & Compliance Notice
This software is engineered to assist Procurement Committees, Evaluation Officers, and Finance Directors in assembling Financial Comparative Statements in compliance with Public Procurement Regulatory Frameworks. All extracted records, OCR tokens, and ranking determinations maintain full, verifiable traceability to the original uploaded PDF documents.
