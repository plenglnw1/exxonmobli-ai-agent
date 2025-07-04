# 📋 ExxonMobil AI-Powered Supply Chain Intelligence System

## 🎯 System Overview

ระบบ Multi-Agent AI สำหรับการจัดการ Supply Chain ของ ExxonMobil ที่พัฒนาด้วย n8n platform ประกอบด้วย 3 Workflows หลัก ที่ทำงานร่วมกันแบบ Real-time

---

## 🏗️ System Architecture

```
┌─────────────────┐    ┌──────────────────────┐    ┌─────────────────┐
│   Data Trigger  │───▶│ Master Orchestrator  │───▶│ Replenisher     │
│                 │    │      Agent           │    │    Agent        │
│ - Schedule      │    │                      │    │                 │
│ - Google Sheets │    │ - Task Classification│    │ - Stock Analysis│
│ - Alert Gen     │    │ - Agent Routing      │    │ - Procurement   │
│ - Data Process  │    │ - Orchestration      │    │ - Recommendations│
└─────────────────┘    └──────────────────────┘    └─────────────────┘
```

---

## 📁 Workflows Description

### 1️⃣ **Data Trigger Workflow**
**File:** `Data Trigger.json`

**Purpose:** ระบบตรวจสอบและประมวลผลข้อมูลแบบ Real-time

**Key Components:**
- **Schedule Trigger** - ตรวจสอบทุก 2 วัน
- **Google Sheets Integration** - อ่านข้อมูล Inventory, Inbound, Outbound
- **Gemini AI Analysis** - วิเคราะห์ข้อมูลและสร้าง Alert
- **Master Orchestrator Caller** - ส่งข้อมูลไปยัง Orchestrator

**Data Sources:**
- Current Stock levels
- Inbound shipments
- Outbound deliveries
- Material information

---

### 2️⃣ **Master Orchestrator Agent**
**File:** `Master_Orchestrator_Agent.json`

**Purpose:** ตัวควบคุมหลักที่จัดการและกระจายงานไปยัง Specialized Agents

**Key Features:**
- **Task Classification** - แยกประเภทงานตาม Business Logic
- **Agent Routing** - ส่งงานไปยัง Agent ที่เหมาะสม
- **Orchestration Planning** - วางแผนการดำเนินงาน
- **Progress Monitoring** - ติดตามความคืบหน้า

**Routing Logic:**
```javascript
// Task Categories
- replenisher_tasks: Stock management, Procurement
- warehouse_tasks: Inventory operations, Logistics
- finance_tasks: Cost analysis, Budget planning
- alert_tasks: Critical notifications, Escalations
```

---

### 3️⃣ **Replenisher Agent**
**File:** `Replenisher_Agent.json`

**Purpose:** Specialized Agent สำหรับการจัดการ Stock และ Procurement

**Core Functions:**
- **Stock Level Analysis** - วิเคราะห์ระดับสต็อก
- **Demand Forecasting** - พยากรณ์ความต้องการ
- **Procurement Recommendations** - แนะนำการสั่งซื้อ
- **Supplier Management** - จัดการข้อมูลผู้จัดจำหน่าย

---

## 🚀 Installation & Setup

### Prerequisites
```bash
# Required
- n8n platform (v1.0+)
- Google Sheets API access
- Google Gemini API key
- Node.js environment
```

### Step 1: Import Workflows
```bash
# Import ทั้ง 3 workflows ใน n8n
1. Data Trigger.json
2. Master_Orchestrator_Agent.json  
3. Replenisher_Agent.json
```

### Step 2: Configure Credentials
```javascript
// Google Sheets OAuth2
- Client ID: [Your Google Client ID]
- Client Secret: [Your Google Client Secret]

// Google Gemini API
- API Key: [Your Gemini API Key]
- Model: models/gemini-2.5-pro
```

### Step 3: Update Google Sheets IDs
```javascript
// ใน Data Trigger workflow
documentId: "143Fy4ASTfqV6G7RbQPRIaDj0DMJ_tu1PLOSW8WhBZYY"

// Sheets:
- Inventory (ID: 1835731665)
- Inbound
- Outbound  
- Material Info
```

---

## ⚙️ Configuration

### Webhook Endpoints
```bash
# Master Orchestrator
POST http://localhost:5678/webhook/Master-Orchestrator-Agent

# Replenisher Agent  
POST http://localhost:5678/webhook/replenisher-agent
```

### Schedule Settings
```javascript
// Data Trigger runs every 2 days
"interval": [{"daysInterval": 2}]
```

### Google Sheets Structure
```
📊 Inventory Sheet:
- Product_ID | Product_Name | Current_Stock | Min_Level | Max_Level

📊 Inbound Sheet:  
- Shipment_ID | Product_ID | Quantity | Expected_Date | Status

📊 Outbound Sheet:
- Order_ID | Product_ID | Quantity | Ship_Date | Destination
```

---

## 🔄 System Flow

### 1. **Data Collection Phase**
```mermaid
Data Trigger → Google Sheets → Data Processing → Alert Generation
```

### 2. **Orchestration Phase**  
```mermaid
Master Orchestrator → Task Classification → Agent Selection → Task Assignment
```

### 3. **Execution Phase**
```mermaid
Specialized Agents → Data Analysis → Recommendations → Response Generation
```

---

## 📊 Sample API Calls

### Trigger Data Analysis
```bash
curl -X POST http://localhost:5678/webhook/Master-Orchestrator-Agent \
  -H "Content-Type: application/json" \
  -d '{
    "request_type": "stock_analysis",
    "priority": "HIGH",
    "data_source": "inventory_check"
  }'
```

### Call Replenisher Agent
```bash
curl -X POST http://localhost:5678/webhook/replenisher-agent \
  -H "Content-Type: application/json" \
  -d '{
    "task_type": "stock_replenishment",
    "product_ids": ["PROD001", "PROD002"],
    "urgency": "CRITICAL"
  }'
```

---

## 📈 Expected Outputs

### Data Trigger Response
```json
{
  "analysis_summary": {
    "total_products": 150,
    "low_stock_alerts": 12,
    "critical_items": 3,
    "recommendations": "Immediate action required"
  },
  "orchestrator_call": "SUCCESS",
  "next_check": "2025-07-06T22:41:00Z"
}
```

### Master Orchestrator Response
```json
{
  "orchestration_id": "ORCH-1720134180000",
  "task_routing": {
    "replenisher_tasks": 5,
    "warehouse_tasks": 2,
    "alert_tasks": 3
  },
  "execution_plan": {
    "immediate_tasks": ["STOCK_ALERT", "PROCUREMENT"],
    "agent_assignments": ["replenisher-agent", "warehouse-agent"]
  }
}
```

### Replenisher Agent Response
```json
{
  "replenishment_plan": {
    "urgent_orders": [
      {
        "product_id": "PROD001",
        "current_stock": 50,
        "recommended_order": 500,
        "supplier": "SUPPLIER_A",
        "estimated_delivery": "2025-07-10"
      }
    ],
    "total_cost_estimate": 125000,
    "approval_required": true
  }
}
```

---

## 🛠️ Troubleshooting

### Common Issues

#### 1. Webhook Not Responding
```bash
# Check if workflows are Active
- Go to n8n Workflows
- Ensure toggle is ON for all 3 workflows
```

#### 2. Google Sheets Access Error
```bash
# Verify credentials
- Check OAuth2 token expiry
- Confirm Sheet IDs are correct
- Ensure proper permissions
```

#### 3. Gemini API Errors
```bash
# API Key issues
- Verify API key is valid
- Check quota limits
- Confirm model name: "models/gemini-2.5-pro"
```

---

## 🔧 Customization

### Adding New Agents
```javascript
// In Master Orchestrator
task_routing: {
  replenisher_tasks: [],
  warehouse_tasks: [],
  finance_tasks: [],
  quality_tasks: [],      // New agent type
  maintenance_tasks: []   // New agent type
}
```

### Modifying Schedule
```javascript
// Change trigger frequency
"rule": {
  "interval": [
    {"hoursInterval": 6}  // Every 6 hours instead of 2 days
  ]
}
```

### Custom Business Logic
```javascript
// Add custom rules in Orchestrator
if (analysis.critical_stock_level > threshold) {
  orchestration_decisions.push({
    action: "EMERGENCY_PROCUREMENT",
    agent: "replenisher-agent",
    priority: "CRITICAL"
  });
}
```

---

## 📞 Support & Maintenance

### Health Check Endpoints
```bash
# Test all workflows
GET http://localhost:5678/workflows

# Check specific workflow status  
GET http://localhost:5678/workflows/[workflow-id]
```

### Monitoring
- **Execution History** - Check n8n execution logs
- **Error Tracking** - Monitor failed executions
- **Performance Metrics** - Track response times

### Backup
```bash
# Export workflows regularly
n8n export:workflow --all --output=backup/
```

---

## 🎯 Next Steps

1. **Add More Agents** - Warehouse, Finance, Quality Control
2. **Implement Dashboard** - Real-time monitoring interface  
3. **Add Notifications** - Slack/Discord/Email integration
4. **Enhanced Analytics** - Advanced reporting features
5. **Mobile Interface** - Mobile app for field operations

---

## 📝 Version History

- **v1.0** - Initial release with 3 core workflows
- **v1.1** - Enhanced Gemini integration
- **v1.2** - Improved error handling
- **v2.0** - Multi-agent orchestration (Current)

---

**🏢 Developed for ExxonMobil Supply Chain Intelligence**  
**🤖 Powered by n8n + Google Gemini AI**  
**📅 Last Updated: July 2025**