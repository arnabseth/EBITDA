# EBITDA 360 - Technical Proposal
## Complete Information Extract

**Document Version:** v1.3  
**Company:** TCG Digital Solutions LLC  
**Project:** EBITDA 360

---

## TABLE OF CONTENTS

1. [Project Overview](#1-project-overview)
2. [Our Understanding of the Scope](#2-our-understanding-of-the-scope)
3. [Value Proposition](#3-value-proposition)
4. [Technical Approach](#4-technical-approach)
5. [Assumptions, Dependencies and Exclusions](#5-assumptions-dependencies-and-exclusions)

---

## 1. PROJECT OVERVIEW

### Project Objective

The goal of this project is to create a clear and easy-to-understand method to calculate the company's daily and monthly EBITDA. This will help align sales, costs, and margins across the organization. It will also make it easier to track what drives profitability in the future.

### Current Pain Points

#### 1. Manual and Fragmented Data Handling
- Sales, procurement, and expense data come from multiple sources like SAP, finance systems, and procurement files.
- Heavy use of manual spreadsheet entries increases the risk of errors and delays.

#### 2. Delayed Visibility of Profitability
- EBITDA is calculated after multiple reconciliations.
- Management often gets delayed profitability insights, which limits proactive decision-making.

#### 3. Lack of Real-Time View
- No automated link exists between sales realization, raw material consumption, and margin impact.
- Teams cannot see day-to-day margin changes quickly.

#### 4. Limited Product-Level Insights
- Profitability is tracked at an aggregate level in Excel.
- It is difficult to drill down into product-wise or SKU-wise EBITDA drivers.

#### 5. Cumbersome Month-End Closing
- Reconciliations between sales, procurement, and finance are effort-intensive and slow down month-end closing.
- This increases pressure on finance teams and delays reporting to leadership.

---

## 2. OUR UNDERSTANDING OF THE SCOPE

The project scope covers five main modules:

### MODULE 1: SALES AND REALIZATION

#### Brief Understanding
- Tracks domestic, export, and waste/by-product sales.
- Adjusts for discounts, freight, commission, and Sales & Distribution (S&D) expenses.
- Provides Net Sales visibility at product and SKU levels.

#### Logic

```
Total Sales = Sales Value Domestic + Sales Value Export + Waste Sale/Others

Sales and Distribution Expenses = Outward Freight + Ocean Freight + Commission 
                                 + Cash Discount + Rate Discount + Internal Freight 
                                 + Warehouse charges

Net Sales = Total Sales - Sales and Distribution expenses
```

#### Outcome
**Net Sales = Total Sales - Sales and Distribution expenses**

#### Dependency
Availability of Sales Qty Domestic, Sales Value Domestic, Sales Qty Export, Sales Value Export, and Waste Sale data in SAP on a daily or nearly daily basis is required.

#### Total Sales Details
- Represents the **gross revenue** generated from sales before deducting any selling or distribution-related expenses.
- Includes:
  - **Sales Quantity (Domestic & Export)** - measured in MT (metric tons)
  - **Sales Value (Domestic & Export)** - measured in ₹ Crore
- **Data Source:** Primarily SAP
- **Breakdown:** Sales are divided by product categories such as **Chips, POY, FDY, DTY, ACY, ATY, DW, SZ, Mono, Others**
- **Key Insight:** Total Sales gives the top-line revenue without considering logistics, freight, commission, or trade-related costs.

#### Net Sales Details
- Refers to Total Sales after deducting direct selling and distribution expenses.
- **Key deductions include:**
  - Outward Freight (logistics for domestic movement)
  - Ocean Freight (for exports)
  - Commission (distributor/agent commission, invoice-linked)
- **Data Source:** Mix of SAP, TBS, and Manual Inputs
- **Formula:**
  ```
  Net Sales = Total Sales - (Outward Freight + Ocean Freight + Commission 
            + Cash Discount + Rate Discount + Internal Freight + Warehouse charges)
  ```
- **Note:** Internal freight is freight between Jolwa, Karala and Hazira
- **Key Insight:** Net Sales represents the realized revenue after deductions and is a more accurate measure of effective sales contribution to profitability.

---

### MODULE 2: RAW MATERIAL AND MELT

#### Brief Understanding
- Captures PTA, MEG, and other raw material procurement costs.
- Calculates Melt = Total Raw Material consumption cost.

#### Logic
```
Melt = PTA + MEG/Other Raw Materials cost including Conversion cost 
       (Procured RM (Chips, spandex - Other than PTA & MEG))
```

#### Outcome
**Gross Margin = Net Sales - Melt**

#### Dependency
BOM (Bill of Materials) for consumption mapping along with last cost price for all raw material will be available in SAP.

#### Important Notes
- **Since RM other than PTA & MEG are also considered here, they should not be considered again in Variable Conversion Cost.**
- **By Melt Cost we mean PTA & MEG consumption only. It does not include any other raw material nor any conversion cost.**

#### Raw Material Details
- **Key Inputs:** PTA (Purified Terephthalic Acid), MEG (Mono Ethylene Glycol)
- **Measurement:** Cost is captured in ₹ Crore
- **Source:** Entered manually (not automated from SAP)
- **Role:** These are the primary feedstocks for polyester manufacturing. Their cost fluctuations directly impact the profitability model.

#### Melt Details
- Melt represents the **combined cost of PTA + MEG**
- **Formula:** Melt = PTA + MEG
- **Unit:** ₹ Crore
- **Significance:**
  - Forms the base raw material cost pool used to calculate production cost for downstream products (Chips, POY, FDY, DTY, ACY, ATY, etc.)
  - It is the **largest cost driver** in polyester production

#### Gross Margin Details
- Gross Margin reflects the profitability after subtracting raw material costs (Melt) from Net Sales
- **Formula (conceptual):** Gross Margin = Net Sales - Melt Cost
- **Breakdown:**
  - Calculated across different product categories
  - Expressed both in absolute value (₹ Crore) and potentially in percentage terms (% of sales)
- **Purpose:** Helps assess how much value addition is being generated over the cost of raw materials

---

### MODULE 3: VARIABLE CONVERSION COST AND CONTRIBUTION

#### Brief Understanding
- Tracks variable production costs: power, heating, chemicals, packaging, consumables, etc.
- Links to operational metrics (e.g., energy per MT, packaging per MT).
- Enables variance analysis against benchmarks.

#### Logic
```
VCC = Power + Heating + Antimony + Additives & chemicals + Packing materials 
    + Procured RM (Chips, spandex - Other than PTA & MEG) + NRV 
    + Inventory Cost + Other VCC
```

#### Outcome
**Contribution = Gross Margin - Variable Conversion Cost**

#### Dependency
- Power factor and Merge factor to be available Plant-wise and SKU-wise in SAP for computation of power and heat.
- Additives, chemicals and packing material to be present in BOM consumption mapping along with rate in SAP.

#### Variable Conversion Cost (VCC) Details
Variable Conversion Cost represents the processing cost needed to convert Melt (PTA + MEG) into finished products such as Chips, POY, FDY, DTY, ACY, ATY, etc.

**Nature of Cost:**
- These are **variable costs** that increase or decrease with production volume
- Typical inclusions could be:
  - Power & fuel consumption
  - Catalysts, chemicals, packaging materials
  - Direct labor linked to output
  - Plant-level processing overheads that vary with throughput

#### NRV and Inventory Cost
- **NRV is defined as:** the gross selling price of a SKU in the ordinary course of business, less the expected costs necessary to complete and sell the asset and S&D expenses
- **Formula:**
  ```
  NRV = Gross Selling Price - Costs to Produce - S&D expenses - Costs to Sell
  ```
- Inventory Cost will be calculated for EBITDA
- **NRV is the net selling price of finished goods of the next two days of the reporting date as the reporting day would take place on T+2.**
- **If NRV is lower than COGS of an SKU**, a provision for NRV Loss (NRV - COGS) needs to be made in the Daily EBITDA for that SKU.
- Next day, the same loss needs to be reversed in the Daily EBITDA calculation and further new provision to be made (if any) in the similar way.

**Source in File:** Likely from SAP or plant cost reports, depending on expense type.

**Role in Profitability Chain:**
- Applied after Melt cost to reflect the true cost of producing finished goods
- Essential for evaluating process efficiency and cost optimization at the manufacturing stage

#### Contribution Details
- Contribution is the margin left after deducting both Raw Material (Melt) cost and Variable Conversion Cost from Net Sales
- **Formula (Conceptual):** Contribution = Gross Margin - (Variable Conversion Cost)
- **Breakdown:**
  - Calculated product-wise across categories (Chips, POY, FDY, DTY, etc.)
  - Expressed in absolute value (₹ Cr.) and percentage terms (% of sales)
- **Purpose:** Provides a clear measure of profitability contribution from each product line after accounting for both feedstock cost and conversion expenses

---

### MODULE 4: FACTORY CONVERSION COST AND MARGIN AT JOLWA

#### Brief Understanding
- Captures fixed and semi-variable manufacturing overheads: Manpower, maintenance, spares, consumables, etc.
- Allocates costs across products and SKUs, wherever applicable, based on defined drivers.
- Provides insights into plant-level efficiency and cost allocation.

#### Logic
```
FC = Stores & repairs + Manpower (Payroll) + Contract Manpower 
   + Insurance + Overheads
```

#### Outcome
**Margin at Jolwa = Contribution - FC**

#### Dependency
- **Stores and repairs:** Daily consumption to be available in SAP
- **Manpower (Payroll and Contract), Overheads** will be based on monthly average data from SAP
- **COGS (Cost of Goods Sold) and COGM (Cost of Goods Manufactured)** will be derived from SAP

#### Factory Conversion Cost (FC Cost) Details
Factory Conversion Cost represents the fixed and semi-variable costs at the plant to convert raw material (Melt) into finished goods. Unlike Variable Conversion Cost (VCC), these costs are more plant overhead-driven and less sensitive to production volume in the short term.

**Components (based on typical scope of FC costs in polyester manufacturing):**
- Plant-level utilities and overheads
- Salaries & wages of plant operations staff
- Maintenance & repairs of equipment
- Depreciation and insurance for plant machinery
- Other factory-related indirect costs

**Data Source in File:** Drawn from SAP, manual inputs, or TBS (in SAP)

**Purpose:** Provides visibility into factory efficiency and cost absorption at the Jolwa plant.

#### Margin at Jolwa Details
Margin at Jolwa refers to the profitability after considering Net Sales, Raw Material (Melt), Variable Conversion Cost, and Factory Conversion Cost specific to the Jolwa plant.

- **Formula:**
  ```
  Margin at Jolwa = Net Sales - (Melt Cost + Variable Conversion Cost 
                  + Factory Conversion Cost)
  ```
- **Breakdown:**
  - Calculated at the product-level (Chips, POY, FDY, DTY, ACY, ATY, etc.) but consolidated to reflect Jolwa plant performance
  - Expressed in absolute value (₹ Crore) and percentage terms (% of Net Sales)
  
- **Significance:**
  - Provides a plant-specific lens for Jolwa
  - Helps identify whether Jolwa operations are generating adequate returns after covering both variable and fixed factory expenses

---

### MODULE 5: CORPORATE EXPENSE

#### Brief Understanding
- Integrates payroll, admin, and overhead expenses.
- Includes corporate income adjustments.
- Provides transparency in allocation methodology (last month avg, % of sales, etc.)

#### Logic
```
Corporate Expense = Corporate Expense Payroll + Corporate Expense Income 
                  + Corporate Expense for Others
```

#### Outcome
**EBITDA = Margin at Jolwa - Corporate Expense**

#### Dependency
Previous month's Corporate expense to be fetched from SAP for all components of Corporate expense.

#### Corporate Expense Details
Corporate Expenses represent the overheads and indirect costs at the corporate/head office level that are not directly linked to a specific plant or product line but are necessary for running the business.

- **Components:** Corporate Expense Payroll, Corporate Expense Others, Corporate Income
- **Data Source in File:** Likely based on SAP or manual entries, consolidated at the corporate level
- **Purpose:** These expenses are fixed in nature and must be absorbed from the margins generated at the factory level (Jolwa)

#### EBITDA Details
**EBITDA** stands for **Earnings Before Interest, Taxes, Depreciation, and Amortization**.

It measures the operational profitability of the business, excluding non-operating and non-cash expenses.

- **Formula:** 
  ```
  EBITDA = Margin at Plant Level (Jolwa) - Corporate Expenses
  ```
- **Purpose:** Shows the true operating performance of the company

#### Key Outcomes
- EBITDA calculation will provide an **End-to-end view** of:
  ```
  Gross Margin → Contribution → Margin (Jolwa) → EBITDA
  ```
- It will provide **per kg profitability** (Sales/kg, Contribution/kg, Margin/kg, EBITDA/kg)
- The application will provide **Product-wise, SKU-wise profitability with drill-down**, wherever applicable

---

## 3. VALUE PROPOSITION

The EBITDA 360 solution offers the following key benefits:

### 1. Single Source of Truth for EBITDA
Consolidates data from SAP and manual inputs into one integrated platform.

### 2. Near Real-Time Visibility
- Provides daily and monthly EBITDA views at product/category/plant levels.
- Enables management to spot deviations early (like rising freight cost, raw material impact).

### 3. Accuracy and Transparency
- Automates calculation rules (Net Sales → Gross Margin → Contribution → EBITDA).
- Reduces human error, manual overrides, and inconsistent formula usage.

### 4. Efficient and Time Savings
Cuts down manual data preparation effort for Finance and Business Controllers.

### 5. Decision Support
CXOs get actionable insights for pricing, cost optimization, and sales strategies.

---

## 4. TECHNICAL APPROACH

### SOLUTION ARCHITECTURE

The EBITDA 360 solution uses the **tcgmcube platform** with the following architecture:

#### Data Lake Map and Data Servicing Layer
Provides centralized data management and serving capabilities.

#### Ingest Layer
- Pentaho
- Apache Spark
- Kafka

#### Store Layer
- Elasticsearch
- Greenplum Database
- Neo4j

#### Analyze Layer
- tcgmcube Advanced Analytics
- Apache Spark

#### Visualize Layer
- tcgmcube Visualization Screens
- Elasticsearch
- Pentaho Report writer

#### System & Data Management
- Data Profiling
- Auditing
- tcgmcube System & Data Management
- MariaDb
- Metadata Management
- Access Control
- App Admin
- Sys Admin
- License Management
- MDM (basic)

---

### HIGH LEVEL FLOW DIAGRAM

#### Data Sources
1. **SAP**
2. **Manual Webform**

#### Data Ingestion
Data ingestion via API, ETL, file upload, RFC, etc.

#### Central Database
**EBITDA 360 database**

#### Rules and Validation Engine
Validates and processes data according to business rules.

#### EBITDA 360 Orchestration Service
Coordinates the calculation flow across all modules.

#### Module Processing Flow
```
1. Sales and Realization → Net Sales
2. Raw Material and Melt → Gross Margin
3. Variable Conversion Cost → Contribution
4. Factory Conversion Cost → Margin at Jolwa
5. Corporate Expense → EBITDA
```

---

### LANDING SCREEN (ILLUSTRATIVE)

The Landing page (L0) will give a daily view of all components catering to the EBITDA for all product categories like **Chips, POY, FDY, DTY, ACY, ATY, DW, SZ, Mono, Others**.

#### Sample Structure

| Sr No. | Particulars | UOM | Chips | POY | FDY | DTY | ACY | ATY | DW | SZ | Mono | Others |
|--------|-------------|-----|-------|-----|-----|-----|-----|-----|----|----|----- |--------|

#### Key Sections
- **A.** Sales Total Value
- **B.** Sales & Distribution Expenses
- **C.** Net Sales (A-B)
- **D.** Melt
- **E.** Gross Margin/spread (C-D)
- ...continuing through...
- **J.** Subtotal corporate
- **K.** EBITDA (I-J)

#### Per kg metrics
- Sales per kg (C/Sales Qty)
- Contribution per kg (G/Sales Qty)
- Margin per kg (I/Sales Qty)
- EBITDA per kg (K/Sales Qty)

**On click of any particulars for a Product Category**, a detailed screen (L1) will be available to give details for the particular like Sales Qty Domestic for all SKUs within that Product Category.

---

## 5. ASSUMPTIONS, DEPENDENCIES AND EXCLUSIONS

### ASSUMPTIONS

#### 1. Data Availability
- All required inputs (Sales, Freight, Raw Material Costs, Corporate Expenses, etc.) will be available in structured digital format.
- The EBITDA calculation will be shown at **Current Date - 2** basis.

#### 2. Business Rules
Standard EBITDA and related calculation logic is agreed and will not change frequently.

#### 3. System Integration
- Integration with SAP and other systems like S&OP will be done via API, RFC function.
- **GS IT team** will be owners of development within the SAP system.
- **TCG Digital** will integrate with the corresponding RFC functions to fetch data from SAP.

#### 4. Data fetch from SAP
Data to be fetched based on creation date in SAP but reporting will be on Posting date.

#### 5. COGS and COGM
- COGS will be calculated from COGM available in SAP + current cost of manufacturing of specific SKU.
- **Formula:**
  ```
  COGS = Opening Inventory value + Current cost of manufacturing of specific SKU
       = Opening Inventory + COGM
  ```

#### 6. S&OP Integration
- The EBITDA application will fetch required information from S&OP application and will calculate the projected EBITDA.
- The **projected feature will be delivered after Go-Live of S&OP module**.

#### 7. Dashboard and Training
- One EBITDA Dashboard will be provided, including a drill-down page at SKU level with excel download option.
- Two training sessions have been considered during UAT stage.

---

### DEPENDENCIES

#### 1. Data Flow
Timely extraction and upload of raw material cost, VCC, FC Conversion, and Corporate Expenses.

#### 2. Human Input
Manual updates (e.g. Ocean Freight, Corporate Expenses) need to be validated and approved before final EBITDA computation.

#### 3. Source System
- SAP will be the primary source of data.
- Manual webform will be provided where SAP is not updated as per expectation.

---

### EXCLUSIONS

1. **SAP write back is not in scope.**

2. The solution focuses on actual and daily/monthly reporting. **Demand forecasting or scenario modeling is excluded.**

3. Cleaning or correcting legacy ERP/Excel data is not included. **Only provided inputs are assumed correct.**

4. **Warranty Support is excluded** for the application.

5. **Performance testing has been excluded**, as this is a reporting-only solution.

6. **VAPT has not been considered**, as this is for intranet usage only.

---

## ABOUT TCG DIGITAL

**TCG Digital** is the digital & AI arm of The Chatterjee Group (TCG), a multi-billion dollar conglomerate with a diverse portfolio in Pharmaceuticals, Biotech, Petrochemicals, and Real Estate across the US, EU, and South Asia.

### Our umbrella includes companies such as:
- LabVantage
- Lummus Technology
- TCG LifeSciences

### Mission
At TCG Digital, we are driven by our mantra of delivering **"Velocity to Value"**, helping enterprises transform faster and smarter.

### Platform
Our AI Analytics platform **tcgmcube** is at the heart of these transformations. We enable organizations to unlock the full potential of their data. By seamlessly integrating AI/ML capabilities into their business processes, we empower businesses to accelerate their digital transformation journey, enhancing agility and driving impactful results.

---

## CONTACT INFORMATION

**TCG Digital Solutions LLC**

📍 265 Davidson Ave, Suite 220  
Somerset New Jersey 08873

📞 Phone: +1 (732) 515-7376  
📧 Email: contact@tcgdigital.com  
🌐 Website: www.tcgdigital.com

---

**Document created from:** EBITDA_Technical_Proposal_v1_3.pdf  
**TCG Digital Solutions LLC. Confidential. All Rights Reserved**
