# Hack2Hire - AI-Powered Mock Interview Platform

An intelligent mock interview platform that uses AI to simulate real-world technical interviews with adaptive difficulty, objective scoring, and comprehensive feedback.

## 🎯 Key Features

### Core Capabilities
- **Resume Analysis**: Automatically extracts skills, experience, and background from resumes
- **Job Description Alignment**: Analyzes JD to generate role-specific questions
- **Adaptive Questioning**: Dynamically adjusts difficulty based on candidate performance
- **Real-time Scoring**: Evaluates answers on accuracy, clarity, depth, relevance, and time efficiency
- **Early Termination**: Stops interview if performance falls below threshold
- **Comprehensive Reporting**: Generates detailed feedback with strengths, weaknesses, and actionable recommendations

### Intelligent Features
- **Varying Difficulty Levels**: Easy → Medium → Hard questions that adapt in real-time
- **Multiple Question Types**: Technical, Behavioral, Scenario-Based, Conceptual
- **Time Management Tracking**: Enforces strict time constraints per question
- **Performance Thresholds**: Automatic interview termination for poor performance
- **Skill-Based Assessment**: Detailed breakdown by skill area

## 🏗️ Project Structure

```
Hack2Hire/
├── backend/
│   ├── config/
│   │   └── settings.py           # Configuration and constants
│   ├── modules/
│   │   ├── resume_parser.py      # Resume analysis and extraction
│   │   ├── jd_parser.py          # Job description parsing
│   │   ├── question_generator.py # Adaptive question generation
│   │   ├── answer_evaluator.py   # Answer evaluation and scoring
│   │   ├── interview_controller.py # Interview orchestration
│   │   └── report_generator.py   # Report generation
│   ├── utils/
│   │   ├── constants.py          # Constants and enums
│   │   ├── helpers.py            # Utility functions
│   │   └── prompts.py            # LLM prompts
│   ├── main.py                   # Flask API server
│   └── requirements.txt          # Dependencies
├── frontend/
│   └── index.html                # Interactive web interface
├── tests/
│   └── test_interview.py         # Test suite
└── README.md                     # This file
```

## 🚀 Getting Started

### Prerequisites
- Python 3.8+
- OpenAI API Key
- pip (Python package manager)

### Installation

1. **Clone/Setup the Project**
   ```bash
   cd Hack2Hire
   ```

2. **Create and Activate Virtual Environment**
   ```bash
   python -m venv venv
   # On Windows
   venv\Scripts\activate
   # On macOS/Linux
   source venv/bin/activate
   ```

3. **Install Dependencies**
   ```bash
   cd backend
   pip install -r requirements.txt
   ```

4. **Setup Environment Variables**
   ```bash
   # Create .env file in backend directory
   cp .env.example .env
   # Edit .env and add your OpenAI API key
   ```

5. **Run the Server**
   ```bash
   python main.py
   ```
   Server will start at `http://localhost:5000`

6. **Access the Platform**
   - Open `frontend/index.html` in your browser
   - Or navigate to `http://localhost:5000` after setting up frontend routing

## 📖 API Endpoints

### Start Interview
```
POST /api/interview/start
Content-Type: application/json

{
    "resume": "resume text content",
    "job_description": "job description text",
    "candidate_name": "John Doe" (optional)
}

Response:
{
    "success": true,
    "session_id": "uuid",
    "job_title": "Software Engineer",
    "total_questions": 8
}
```

### Get Next Question
```
GET /api/interview/{session_id}/question

Response:
{
    "success": true,
    "question": {
        "id": 1,
        "question": "Explain your approach to system design...",
        "type": "Technical",
        "difficulty": "Medium",
        "keywords": ["scalability", "performance"],
        "time_allowed": 120
    }
}
```

### Submit Answer
```
POST /api/interview/{session_id}/answer
Content-Type: application/json

{
    "question_id": 1,
    "answer": "candidate's answer text",
    "time_taken": 95
}

Response:
{
    "success": true,
    "evaluation": {
        "score": 78,
        "feedback": "Good approach...",
        "strengths": ["Clear explanation"],
        "areas_for_improvement": ["Could include more examples"],
        "next_difficulty": "Hard",
        "questions_remaining": 7
    }
}
```

### Get Session Status
```
GET /api/interview/{session_id}/status

Response:
{
    "success": true,
    "status": {
        "session_id": "uuid",
        "status": "in_progress",
        "questions_asked": 3,
        "total_questions": 8,
        "average_score": 72.5
    }
}
```

### Complete Interview
```
POST /api/interview/{session_id}/complete

Response:
{
    "success": true,
    "report": {
        "overall_score": 75,
        "performance_category": "Strong",
        "interview_readiness": 75,
        "strengths": [...],
        "weaknesses": [...],
        "actionable_feedback": [...],
        "hiring_recommendation": "STRONG HIRE"
    }
}
```

## 🎮 Usage Example

### 1. Start an Interview
Provide resume and job description to start an interview session

### 2. Answer Questions
- Read the AI-generated question
- Provide your answer within time limit
- Submit and receive immediate evaluation

### 3. Adaptive Difficulty
- Performance above 75/100 → Difficulty increases
- Performance below 50/100 → Difficulty decreases
- Performance 50-75/100 → Difficulty stays same

### 4. Interview Termination
- Interview auto-terminates if performance drops below threshold (default: 40/100)
- Prevents wasteful continuation after consistent poor performance

### 5. Get Comprehensive Report
- Overall interview readiness score
- Performance breakdown by skill
- Specific strengths and weaknesses
- Actionable improvement recommendations
- Hiring readiness indicator

## 📊 Scoring Mechanism

### Evaluation Criteria
- **Accuracy** (30%): Technical correctness
- **Clarity** (25%): Clear communication
- **Depth** (25%): Depth of understanding
- **Relevance** (15%): Relevance to job role
- **Time Efficiency** (5%): Answering within time constraints

### Performance Categories
- **Strong** (75-100): Excellent performance
- **Average** (50-74): Acceptable performance
- **Needs Improvement** (0-49): Significant improvement needed

## 🔧 Configuration

### Interview Settings (config/settings.py)
```python
INTERVIEW_CONFIG = {
    "max_questions": 8,              # Total questions in interview
    "time_per_question": 120,        # Seconds per question
    "difficulty_levels": ["Easy", "Medium", "Hard"],
    "initial_difficulty": "Medium",
    "performance_threshold": 40      # Early termination threshold
}

DIFFICULTY_ADAPTATION = {
    "increase_threshold": 75,        # Score to increase difficulty
    "decrease_threshold": 50,        # Score to decrease difficulty
    "stability_range": [50, 75]      # Range for stability
}
```

## 🧪 Testing

### Run Tests
```bash
cd backend
pytest tests/
```

### Example Test
```python
def test_resume_parsing():
    # Initialize parser
    parser = ResumeParser(llm_client)
    
    # Parse resume
    resume = parser.parse(resume_text)
    
    # Assert
    assert resume.name is not None
    assert len(resume.skills) > 0
```

## 🎯 Algorithm Details

### Adaptive Difficulty Algorithm
1. **Baseline**: Start with Medium difficulty
2. **Score Analysis**: After each answer, evaluate score
3. **Adjustment Logic**:
   - If score ≥ 75 → Increase difficulty (if not at max)
   - If score ≤ 50 → Decrease difficulty (if not at min)
   - Otherwise → Keep same difficulty
4. **Progression**: Questions get progressively harder/easier based on performance

### Performance Threshold Algorithm
Terminates early if:
- Last 2 scores both below threshold, OR
- Average of last 3 scores < (threshold × 0.7)

### Score Calculation
```
Overall Score = (Accuracy × 0.30) + (Clarity × 0.25) + 
                (Depth × 0.25) + (Relevance × 0.15) + 
                (TimeEfficiency × 0.05)
```

## 🤖 AI Integration

### LLM Usage
- **Model**: GPT-4
- **Resume Analysis**: Structured extraction with JSON output
- **Question Generation**: Context-aware, adaptive questions
- **Answer Evaluation**: Objective scoring with detailed feedback
- **Report Generation**: Comprehensive analysis and recommendations

### Prompt Engineering
All prompts are structured for:
- Deterministic JSON output
- Minimal hallucination
- Consistent evaluation criteria
- Real-world interview simulation

## 📈 Performance Metrics

### Sample Output
```
Overall Score: 78/100
Performance Category: Strong
Interview Readiness: 78%

Skills Breakdown:
- Python: 85/100 (Strong)
- System Design: 72/100 (Average)
- Communication: 75/100 (Strong)

Hiring Recommendation: STRONG HIRE
Time Management: Excellent (90% on-time)
```

## 🐛 Troubleshooting

### Issue: OpenAI API Key Error
- Verify API key in .env file
- Check API key has proper permissions
- Ensure account has sufficient credits

### Issue: Questions Not Loading
- Check internet connection
- Verify OpenAI service status
- Check API rate limits

### Issue: Incorrect Scoring
- Verify scoring weights in config
- Check LLM prompt formatting
- Review evaluation criteria

## 📝 Limitations & Future Improvements

### Current Limitations
- Requires OpenAI API access
- Text-only answers (no video/audio)
- Limited to tech roles
- Single interviewer persona

### Future Enhancements
- [ ] Video/audio interview support
- [ ] Multiple interviewer styles
- [ ] Specialized domain coverage
- [ ] Interview group feature
- [ ] Performance analytics dashboard
- [ ] Resume improvement suggestions
- [ ] Interview transcript analysis
- [ ] Multi-language support

## 🤝 Contributing

Contributions are welcome! Areas for contribution:
- Additional evaluation criteria
- Improved LLM prompts
- New question types
- Performance optimizations
- Bug fixes and testing

## 📄 License

This project is part of Hack2Hire hackathon.

## 📧 Support

For issues or questions, please refer to the documentation or contact the development team.

---

**Built with ❤️ for Hack2Hire**
