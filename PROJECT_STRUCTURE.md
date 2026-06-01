# PROJECT STRUCTURE & FILE GUIDE

## Complete Directory Tree

```
Hack2Hire/
├── README.md                          # Main documentation
├── QUICK_START.md                     # 5-minute setup guide
├── ARCHITECTURE.md                    # System design & architecture
├── API_DOCUMENTATION.md               # API reference & examples
│
├── backend/
│   ├── main.py                        # Flask API server (entry point)
│   ├── sample_usage.py                # Example usage without web interface
│   ├── requirements.txt               # Python dependencies
│   ├── .env.example                   # Environment variables template
│   │
│   ├── config/
│   │   ├── __init__.py                # Package init
│   │   └── settings.py                # Configuration & constants
│   │
│   ├── modules/
│   │   ├── __init__.py                # Package init
│   │   ├── resume_parser.py           # Resume extraction & parsing
│   │   ├── jd_parser.py               # Job description parsing
│   │   ├── question_generator.py      # Adaptive question generation
│   │   ├── answer_evaluator.py        # Answer scoring & evaluation
│   │   ├── interview_controller.py    # Interview orchestration
│   │   └── report_generator.py        # Final report generation
│   │
│   └── utils/
│       ├── __init__.py                # Package init
│       ├── constants.py               # Constants & enums
│       ├── helpers.py                 # Utility functions
│       └── prompts.py                 # LLM prompt templates
│
├── frontend/
│   └── index.html                     # Interactive web interface
│
├── tests/
│   ├── __init__.py                    # Package init
│   └── test_interview.py              # Test suite (unit & integration)
│
└── .gitignore                         # Git ignore file (recommended)
```

## File Descriptions

### Root Level

| File | Purpose |
|------|---------|
| `README.md` | Main documentation with features, setup, and usage |
| `QUICK_START.md` | Quick setup guide for first-time users |
| `ARCHITECTURE.md` | Detailed system architecture and design decisions |
| `API_DOCUMENTATION.md` | Complete API reference with examples |

### Backend Core

| File | Purpose | Key Classes |
|------|---------|------------|
| `main.py` | Flask API server with all REST endpoints | Flask app, routes |
| `sample_usage.py` | Standalone usage example | Main demo |
| `requirements.txt` | Python package dependencies | N/A |
| `.env.example` | Template for environment variables | N/A |

### Backend Config

| File | Purpose | Key Content |
|------|---------|------------|
| `config/settings.py` | Global configuration and constants | Interview settings, scoring weights |

### Backend Modules

| File | Purpose | Key Classes |
|------|---------|------------|
| `resume_parser.py` | Extracts and parses resume information | `Resume`, `ResumeParser` |
| `jd_parser.py` | Parses job description requirements | `JobDescription`, `JDParser` |
| `question_generator.py` | Generates adaptive interview questions | `InterviewQuestion`, `QuestionGenerator` |
| `answer_evaluator.py` | Evaluates and scores candidate answers | `AnswerEvaluation`, `AnswerEvaluator` |
| `interview_controller.py` | Orchestrates entire interview flow | `InterviewSession`, `InterviewController` |
| `report_generator.py` | Generates comprehensive final reports | `InterviewReport`, `ReportGenerator` |

### Backend Utils

| File | Purpose | Key Functions |
|------|---------|---|
| `constants.py` | Shared constants and enums | Status values, difficulty levels, question types |
| `helpers.py` | Reusable utility functions | Score calculation, performance categorization |
| `prompts.py` | LLM prompt templates | GPT-4 prompts for all operations |

### Frontend

| File | Purpose | Features |
|------|---------|----------|
| `index.html` | Interactive web interface | Resume input, interview UI, real-time timer, report dashboard |

### Tests

| File | Purpose | Coverage |
|------|---------|----------|
| `test_interview.py` | Comprehensive test suite | Unit tests, integration tests, edge cases |

---

## Key Components Explained

### 1. Resume Parser (`resume_parser.py`)
**Purpose:** Extracts structured information from resume text

**Input:** Resume text (raw string)
**Output:** Resume object with parsed skills, experience, education

**Key Methods:**
- `parse()` - Main parsing method
- `extract_skills()` - Get list of skills
- `get_role_relevance_score()` - Score resume vs job requirements

### 2. JD Parser (`jd_parser.py`)
**Purpose:** Extracts structure from job description

**Input:** Job description text
**Output:** JobDescription object with requirements and focus areas

**Key Methods:**
- `parse()` - Parse JD into structured format
- `get_all_skills()` - Combine required and preferred skills
- `get_difficulty_level_mapping()` - Map role level to interview difficulty

### 3. Question Generator (`question_generator.py`)
**Purpose:** Generates contextual, adaptive interview questions

**Input:** Resume, JD, question type, difficulty level
**Output:** InterviewQuestion with question text and metadata

**Key Methods:**
- `generate_question()` - Generate single question
- `generate_batch()` - Generate multiple questions
- `select_next_question_type()` - Balance question types

**Algorithms:**
- Question type cycling (Technical → Behavioral → Scenario → Conceptual)
- Difficulty adaptation based on performance
- Keyword extraction for evaluation

### 4. Answer Evaluator (`answer_evaluator.py`)
**Purpose:** Evaluate candidate answers and adapt difficulty

**Input:** Question, answer, time taken, job requirements
**Output:** AnswerEvaluation with scores and feedback

**Key Methods:**
- `evaluate()` - Main evaluation method
- `get_next_difficulty()` - Determine difficulty for next question
- `should_terminate_early()` - Check if interview should end

**Scoring Criteria:**
- Accuracy (30%)
- Clarity (25%)
- Depth (25%)
- Relevance (15%)
- Time Efficiency (5%)

### 5. Interview Controller (`interview_controller.py`)
**Purpose:** Orchestrates entire interview flow

**Input:** User requests via API
**Output:** Interview responses and final report

**Key Methods:**
- `start_interview()` - Initialize new interview
- `get_next_question()` - Generate next question
- `submit_answer()` - Evaluate answer
- `complete_interview()` - Finalize and generate report

**Responsibilities:**
- Session management
- Module coordination
- State tracking

### 6. Report Generator (`report_generator.py`)
**Purpose:** Generate comprehensive interview reports

**Input:** All interview data
**Output:** Detailed report with recommendations

**Key Methods:**
- `generate()` - Create final report

**Report Sections:**
- Performance metrics
- Skill assessment
- Analysis (technical depth, communication, problem-solving)
- Strengths and weaknesses
- Hiring recommendation

---

## Data Models

### Resume Model
```python
Resume:
  - name: str
  - email: str
  - phone: str
  - skills: List[str]
  - experience: List[Experience]
  - education: List[Education]
  - projects: List[str]
  - certifications: List[str]
  - years_of_experience: int
```

### JobDescription Model
```python
JobDescription:
  - job_title: str
  - company: str
  - level: str (junior/mid/senior)
  - required_skills: List[str]
  - preferred_skills: List[str]
  - years_required: int
  - key_responsibilities: List[str]
  - technical_focus: List[str]
  - soft_skills_required: List[str]
```

### InterviewQuestion Model
```python
InterviewQuestion:
  - id: int
  - question: str
  - type: str (Technical/Behavioral/etc)
  - difficulty: str (Easy/Medium/Hard)
  - keywords: List[str]
  - expected_duration_seconds: int
  - evaluation_criteria: List[str]
```

### AnswerEvaluation Model
```python
AnswerEvaluation:
  - accuracy: float (0-100)
  - clarity: float (0-100)
  - depth: float (0-100)
  - relevance: float (0-100)
  - time_efficiency: float (0-100)
  - overall_score: float (0-100)
  - feedback: str
  - strengths: List[str]
  - areas_for_improvement: List[str]
  - keywords_covered: List[str]
  - recommendation: str
```

### InterviewSession Model
```python
InterviewSession:
  - session_id: str (UUID)
  - candidate_name: str
  - job_title: str
  - status: str (not_started/in_progress/completed/terminated_early)
  - start_time: datetime
  - end_time: datetime (optional)
  - total_questions: int
  - questions_asked: int
  - current_difficulty: str
  - performance_scores: List[float]
  - questions_data: List[Dict]
  - answers_data: List[Dict]
  - termination_reason: str (optional)
```

---

## Configuration Files

### settings.py
```python
INTERVIEW_CONFIG          # Interview parameters
SCORING_CONFIG            # Scoring weights
DIFFICULTY_ADAPTATION     # Difficulty thresholds
PERFORMANCE_CATEGORIES    # Score categories
```

### constants.py
```python
INTERVIEW_STATUSES        # Interview state values
DIFFICULTY_LEVELS         # Difficulty enum
QUESTION_TYPES            # Question type enum
EVALUATION_CRITERIA       # Scoring criteria
PERFORMANCE_FEEDBACK      # Feedback messages
```

### prompts.py
```python
RESUME_EXTRACTION_PROMPT           # Resume parsing
JD_EXTRACTION_PROMPT               # JD parsing
QUESTION_GENERATION_PROMPT         # Question generation
ANSWER_EVALUATION_PROMPT           # Answer evaluation
DIFFICULTY_ADAPTATION_PROMPT       # Difficulty adjustment
REPORT_GENERATION_PROMPT           # Report creation
```

---

## API Endpoints Summary

| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | `/health` | Health check |
| POST | `/api/interview/start` | Start new interview |
| GET | `/api/interview/<id>/question` | Get next question |
| POST | `/api/interview/<id>/answer` | Submit answer |
| GET | `/api/interview/<id>/status` | Get session status |
| POST | `/api/interview/<id>/complete` | Finalize interview |

---

## Test Coverage

### test_interview.py
- **Helper Functions:** 9 tests
- **Resume Parser:** 3 tests
- **JD Parser:** 3 tests
- **Question Generator:** 1 test
- **Answer Evaluator:** 5 tests
- **Integration:** 1 test
- **Edge Cases:** 4 tests

**Total:** 26 test cases covering core functionality

---

## Dependencies

### Python Packages
- **openai** - GPT-4 API client
- **flask** - Web framework
- **flask-cors** - CORS support
- **pydantic** - Data validation
- **python-dotenv** - Environment management
- **requests** - HTTP client
- **pytest** - Testing framework

### External Services
- **OpenAI GPT-4 API** - AI capabilities
- **Browser** - Frontend (no server required)

---

## Usage Flows

### Flow 1: Web Interface
```
Open frontend/index.html
  ↓
Enter resume & JD
  ↓
Call API: POST /api/interview/start
  ↓
For each question:
  ├─ Call: GET /api/interview/{id}/question
  ├─ Display question
  ├─ Get answer
  └─ Call: POST /api/interview/{id}/answer
  ↓
Call: POST /api/interview/{id}/complete
  ↓
Display report
```

### Flow 2: Programmatic (sample_usage.py)
```
Import InterviewController
  ↓
Initialize with OpenAI client
  ↓
Call: start_interview()
  ↓
Get & submit questions
  ↓
Call: complete_interview()
  ↓
Process report data
```

### Flow 3: API Only
```
Make HTTP requests to Flask endpoints
  ↓
Handle JSON responses
  ↓
Manage session state on client
```

---

## Troubleshooting Quick Links

| Issue | File | Solution |
|-------|------|----------|
| API key errors | `.env` | Add OpenAI API key |
| Import errors | `modules/__init__.py` | Check __init__ files exist |
| Port in use | `main.py` | Change port number |
| Slow responses | `prompts.py` | Check API rate limits |
| Score calculation | `answer_evaluator.py` | Verify scoring weights |

---

## Getting Started

1. **Read:** Start with [QUICK_START.md](QUICK_START.md)
2. **Setup:** Follow installation steps
3. **Test:** Run `python main.py`
4. **Learn:** Read [ARCHITECTURE.md](ARCHITECTURE.md)
5. **Build:** Check [API_DOCUMENTATION.md](API_DOCUMENTATION.md)
6. **Extend:** Modify modules as needed

---

**All files are essential for the platform to function correctly. Customize as needed for your use case!**
