# AI-Internship-Assignment
📊 Financial Document Analyzer
(CrewAI-Based Scalable Backend System)
An AI-powered backend system that analyzes financial documents, generates structured summaries, evaluates financial risk, and provides investment recommendations.
This project was debugged, optimized, and enhanced with a production-ready architecture using asynchronous processing, queue workers, and database integration.
🚀 Project Overview
The Financial Document Analyzer allows users to:
Submit financial reports or statements
Generate AI-based summaries
Calculate risk scores
Receive investment recommendations
Store analysis results persistently
Handle concurrent requests efficiently
The system leverages CrewAI multi-agent orchestration to divide financial analysis tasks intelligently and ensure modular AI reasoning.
🎯 Key Objectives
Debug and stabilize the provided codebase
Fix runtime and dependency issues
Improve reliability and validation
Implement concurrent request handling
Add database persistence
Deliver a clean, production-ready API
✨ Features
🤖 CrewAI-based multi-agent financial analysis
📄 Intelligent financial document summarization
📊 Risk scoring engine
💡 Investment recommendation generation
⚡ Asynchronous request handling
🔁 Queue Worker Model (Redis + Celery)
🗄 Database integration (PostgreSQL / SQLite)
📘 Auto-generated Swagger documentation
🔐 Secure environment configuration
🧪 Structured error handling
🏥 Health check endpoint
🛠 Tech Stack
Layer	Technology
Backend Framework	FastAPI
AI Agent Framework	CrewAI
LLM Integration	OpenAI API
Task Queue	Redis
Background Worker	Celery
Database	PostgreSQL / SQLite
ORM	SQLAlchemy
Environment Management	python-dotenv
API Documentation	Swagger UI (FastAPI)
🧠 CrewAI Agent Workflow
The system uses multiple AI agents coordinated by CrewAI:
Financial Analyst Agent → Extracts key metrics from document
Risk Evaluation Agent → Computes financial risk score
Recommendation Agent → Generates structured investment advice
Each agent performs a specific role, improving modularity and reasoning quality.
🐞 Bugs Found and How They Were Fixed
1️⃣ Import & Dependency Errors
Issue:
Application failed to start due to incorrect module imports and missing dependencies.
Fix:
Corrected module paths
Added missing packages to requirements.txt
Standardized folder structure
2️⃣ CrewAI Agent Execution Failure
Issue:
Agents were not executing properly due to misconfigured Crew and Task definitions.
Fix:
Reconfigured Crew initialization
Properly linked agents with tasks
Ensured sequential output flow between agents
3️⃣ Blocking AI Calls
Issue:
Long response times because AI processing was synchronous inside API endpoints.
Fix:
Implemented Celery worker
Integrated Redis queue
Moved AI processing to background worker
Result:
API now responds immediately while processing occurs asynchronously.
4️⃣ No Persistent Data Storage
Issue:
Analysis results were lost after server restart.
Fix:
Integrated SQLAlchemy ORM
Created database model for storing analysis results
Added endpoint to retrieve stored data
5️⃣ Missing Input Validation
Issue:
Empty or invalid requests caused runtime exceptions.
Fix:
Implemented Pydantic request validation
Added explicit input checks
Standardized error responses
6️⃣ Hardcoded Configuration
Issue:
API keys and database URLs were hardcoded.
Fix:
Moved configuration to .env
Used environment variable management
Added .env.example file
⚙️ Setup and Usage Instructions
1️⃣ Clone Repository
git clone https://github.com/your-username/financial-document-analyzer.git
cd financial-document-analyzer
2️⃣ Create Virtual Environment
python -m venv venv
Activate:
Windows:
venv\Scripts\activate
Mac/Linux:
source venv/bin/activate
3️⃣ Install Dependencies
pip install -r requirements.txt
4️⃣ Configure Environment Variables
Create a .env file:
OPENAI_API_KEY=your_openai_api_key
DATABASE_URL=postgresql://user:password@localhost/dbname
REDIS_URL=redis://localhost:6379/0
5️⃣ Start Redis
redis-server
6️⃣ Start Celery Worker
celery -A app.worker worker --loglevel=info
7️⃣ Run FastAPI Application
uvicorn app.main:app --reload
Open in browser:
http://127.0.0.1:8000/docs
📡 API Documentation
🔹 POST /analyze
Analyze a financial document.
Request
{
  "document": "Company annual financial report..."
}
Response
{
  "id": 1,
  "summary": "Revenue increased by 15% year-over-year...",
  "risk_score": 0.72,
  "recommendation": "Moderate Risk Investment"
}
🔹 GET /results/{id}
Retrieve stored analysis result by ID.
Example:
GET /results/1
🔹 GET /health
Health check endpoint.
{
  "status": "ok"
}
🔐 Security Practices
Environment-based configuration
No hardcoded secrets
Input validation
Structured exception handling
Separation of API and AI logic
📈 Scalability Considerations
Asynchronous background processing
Queue-based architecture
Stateless API design
Database persistence
Horizontal scaling capability
🧪 Testing
Run tests using:
pytest
🚀 Future Enhancements
JWT Authentication
Role-based access control
Rate limiting
Docker & Docker Compose setup
Kubernetes deployment
Advanced financial metric extraction
Dashboard UI for result visualization
📌 Conclusion
The Financial Document Analyzer demonstrates a production-ready AI backend system that combines:
Multi-agent orchestration using CrewAI
Asynchronous processing with Redis and Celery
Persistent storage using SQLAlchemy
Scalable and modular API architecture
The project reflects strong backend engineering practices, AI integration capability, and system design understanding suitable for real-world deployment.
