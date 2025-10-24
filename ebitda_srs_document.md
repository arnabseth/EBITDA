# Requirements Specification Document for mcube Solution

## Daily EBITDA Calculation and Reporting System

**For GSMPL (Textile Manufacturing)**

**For TCG Digital**

**Version 1.0**

**TCG Digital Solutions Private Limited**
**INDIA**

---

## A. Table of Contents

1. [Introduction](#1-introduction)
   - 1.1 [Purpose](#11-purpose)
   - 1.2 [Intended Audience and Reading Suggestions](#12-intended-audience-and-reading-suggestions)
   - 1.3 [Project Scope](#13-project-scope)
   - 1.4 [Scope Exclusions](#14-scope-exclusions)
   - 1.5 [Success Criteria](#15-success-criteria)
   - 1.6 [References](#16-references)

2. [Functional Requirements](#2-functional-requirements)
   - 2.1 [Sales and Net Sales Module](#21-sales-and-net-sales-module)
   - 2.2 [Melt (PTA & MEG) Module](#22-melt-pta--meg-module)
   - 2.3 [Variable Cost Contribution (VCC) Module](#23-variable-cost-contribution-vcc-module)
   - 2.4 [Fixed Cost Conversion Module](#24-fixed-cost-conversion-module)
   - 2.5 [Corporate Expenses Module](#25-corporate-expenses-module)
   - 2.6 [Data Ingestion](#26-data-ingestion)

3. [Usage Scope](#3-usage-scope)

4. [Nonfunctional Requirements](#4-nonfunctional-requirements)
   - 4.1 [Performance Requirements](#41-performance-requirements)
   - 4.2 [Supportability Requirements](#42-supportability-requirements)

5. [Assumptions / Constraints](#5-assumptions--constraints)

6. [Other Requirements](#6-other-requirements)

7. [Appendix A: Glossary](#7-appendix-a-glossary)

8. [Appendix B: Assumptions and Dependencies](#8-appendix-b-assumptions-and-dependencies)

9. [Appendix C: Issues List](#9-appendix-c-issues-list)

10. [Appendix D: Business Risks](#10-appendix-d-business-risks)

---

## B. Version History

| Revision | Date | Created By | Approved By | Short Description of Changes |
|----------|------|------------|-------------|------------------------------|
| 1.0 | [Date] | [Name] | [Name] | Initial version of approved document |

---

# 1. Introduction

## 1.1 Purpose

This document specifies the requirements for the Daily EBITDA Calculation and Reporting System for GSMPL textile manufacturing operations. The system will provide accurate, real-time margin analysis at SKU level to support management decisions in a multi-SKU manufacturing environment.

The application will calculate and track daily EBITDA by integrating data from SAP, TBS (Transaction Booking System), IGMS (Integrated Gate Management System), and manual entries. It will provide transparency, accuracy, and granularity down to SKU level for profitability analysis.

## 1.2 Intended Audience and Reading Suggestions

This document is intended for:

- Plant managers and operations teams who need daily insight into margins and costs
- Corporate finance teams monitoring overall profitability
- Business heads and leadership for decision making
- Development teams building the solution
- Quality assurance teams testing the system
- Project managers coordinating implementation

The document is organized to first present the business context and functional requirements, followed by technical specifications. Business stakeholders should focus on sections 1-3, while technical teams should review all sections.

## 1.3 Project Scope

The Daily EBITDA Calculation and Reporting System will:

- Calculate and track daily EBITDA at SKU level for Jolwa Plant
- Integrate data from SAP, TBS, IGMS, and manual entries
- Support more than 500 SKUs with plant-wise and product-wise breakdown
- Provide daily, month-to-date, and T-2 (two days lag) management dashboards
- Enable SKU-wise, product-wise, and plant-wise profitability analysis
- Support cost forecasting and price optimization during market fluctuations
- Calculate metrics including Total Sales, Net Sales, Contribution, Margin, and EBITDA
- Allocate fixed costs, variable costs, sales and distribution expenses across SKUs

The system will be built on the tcgmcube platform and will leverage its business intelligence, data ingestion, and reporting capabilities.

## 1.4 Scope Exclusions

The following items are explicitly excluded from this release:

- Vareili Plant is out of scope for initial release
- Projected EBITDA feature will be delivered post Go-Live of S&OP module
- Automated procurement rate updates (will be handled manually until month-end)
- Real-time inventory valuation (will use weighted average from SAP)
- Historical data migration beyond current fiscal year
- Integration with systems other than SAP, TBS, and IGMS

## 1.5 Success Criteria

The project will be considered successful when:

- Accurate, daily SKU-level EBITDA calculation and reporting is achieved
- Manual interventions are minimized through direct system integration
- Response time for dashboard loading is under 5 seconds
- Effective margin optimization and anomaly detection in cost structures
- User adoption by plant managers, finance teams, and corporate management
- 95% data accuracy when compared with month-end financial closing
- System processes data for 500+ SKUs within defined timeframes

## 1.6 References

- BA Instruction for AI.docx
- Daily EBITDA Format.xlsx
- GSMPL EBITA Understanding Doc 09092025.xlsx
- SOP_EBIDTA Calculation Sheet 1 1.xlsx
- Other Inputs.pdf
- tcgmcube Business Intelligence Manual v5.3
- SAP Material Master Documentation
- IGMS System Documentation

---

# 2. Functional Requirements

## 2.1 Sales and Net Sales Module

| Use Case ID | UC-001 |
|-------------|---------|
| **Use Case Name** | Calculate Sales and Net Sales |
| **Primary Actor** | System (Automated) |
| **Pre-Conditions** | - SAP system is available and accessible<br>- Sales data has been posted in SAP<br>- Invoice-level data is complete |
| **Basic Process Flow** | 1. System retrieves sales data from SAP daily<br>2. System captures Domestic Sales (quantity and value)<br>3. System captures Export Sales (quantity and value)<br>4. System captures Waste Sales and Others using waste codes<br>5. System adjusts for Sales Returns on actual return date<br>6. System fetches Dealer Commission at invoice level<br>7. System calculates Price/Rate Differences per SKU<br>8. System retrieves Distribution Expenses (Outward Freight, Ocean Freight, Commission, Cash Discount, Rate Discount, Internal Freight, Warehouse Charges)<br>9. System calculates Total Sales = (Sales Value Domestic + Sales Value Export) - Sales Returns Value - Dealer Commission ± Price/Rate Differences<br>10. System calculates Net Sales = Total Sales - Sales Distribution Expenses<br>11. System stores results with date and SKU mapping |
| **Alternate Flow** | 1. If SAP data is unavailable<br>2. System logs error<br>3. System sends notification to administrator<br>4. System uses last available data with warning flag |
| **Acceptance Criteria** | - Sales data is retrieved from SAP daily without manual intervention<br>- All SKUs have sales values mapped correctly<br>- Distribution expenses are allocated to correct SKUs<br>- Net Sales calculation matches formula specification<br>- Data is available for reporting within 2 hours of SAP posting<br>- Drill-down capability available by SKU, product, date, and plant |

---

## 2.2 Melt (PTA & MEG) Module

| Use Case ID | UC-002 |
|-------------|---------|
| **Use Case Name** | Calculate Melt Cost and Gross Margin |
| **Primary Actor** | System (Automated) / Finance User (Manual Rate Entry) |
| **Pre-Conditions** | - BOM (Bill of Materials) is configured in system<br>- Daily production plan is available<br>- PTA/MEG rates are entered or available |
| **Basic Process Flow** | 1. System retrieves daily production output by SKU<br>2. System fetches BOM consumption factor for each SKU<br>3. System calculates PTA/MEG consumption = SKU Output × BOM Factor<br>4. System uses last manual entry of PTA rate for daily costing<br>5. Finance User updates PTA/MEG rates as needed<br>6. At month-end, system adjusts with actual procurement rates<br>7. System calculates Melt Cost = PTA Consumption × PTA Rate + MEG Consumption × MEG Rate<br>8. System includes Other Raw Materials cost and Conversion cost<br>9. System calculates Gross Margin = Net Sales - Melt Cost<br>10. System stores results with SKU and date mapping |
| **Alternate Flow** | 1. If BOM is not configured for a SKU<br>2. System flags error<br>3. System notifies administrator<br>4. User must configure BOM before calculation proceeds |
| **Acceptance Criteria** | - BOM-based consumption mapping works for all SKUs<br>- Manual rate entry interface is user-friendly<br>- System maintains rate history<br>- Gross Margin calculation is accurate<br>- Month-end adjustment process works correctly<br>- Impact of raw material volatility is tracked |

---

## 2.3 Variable Cost Contribution (VCC) Module

| Use Case ID | UC-003 |
|-------------|---------|
| **Use Case Name** | Calculate Variable Costs and Contribution |
| **Primary Actor** | System (Automated) |
| **Pre-Conditions** | - Production data is available<br>- Material master rates are updated in SAP<br>- Plant-wise correction factors are configured<br>- Merge factors are defined |
| **Basic Process Flow** | 1. System calculates Power Cost (retrieves plant and production line data, applies rate and quantity norms, multiplies with plant-wise correction factors)<br>2. System calculates Heating Cost (applies Merge Factor per SKU, calculates required heating quantity, costs at latest manual rate)<br>3. System calculates Antimony, Additives, Chemicals cost (retrieves quantity from BOM/production plans, fetches rates from SAP material master, calculates cost per SKU)<br>4. System calculates Packing Material cost (uses BOM and production data, applies rates from material master)<br>5. System calculates Procured RM - Chips & Spandex (uses last cost from SAP material master, multiplies by actual usage)<br>6. System calculates NRV = Estimated Selling Price - Costs to Produce - Costs to Sell<br>7. System calculates Inventory Cost for EBITDA<br>8. System allocates Other VCC (uses plant-wise consumable costs, splits using merge factors for SKU-wise allocation)<br>9. System calculates Total VCC = Power + Heating + Antimony + Additives + Chemicals + Packing + Procured RM + NRV + Inventory Cost + Other VCC<br>10. System calculates Contribution = Gross Margin - Total VCC<br>11. System provides audit trails for all variable costs |
| **Alternate Flow** | 1. If material master rate is missing<br>2. System uses last available rate<br>3. System flags for user review<br>4. System sends notification |
| **Acceptance Criteria** | - All VCC components are calculated daily<br>- SKU-level allocation is accurate<br>- Audit trails are maintained<br>- Power calculation uses correct plant factors<br>- BOM mapping works for all materials<br>- Contribution margin is calculated correctly |

---

## 2.4 Fixed Cost Conversion Module

| Use Case ID | UC-004 |
|-------------|---------|
| **Use Case Name** | Calculate Fixed Costs and Margin at Jolwa |
| **Primary Actor** | System (Automated) / HR/Admin User (Manual Entry) |
| **Pre-Conditions** | - Merge factors are configured<br>- Production distribution logic is defined<br>- Payroll data is available<br>- Contract manpower data is available from IGMS and SAP |
| **Basic Process Flow** | 1. System retrieves Stores & Repairs data from SAP<br>2. System retrieves Manpower Payroll (Manual entry) with adjustment for leaves and overtime<br>3. System retrieves Contract Manpower from IGMS + SAP<br>4. System retrieves Insurance data (Manual entry) using Option 1 (Upfront allocation per planned sales quantity) or Option 2 (Daily spread using recurring monthly journals)<br>5. System retrieves Overheads (Manual/historical entry)<br>6. System allocates fixed costs to SKUs using merge factors and production distribution logic<br>7. System calculates Total Fixed Cost Conversion = Stores & Repairs + Manpower Payroll + Contract Manpower + Insurance + Overheads<br>8. System retrieves Internal Shifting Cost of SFG (uses COGM, accounts for opening stock from SAP, maintains weighted average COGS daily)<br>9. System calculates Margin at Jolwa = Contribution - Total Fixed Cost Conversion - Internal Shifting Cost of SFG<br>10. System provides daily control and transparency |
| **Alternate Flow** | 1. If IGMS data is unavailable<br>2. System uses SAP contract manpower data only<br>3. System sends notification<br>4. User must verify and reconcile later |
| **Acceptance Criteria** | - All fixed cost components are captured<br>- SKU allocation uses correct merge factors<br>- Payroll adjustments work correctly<br>- Insurance allocation follows chosen method<br>- Internal shifting costs are accurate<br>- Margin at Jolwa is calculated correctly<br>- Jolwa plant profitability is trackable |

---

## 2.5 Corporate Expenses Module

| Use Case ID | UC-005 |
|-------------|---------|
| **Use Case Name** | Calculate Corporate Expenses and EBITDA |
| **Primary Actor** | Finance User |
| **Pre-Conditions** | - Corporate cost centers are defined<br>- Last month/quarter averages are available |
| **Basic Process Flow** | 1. User enters Corporate Payroll expenses<br>2. User enters Other Corporate Expenses<br>3. User enters Corporate Income<br>4. System allocates using last month/quarter averages<br>5. System groups under four cost centers<br>6. System provides option to bucket as "Others" or dedicated reporting heads<br>7. System calculates Total Corporate Expenses = Corporate Expense Payroll + Corporate Expense Others - Corporate Income<br>8. System calculates EBITDA = Margin at Jolwa - Total Corporate Expenses<br>9. System segregates headquarters costs from operational plant costs<br>10. System enables clean plant performance tracking |
| **Alternate Flow** | 1. If corporate expense data is not entered<br>2. System uses last available data<br>3. System flags for user update<br>4. System displays warning on reports |
| **Acceptance Criteria** | - Corporate expense entry interface is simple<br>- Allocation logic works correctly<br>- EBITDA calculation is accurate<br>- Plant and corporate costs are segregated<br>- Historical comparison is available |

---

## 2.6 Data Ingestion

### Data Sources and Integration:

1. **SAP System**:
   - Sales data (domestic, export, waste)
   - Material master rates
   - Stores and repairs
   - Production data
   - COGM and inventory data
   - Contract manpower (partial)
   - Frequency: Daily automated extraction

2. **TBS (Transaction Booking System)**:
   - Transaction-level booking details
   - Frequency: Daily automated extraction

3. **IGMS (Integrated Gate Management System)**:
   - Contract manpower attendance and costs
   - Frequency: Daily automated extraction

4. **Manual Entries**:
   - PTA/MEG rates
   - Manpower payroll
   - Insurance
   - Overheads
   - Corporate expenses
   - Frequency: As needed / Monthly

### Data Ingestion Requirements:

- System must validate data quality before processing
- System must log all data extraction activities
- System must handle missing or incomplete data gracefully
- System must maintain data lineage and audit trail
- System must process data within 2 hours of availability
- System must provide data ingestion status dashboard

---

# 3. Usage Scope

## User Roles and Permissions:

### Plant Manager:
- View daily EBITDA reports for Jolwa Plant
- View SKU-level profitability analysis
- View cost breakdown by category
- View trend analysis and comparisons
- Export reports to Excel/PDF

### Finance User:
- All Plant Manager permissions
- Enter manual rates (PTA, MEG, Payroll, Insurance, Overheads, Corporate)
- Adjust cost allocations
- Perform month-end reconciliation
- View audit trails
- Access historical data

### Corporate Management:
- View consolidated EBITDA across all in-scope plants
- View executive dashboard with KPIs
- View margin trends and analysis
- View cost optimization opportunities
- Access comparative reports

### System Administrator:
- Configure BOM and merge factors
- Configure cost center mappings
- Manage user access and permissions
- Monitor system health and data quality
- View system logs and error reports

---

# 4. Nonfunctional Requirements

## 4.1 Performance Requirements

- Dashboard loading time: Less than 5 seconds
- Data processing for 500+ SKUs: Complete within 2 hours of data availability
- Daily calculation completion: Before 10 AM for previous day data
- Concurrent users: Support minimum 50 concurrent users
- Report generation: Less than 10 seconds for standard reports
- Data export: Less than 30 seconds for Excel/PDF export
- System availability: 99.5% uptime during business hours
- Database query response: Less than 3 seconds for complex queries

## 4.2 Supportability Requirements

### Coding Standards:
- Follow tcgmcube development guidelines
- Use consistent naming conventions
- Maintain code documentation
- Follow modular design principles

### Maintenance Access:
- Provide administrator interface for configuration changes
- Enable remote troubleshooting capabilities
- Maintain detailed system logs
- Provide data backup and recovery mechanisms

### User Support:
- Provide user manual and training documentation
- Include in-app help and tooltips
- Provide error messages in simple language
- Enable user feedback mechanism

---

# 5. Assumptions / Constraints

## Assumptions:

- SAP system will be available daily for data extraction
- TBS and IGMS systems will provide accurate data
- Users will enter manual data in a timely manner
- BOM data is maintained accurately in the system
- Network connectivity between systems is stable
- Users have basic understanding of EBITDA concepts
- Month-end closing will be completed by finance team

## Constraints:

- Initial scope limited to Jolwa Plant only
- Projected EBITDA feature depends on S&OP module Go-Live
- Manual rate entry required until automated procurement integration
- Limited to 500+ SKUs in initial release
- Historical data limited to current fiscal year
- No real-time inventory valuation in first release
- Reporting with T-2 (two days lag) for management dashboards

---

# 6. Other Requirements

## Security Requirements:

- Role-based access control for all users
- Audit trail for all data changes
- Secure data transmission between systems
- Data encryption at rest and in transit
- Session timeout after 30 minutes of inactivity
- Password policy enforcement

## Compliance Requirements:

- Comply with financial reporting standards
- Maintain data retention as per company policy
- Support audit requirements
- Follow data privacy regulations

## Training Requirements:

- Provide user training for all roles
- Create training videos and documentation
- Conduct hands-on workshops
- Provide quick reference guides

---

# 7. Appendix A: Glossary

| Abbreviation | Description |
|--------------|-------------|
| BOM | Bill of Materials - defines material consumption per SKU |
| COGM | Cost of Goods Manufactured |
| COGS | Cost of Goods Sold |
| EBITDA | Earnings Before Interest, Taxes, Depreciation, and Amortization |
| IGMS | Integrated Gate Management System |
| MEG | Monoethylene Glycol - raw material |
| NRV | Net Realizable Value - estimated selling price minus costs |
| PTA | Purified Terephthalic Acid - raw material |
| SAP | Systems, Applications, and Products in Data Processing |
| SFG | Semi-Finished Goods |
| SKU | Stock Keeping Unit - individual product identifier |
| S&OP | Sales and Operations Planning |
| TBS | Transaction Booking System |
| T-2 | Two days lag reporting |
| VCC | Variable Cost Contribution |

---

# 8. Appendix B: Assumptions and Dependencies

## Dependencies:

- SAP system availability and data accuracy
- TBS system integration and data flow
- IGMS system integration for manpower data
- tcgmcube platform availability
- Network infrastructure and bandwidth
- Finance team for manual data entry
- BOM maintenance by operations team
- S&OP module for projected EBITDA feature

## Third-Party Components:

- tcgmcube platform (v5.3 or higher)
- SAP ERP system
- TBS system
- IGMS system
- Database server
- Application server
- Web server

---

# 9. Appendix C: Issues List

| Issue ID | Description | Status | Owner | Target Resolution |
|----------|-------------|--------|-------|-------------------|
| ISS-001 | Decision needed: How to allocate Common Cost center expenses at SKU/Product Level | Open | Finance Lead | [Date] |
| ISS-002 | Waste Sales SKU mapping needs more granular definition | Open | Operations Lead | [Date] |
| ISS-003 | Insurance allocation method to be finalized (Option 1 or 2) | Open | Finance Lead | [Date] |
| ISS-004 | Corporate expense bucketing strategy needs confirmation | Open | CFO | [Date] |

---

# 10. Appendix D: Business Risks

| Risk ID | Description | Impact | Likelihood | Mitigation Strategy |
|---------|-------------|--------|------------|---------------------|
| BR-001 | Inaccurate BOM data leading to wrong cost calculations | High | Medium | Implement BOM validation process and regular audits |
| BR-002 | Delayed manual data entry affecting daily reports | Medium | High | Implement data entry reminders and escalation process |
| BR-003 | SAP system downtime affecting data availability | High | Low | Implement data caching and retry mechanisms |
| BR-004 | User resistance to new system adoption | Medium | Medium | Conduct thorough training and change management |
| BR-005 | Data quality issues from source systems | High | Medium | Implement data validation and quality checks |
| BR-006 | Complex allocation logic causing calculation errors | High | Medium | Implement thorough testing and reconciliation processes |
| BR-007 | Market price fluctuations requiring frequent rate updates | Medium | High | Implement alert mechanism for significant price changes |

---

**TCG Digital Solutions LLC**  
www.tcgdigital.com

TCG Digital is the flagship data science and technology solutions company of 'The Chatterjee Group' (TCG), a multi-billion dollar conglomerate. We leverage hyper-contemporary technologies and deep domain expertise to engage enterprises with full-spectrum digital transformation initiatives in operational support systems, enterprise mobility, app development and testing, cloud and microservices, automation, security, Big Data Strategy, AI/ML, and advanced analytics.

In addition to our Digital Transformation practices, by using our end-to-end AI and advanced analytics Platform, tcgmcube, enterprises are extracting highly actionable insights from their invaluable data assets and achieving Velocity to Value. tcgmcube democratizes data science with scalability, performance, and flexibility. For more information, please visit our website at www.tcgdigital.com

**www.tcgdigital.com**  
contact@tcgdigital.com  
+1 (732) 515-7376