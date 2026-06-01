# ARCHITECTURE DOCUMENT - Hack2Hire Interview Platform

## System Overview

The Hack2Hire platform is an intelligent mock interview system that leverages AI to conduct adaptive, realistic technical interviews with objective evaluation and comprehensive feedback.

```
┌─────────────────────────────────────────────────────────────────┐
│                        Frontend Layer                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Interactive Web Interface (HTML/CSS/JavaScript)         │  │
│  │  - Resume Upload Interface                               │  │
│  │  - Job Description Input                                 │  │
│  │  - Real-time Question Display                            │  │
│  │  - Timer & Performance Metrics                           │  │
│  │  - Report Dashboard                                      │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              ↕
                         HTTP/REST API
                              ↕
┌─────────────────────────────────────────────────────────────────┐
│                      Backend Layer (Flask)                      │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │            Interview Controller (Orchestrator)           │  │
│  │  - Session Management                                    │  │
│  │  - State Machine for Interview Flow                      │  │
│  │  - Module Coordination                                   │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              ↕
        ┌─────────────────────────────────────────┐
        │     Processing Modules Layer            │
        ├─────────────────────────────────────────┤
        │ ┌─────────────────────────────────────┐ │
        │ │   Resume Parser                     │ │
        │ │  - Extract skills, experience       │ │
        │ │  - Parse projects & education       │ │
        │ │  - Calculate role relevance         │ │
        │ └─────────────────────────────────────┘ │
        │ ┌─────────────────────────────────────┐ │
        │ │   JD Parser                         │ │
        │ │  - Extract requirements             │ │
        │ │  - Identify technical focus         │ │
        │ │  - Map role level to difficulty     │ │
        │ └─────────────────────────────────────┘ │
        │ ┌─────────────────────────────────────┐ │
        │ │   Question Generator                │ │
        │ │  - Generate adaptive questions      │ │
        │ │  - Balance question types           │ │
        │ │  - Ensure relevance to role         │ │
        │ └─────────────────────────────────────┘ │
        │ ┌─────────────────────────────────────┐ │
        │ │   Answer Evaluator                  │ │
        │ │  - Score on 5 criteria              │ │
        │ │  - Adapt difficulty                 │ │
        │ │  - Detect early termination         │ │
        │ └─────────────────────────────────────┘ │
        │ ┌─────────────────────────────────────┐ │
        │ │   Report Generator                  │ │
        │ │  - Calculate final scores           │ │
        │ │  - Generate comprehensive report    │ │
        │ │  - Provide hiring recommendation    │ │
        │ └─────────────────────────────────────┘ │
        └─────────────────────────────────────────┘
                              ↕
┌─────────────────────────────────────────────────────────────────┐
│                     External Services                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │            OpenAI GPT-4 API                              │  │
│  │  - Natural language processing                           │  │
│  │  - Question generation                                   │  │
│  │  - Answer evaluation                                     │  │
│  │  - Report generation                                     │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Component Architecture

### 1. **Frontend Layer** (`frontend/index.html`)

**Responsibilities:**
- User interface for interview interaction
- Resume and JD input collection
- Real-time question display
- Answer submission interface
- Timer and progress tracking
- Report visualization

**Key Features:**
- Responsive design (mobile, tablet, desktop)
- Real-time timer with visual feedback
- Dynamic score display
- Comprehensive report dashboard

**Technologies:**
- HTML5, CSS3
- Vanilla JavaScript (no dependencies)
- Fetch API for REST calls

---

### 2. **Backend - Flask API** (`backend/main.py`)

**Responsibilities:**
- RESTful API endpoints
- Request validation
- Session routing
- Error handling

**Key Endpoints:**
```
POST   /api/interview/start              - Initialize interview
GET    /api/interview/<id>/question      - Get next question
POST   /api/interview/<id>/answer        - Submit answer
GET    /api/interview/<id>/status        - Get session status
POST   /api/interview/<id>/complete      - Finalize interview
```

**Request/Response Handling:**
- JSON serialization/deserialization
- CORS configuration for frontend access
- Error responses with meaningful messages

---

### 3. **Interview Controller** (`modules/interview_controller.py`)

**Responsibilities:**
- Orchestrate entire interview flow
- Manage interview sessions
- Coordinate between modules
- Track interview state and metrics

**Key Methods:**
- `start_interview()` - Initialize new session
- `get_next_question()` - Generate adaptive question
- `submit_answer()` - Evaluate and score response
- `complete_interview()` - Generate final report

**Session Management:**
```python
InterviewSession:
- session_id: unique identifier
- candidate_name: name of candidate
- job_title: position applied for
- status: not_started | in_progress | completed | terminated_early
- performance_scores: list of all scores
- current_difficulty: Easy | Medium | Hard
```

---

### 4. **Resume Parser** (`modules/resume_parser.py`)

**Responsibilities:**
- Extract structured data from resume
- Identify key skills and experience
- Calculate role relevance

**Process Flow:**
```
Raw Resume Text
    ↓
GPT-4 Extraction Prompt
    ↓
Parse JSON Response
    ↓
Resume Object (skills, experience, education, projects)
    ↓
Role Relevance Scoring
```

**Output Model:**
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

---

### 5. **Job Description Parser** (`modules/jd_parser.py`)

**Responsibilities:**
- Extract requirements from job description
- Identify technical focus areas
- Map role level to difficulty

**Process Flow:**
```
Raw JD Text
    ↓
GPT-4 Extraction Prompt
    ↓
Parse JSON Response
    ↓
JobDescription Object
    ↓
Difficulty Level Mapping
```

**Output Model:**
```python
JobDescription:
- job_title: str
- company: str
- level: junior | mid | senior
- required_skills: List[str]
- preferred_skills: List[str]
- years_required: int
- key_responsibilities: List[str]
- technical_focus: List[str]
- soft_skills_required: List[str]
```

---

### 6. **Question Generator** (`modules/question_generator.py`)

**Responsibilities:**
- Generate contextual interview questions
- Balance question types
- Adapt difficulty based on performance
- Ensure relevance to role

**Algorithm:**

**Question Type Selection:**
```python
cycle_through: [Technical, Behavioral, Scenario, Conceptual]
least_asked = count_by_type()
next_type = select_from(least_asked)
```

**Difficulty Determination:**
```python
initial_difficulty = job_level_to_difficulty(JD.level)
if score >= 75:
    difficulty = increase(difficulty)
elif score <= 50:
    difficulty = decrease(difficulty)
else:
    difficulty = maintain(difficulty)
```

**Question Generation Prompt:**
```
Input:
- Resume context
- Job requirements
- Question type
- Difficulty level
- Previous questions (to avoid repetition)
- Candidate skills

Output:
- Question text
- Expected keywords
- Evaluation criteria
- Time allocation
```

---

### 7. **Answer Evaluator** (`modules/answer_evaluator.py`)

**Responsibilities:**
- Evaluate candidate answers
- Score on multiple criteria
- Adapt difficulty for next question
- Detect early termination conditions

**Scoring Framework:**

**Evaluation Criteria:**
```
1. Accuracy (30%)        - Technical correctness
2. Clarity (25%)         - Communication quality
3. Depth (25%)           - Understanding depth
4. Relevance (15%)       - Relevance to role
5. Time Efficiency (5%)  - Speed vs quality
```

**Score Calculation:**
```python
weighted_score = (
    accuracy * 0.30 +
    clarity * 0.25 +
    depth * 0.25 +
    relevance * 0.15 +
    time_efficiency * 0.05
)
```

**Difficulty Adaptation Algorithm:**
```
if score >= INCREASE_THRESHOLD (75):
    difficulty = increase(if not at max)
elif score <= DECREASE_THRESHOLD (50):
    difficulty = decrease(if not at min)
else:
    difficulty = maintain()
```

**Early Termination Logic:**
```python
if len(performance_history) >= 2:
    if all(score < threshold for score in last_2):
        terminate = True
        
if len(performance_history) >= 3:
    avg_3 = average(last_3_scores)
    if avg_3 < threshold * 0.7:
        terminate = True
```

---

### 8. **Report Generator** (`modules/report_generator.py`)

**Responsibilities:**
- Synthesize all interview data
- Generate comprehensive assessment
- Provide actionable feedback
- Recommend hiring decision

**Report Sections:**

1. **Performance Metrics**
   - Overall score (0-100)
   - Performance category (Strong/Average/Needs Improvement)
   - Individual question scores

2. **Skill Assessment**
   - Score per required skill
   - Proficiency level (Strong/Average/Weak)
   - Skill-job alignment

3. **Analysis**
   - Technical depth assessment
   - Communication skills evaluation
   - Problem-solving ability

4. **Recommendations**
   - Specific strengths (top 3-5)
   - Areas for improvement (top 3-5)
   - Actionable feedback (ranked by priority)

5. **Hiring Decision**
   - Recommendation (Strong Hire/Hire/Maybe/No Hire)
   - Role fit percentage
   - Skills match percentage

---

## Data Flow

### Interview Session Lifecycle

```
1. START INTERVIEW
   ├─ Parse Resume
   ├─ Parse Job Description
   ├─ Create Session
   └─ Store Context
   
2. FOR EACH QUESTION (1 to 8)
   ├─ Generate Adaptive Question
   ├─ Get User Answer
   ├─ Submit for Evaluation
   ├─ Evaluate Answer
   │  ├─ Score on 5 criteria
   │  ├─ Generate feedback
   │  └─ Adapt difficulty
   ├─ Check Termination Conditions
   │  └─ Terminate if threshold met
   └─ Next Question
   
3. COMPLETE INTERVIEW
   ├─ Collect All Data
   ├─ Generate Report
   └─ Return Comprehensive Results
```

### Question Generation Flow

```
Get Next Question
    ↓
Select Question Type (balanced)
    ↓
Determine Difficulty (adaptive)
    ↓
Create Prompt (context-aware)
    ↓
Call GPT-4 API
    ↓
Parse JSON Response
    ↓
Create InterviewQuestion Object
    ↓
Store & Return Question
```

### Answer Evaluation Flow

```
Receive Answer + Metadata
    ↓
Create Evaluation Prompt
    ↓
Call GPT-4 API
    ↓
Parse Scores (5 criteria)
    ↓
Calculate Weighted Score
    ↓
Determine Next Difficulty
    ↓
Check Termination Conditions
    ↓
Return Evaluation + Status
```

---

## Adaptive Difficulty System

### Mechanism

The system dynamically adjusts question difficulty based on real-time performance:

**Difficulty Levels:**
- Easy: Basic concepts, standard implementations
- Medium: Intermediate problems, design consideration
- Hard: Complex scenarios, system-level thinking

**Adaptation Rules:**

```python
if performance_score >= 75:
    difficulty = increase()  # Easy → Medium → Hard
    
elif performance_score <= 50:
    difficulty = decrease()  # Hard → Medium → Easy
    
else:
    difficulty = maintain()  # Keep current level
```

**Benefits:**
- Challenges strong candidates
- Doesn't overwhelm weak candidates
- More accurate assessment
- Better candidate experience

---

## Performance Threshold System

### Early Termination Logic

Interviews terminate early when:

1. **Last 2 Consecutive Scores Below Threshold**
   ```python
   if score_n < 40 AND score_n-1 < 40:
       terminate = True
   ```

2. **Average of Last 3 Scores Critically Low**
   ```python
   if avg(score_n-2, score_n-1, score_n) < 28:  # 40 * 0.7
       terminate = True
   ```

**Benefits:**
- Respects candidate time
- Stops prolonging poor performance
- Still collects sufficient data
- Humane interview experience

---

## LLM Integration

### API Calls

The system makes GPT-4 API calls for:

1. **Resume Parsing** (1 call at start)
2. **Job Description Parsing** (1 call at start)
3. **Question Generation** (1 call per question, max 8)
4. **Answer Evaluation** (1 call per answer, max 8)
5. **Report Generation** (1 call at end)

**Total Calls:** ~18-20 per interview session

### Prompt Engineering Strategy

**Key Principles:**
- Structured JSON output
- Deterministic responses
- Specific evaluation criteria
- Role-based context

**Example Question Prompt:**
```
Role: Software Engineer (Mid-level)
Resume Highlights: Python, AWS, System Design
Question Type: Technical
Difficulty: Medium

Generate a technical interview question about distributed systems
that's relevant to this role and difficulty level.

Response format: JSON with fields...
```

---

## Configuration System

### Settings Management

**`config/settings.py`:**
```python
INTERVIEW_CONFIG = {
    "max_questions": 8,
    "time_per_question": 120,
    "initial_difficulty": "Medium",
    "performance_threshold": 40
}

SCORING_CONFIG = {
    "accuracy_weight": 0.30,
    "clarity_weight": 0.25,
    "depth_weight": 0.25,
    "relevance_weight": 0.15,
    "time_efficiency_weight": 0.05
}

DIFFICULTY_ADAPTATION = {
    "increase_threshold": 75,
    "decrease_threshold": 50,
    "stability_range": [50, 75]
}
```

**Environment Configuration:**
- `OPENAI_API_KEY` - GPT-4 API key
- `FLASK_ENV` - development/production
- `DEBUG` - Enable/disable debug mode

---

## Error Handling & Resilience

### Error Types

1. **API Errors**
   - OpenAI API failures
   - Network timeouts
   - Rate limiting

2. **Validation Errors**
   - Missing fields
   - Invalid session ID
   - Malformed JSON

3. **Processing Errors**
   - JSON parsing failures
   - Prompt generation issues
   - Scoring calculation errors

### Error Responses

```python
{
    "error": "Descriptive error message",
    "status": 400/404/500,
    "recovery": "Suggested recovery action"
}
```

---

## Performance Considerations

### Optimization Strategies

1. **Caching**
   - Cache parsed resume/JD
   - Reuse session context

2. **Batch Processing**
   - Pre-generate question batches
   - Parallel API calls where possible

3. **Response Time**
   - Average API call: 3-5 seconds
   - Total interview time: 15-20 minutes

### Scalability

**Current Limitations:**
- Single-threaded session handling
- Sequential API calls

**Future Improvements:**
- Async processing with queues
- Caching layer (Redis)
- Database for session persistence
- Load balancing for multiple servers

---

## Security Considerations

### Current Implementation

- No authentication (for hackathon)
- No encryption (HTTPS recommended)
- No rate limiting

### Production Requirements

- JWT/OAuth authentication
- HTTPS/TLS encryption
- API rate limiting
- Input validation & sanitization
- SQL injection prevention
- XSS protection
- CSRF tokens

---

## Testing Strategy

### Test Coverage

1. **Unit Tests**
   - Parser functions
   - Score calculations
   - Helper utilities

2. **Integration Tests**
   - Full interview workflow
   - Module interactions
   - API endpoints

3. **Edge Cases**
   - Empty inputs
   - Invalid scores
   - Performance history variations

### Test Execution

```bash
pytest tests/ -v
pytest tests/ --cov  # With coverage
```

---

## Deployment Architecture

### Development Setup

```
Laptop/Local Machine
├─ Backend (Python/Flask)
│  └─ localhost:5000
└─ Frontend (HTML/JS)
   └─ file:// or localhost
```

### Production Setup (Recommended)

```
Cloud Provider (AWS/GCP/Azure)
├─ Frontend
│  └─ Static hosting (S3, Cloud Storage)
├─ Backend
│  ├─ API Server (Docker container)
│  ├─ Load Balancer
│  └─ Auto-scaling group
├─ Database
│  ├─ Session storage
│  └─ Interview history
├─ Cache
│  └─ Redis for caching
└─ Monitoring
   ├─ CloudWatch/Stackdriver
   └─ Error tracking
```

---

## Monitoring & Analytics

### Metrics to Track

1. **Performance Metrics**
   - Average interview score
   - Completion rate
   - Average time per interview

2. **Candidate Metrics**
   - Questions per candidate
   - Difficulty progression
   - Time management score

3. **System Metrics**
   - API response times
   - Error rates
   - Concurrent interviews

4. **Business Metrics**
   - Hiring recommendations
   - Correlations with actual hiring decisions
   - Interview quality scores

---

## Future Enhancements

### Phase 2 Features
- [ ] Video/audio interview support
- [ ] Multiple interviewer personas
- [ ] Coding problem integration
- [ ] Whiteboard simulation

### Phase 3 Features
- [ ] Interview group feature
- [ ] Team assessments
- [ ] Performance analytics dashboard
- [ ] Candidate comparison reports

### Phase 4 Features
- [ ] Multi-language support
- [ ] Custom domain expertise
- [ ] Interview template library
- [ ] Integration with ATS systems

---

## Conclusion

The Hack2Hire platform represents a sophisticated approach to technical interview simulation, combining modern AI capabilities with practical interview principles. The modular architecture allows for easy extension and improvement, while the adaptive algorithms ensure fair and comprehensive evaluation of candidates.

The system successfully addresses the core challenge: providing realistic, objective, and scalable interview preparation that adapts to individual candidate levels and generates actionable insights for improvement.
