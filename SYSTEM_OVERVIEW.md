# Niah System Overview
*Last Updated: November 28, 2024*

## 🎯 **System Purpose**
Niah is a Slack-based AI data analyst that converts natural language questions into SQL queries and provides business intelligence. Niah stands for "Needle in a Haystack" and offers two distinct processing modes through intelligent routing.

## 🏗️ **Core Architecture**

### **Two-Command System:**
1. **`/dd`** - Direct Data Queries (fast, table-focused)
2. **`/askdb`** - Intelligent Business Analysis (with smart routing)

### **Smart Routing in `/askdb`:**
```
User Question → Query Router → Decision
                     ↓
        Simple Query ←→ Complex Investigation
             ↓                    ↓
     /dd-style processing    True Agentic System
     (0.8s, 1 LLM call)      (3-5s, 5+ LLM calls)
             ↓                    ↓
        Quick Answer        Comprehensive Analysis
```

## 🤖 **LLM Backend Configuration**

### **Dual Backend Support:**
- **Ollama (Local)**: `LLM_BACKEND=ollama` - Privacy-first, no data leaves machine
- **Groq (Cloud)**: `LLM_BACKEND=groq` - Speed-first, fast inference

### **Environment Variables:**
```bash
# LLM Backend Choice
LLM_BACKEND=groq  # or "ollama"

# Groq Configuration (cloud)
GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
GROQ_MODEL=llama3-8b-8192

# Ollama Configuration (local)
OLLAMA_BASE_URL=http://127.0.0.1:11434
OLLAMA_MODEL=granite4

# Other
SLACK_BOT_TOKEN=xoxb-...
SQLITE_PATH=./your_database.db
```

## 📊 **Visualization Capabilities**

### **Chart Types:**
- **📊 Bar Charts**: Categorical comparisons
- **📈 Line Charts**: Trends and time series (fixed X-axis labeling)
- **🥧 Pie Charts**: Proportional analysis with callouts and legends

### **Export Options:**
- **📎 CSV Export**: Full data download
- **🔍 AI Insights**: Business analysis of results

### **Button Layout:**
- Row 1: `[📎 Export] [📊 Bar] [📈 Line] [🥧 Pie] [🔍 Insights]`
- Row 2: `[⬅️ Previous] [📄 Page X/Y] [Next ➡️]` (pagination only)

## 🧠 **True Agentic System**

### **When Triggered:**
Complex questions like:
- "Why did sales drop last month?"
- "What patterns do you see in customer behavior?"
- "Analyze performance trends over time"
- "What should I know about this dataset?"

### **4-Step Process:**
1. **Reasoning**: Analyzes question type and strategy
2. **Hypothesis**: Forms testable theories about the answer
3. **Investigation**: 1-5 iterative queries building on findings
4. **Synthesis**: Combines all data into business insights

### **Key Features:**
- **Database Agnostic**: Works with any schema (F1, e-commerce, HR, etc.)
- **Dynamic Adaptation**: Each query informed by previous results
- **Smart Confidence**: Only completes with 85%+ confidence + multiple findings
- **Schema Intelligence**: Automatically discovers tables and columns

## 🎯 **Query Router Logic**

### **Simple Query Patterns:**
- `"show me"`, `"list"`, `"count"`, `"top 10"`
- `"how many"`, `"total"`, `"all customers"`
- Direct data requests and aggregations

### **Complex Analysis Patterns:**
- `"why"`, `"analyze"`, `"patterns"`, `"trends"`
- `"what should I know"`, `"investigate"`, `"explain"`
- Business intelligence and root cause questions

### **Routing Confidence:**
- **90%+ confidence**: Strong pattern match
- **70-89%**: Likely match
- **<70%**: Defaults to simple for efficiency

## 🔒 **Security & Guardrails**

### **SQL Security:**
- Read-only database connections
- SELECT-only queries (blocks DDL/DML)
- Automatic LIMIT injection (default 500, agentic uses 50)
- Pattern-based SQL injection prevention
- System table access blocking

### **Data Privacy:**
- **Ollama**: Complete local processing, no data leaves machine
- **Groq**: Cloud processing, follows Groq's privacy policies
- No permanent data storage beyond session management

## 📁 **Key Files & Services**

### **Core Services:**
- `services/true_agentic_analyst.py` - Agentic investigation system
- `services/query_router.py` - Intelligence routing logic
- `services/llm.py` - Dual LLM backend management
- `services/database.py` - Safe database operations
- `services/data_export.py` - Visualization and export

### **Routes:**
- `routes/slack_routes.py` - All Slack endpoints and logic
  - `/slack/sqlquery` - `/dd` command handler
  - `/slack/askdb` - `/askdb` command handler  
  - `/slack/interactions` - Button click handlers
  - `/slack/help` - Help command

### **Configuration:**
- `config/settings.py` - Centralized configuration with validation
- `core/guardrails.py` - SQL security and validation

## 🚀 **Performance Characteristics**

### **Response Times:**
- **Simple `/askdb`**: ~0.8 seconds (1 LLM call + 1 DB query)
- **Complex `/askdb`**: 3-5 seconds (5+ LLM calls + multiple DB queries)
- **Regular `/dd`**: ~0.6 seconds (1 LLM call + 1 DB query)

### **Token Efficiency:**
- **70% reduction** for simple queries through smart routing
- **Smart iteration control** - stops when confident, not on fixed schedule
- **Database-specific fallbacks** prevent unnecessary LLM calls

## 🌐 **Database Compatibility**

### **Current Support:**
- **SQLite** (primary) - Read-only mode for safety

### **Schema Agnostic Design:**
The agentic system works with ANY database schema:
- **F1 Racing**: drivers, constructors, races, circuits
- **E-commerce**: customers, orders, products, reviews
- **HR**: employees, departments, salaries, performance
- **Finance**: transactions, accounts, budgets, investments

### **Auto-Discovery:**
- Parses schema YAML or SQL DDL automatically
- Extracts table names and column types
- Identifies relationships and key fields
- Generates appropriate queries for any domain

## 📈 **Usage Patterns**

### **Recommended Use Cases:**

**Use `/dd` for:**
- Quick data lookups
- Table browsing
- Simple aggregations
- When you want raw data + visualizations

**Use `/askdb` for:**
- Business questions
- Root cause analysis  
- Trend investigation
- When you want insights + recommendations

## 🔧 **Development & Testing**

### **Quick Start:**
```bash
git clone <repo>
pip install -r requirements.txt
cp .env.example .env  # Edit with your tokens
python app.py
```

### **Testing:**
```bash
python run_tests.py  # Runs all tests
```

### **Debug Mode:**
The system includes extensive logging for troubleshooting:
- `[DEBUG]` - General flow information
- `[AGENTIC]` - Agentic system internals
- `[SIMPLE]` - Simple processing path
- SQL query logging and timing information

## 🎯 **Future Considerations**

### **Potential Enhancements:**
- Multi-database connections (PostgreSQL, MySQL)
- Real-time dashboards
- Advanced anomaly detection
- Predictive analytics integration
- Custom domain knowledge plugins

### **Scaling:**
- Current architecture supports multiple concurrent users
- Stateless design (except session management for plots)
- Horizontal scaling possible with external session storage

---

*This document serves as the definitive reference for understanding Niah's architecture and capabilities. Update this file when making significant system changes.*
