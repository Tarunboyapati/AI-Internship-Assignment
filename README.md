FIXED CODE:
requirements.txt:
fastapi
uvicorn
sqlalchemy
psycopg2-binary
python-dotenv
pydantic
celery
redis
openai
app/database.py :
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base
import os
from dotenv import load_dotenv
load_dotenv()
DATABASE_URL = os.getenv("DATABASE_URL", "sqlite:///./test.db")
engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False} if "sqlite" in DATABASE_URL else {})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()
app/models.py :
from sqlalchemy import Column, Integer, String, Float, Text
from .database import Base
class AnalysisResult(Base):
    __tablename__ = "analysis_results"
    id = Column(Integer, primary_key=True, index=True)
    document = Column(Text)
    summary = Column(Text)
    risk_score = Column(Float)
    recommendation = Column(String)
app/schemas.py : 
from pydantic import BaseModel
class DocumentRequest(BaseModel):
    document: str
class AnalysisResponse(BaseModel):
    id: int
    summary: str
    risk_score: float
    recommendation: str
    class Config:
        orm_mode = True
app/analyzer.py :
import random
def analyze_document(document: str):
    summary = f"Summary: {document[:150]}..."
    # Simulated risk score logic
    risk_score = round(random.uniform(0.3, 0.9), 2)
    if risk_score > 0.75:
        recommendation = "High Risk Investment"
    elif risk_score > 0.5:
        recommendation = "Moderate Risk Investment"
    else:
        recommendation = "Low Risk Investment"
    return summary, risk_score, recommendation
🔹 app/worker.py (Celery Worker)
from celery import Celery
from .analyzer import analyze_document
from .database import SessionLocal
from .models import AnalysisResult
celery_app = Celery(
    "worker",
    broker="redis://localhost:6379/0"
)
@celery_app.task
def process_document(document: str):
    db = SessionLocal()
    summary, risk_score, recommendation = analyze_document(document)
    result = AnalysisResult(
        document=document,
        summary=summary,
        risk_score=risk_score,
        recommendation=recommendation
    )
    db.add(result)
    db.commit()
    db.refresh(result)
    db.close()
    return result.id
🔹 app/main.py
from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.orm import Session
from .database import engine, Base, SessionLocal
from .models import AnalysisResult
from .schemas import DocumentRequest, AnalysisResponse
from .analyzer import analyze_document
from .worker import process_document
Base.metadata.create_all(bind=engine)
app = FastAPI(title="Financial Document Analyzer")
# Dependency
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
@app.get("/health")
def health():
    return {"status": "ok"}
@app.post("/analyze", response_model=AnalysisResponse)
async def analyze(request: DocumentRequest, db: Session = Depends(get_db)):
    if not request.document.strip():
        raise HTTPException(status_code=400, detail="Document cannot be empty")
    summary, risk_score, recommendation = analyze_document(request.document)
    result = AnalysisResult(
        document=request.document,
        summary=summary,
        risk_score=risk_score,
        recommendation=recommendation
    )
    db.add(result)
    db.commit()
    db.refresh(result)
    return result
@app.get("/results/{result_id}", response_model=AnalysisResponse)
def get_result(result_id: int, db: Session = Depends(get_db)):
    result = db.query(AnalysisResult).filter(AnalysisResult.id == result_id).first()
    if not result:
        raise HTTPException(status_code=404, detail="Result not found")
    return result
