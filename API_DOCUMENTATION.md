# API DOCUMENTATION

## Hack2Hire - AI-Powered Mock Interview Platform API

### Base URL
```
http://localhost:5000/api
```

### Authentication
Currently no authentication required. In production, implement JWT or OAuth.

---

## Endpoints

### 1. Health Check
Check if the service is running.

**Endpoint:** `GET /health`

**Response:**
```json
{
    "status": "healthy",
    "service": "Hack2Hire Interview Platform"
}
```

---

### 2. Start Interview
Initialize a new interview session.

**Endpoint:** `POST /api/interview/start`

**Content-Type:** `application/json`

**Request Body:**
```json
{
    "resume": "resume text content here",
    "job_description": "job description text here",
    "candidate_name": "John Doe"  // optional
}
```

**Response (201 Created):**
```json
{
    "success": true,
    "session_id": "550e8400-e29b-41d4-a716-446655440000",
    "message": "Interview started for John Doe",
    "job_title": "Software Engineer",
    "total_questions": 8
}
```

**Error Response (400):**
```json
{
    "error": "Missing required fields: resume, job_description"
}
```

**Notes:**
- `resume` and `job_description` are required
- `candidate_name` is optional; defaults to "Candidate"
- Session ID is used for all subsequent requests
- Interview starts with Medium difficulty by default

---

### 3. Get Next Question
Retrieve the next interview question.

**Endpoint:** `GET /api/interview/{session_id}/question`

**Path Parameters:**
- `session_id` (string, required): Session identifier from /start

**Response (200 OK):**
```json
{
    "success": true,
    "question": {
        "id": 1,
        "question": "Explain how you would design a system to handle 1 million concurrent users.",
        "type": "Technical",
        "difficulty": "Medium",
        "keywords": ["scalability", "load_balancing", "caching", "database_design"],
        "time_allowed": 120,
        "evaluation_criteria": ["system_design_knowledge", "scalability_awareness", "practical_experience"]
    }
}
```

**Error Response (404):**
```json
{
    "error": "Session xyz not found"
}
```

**Error Response (400):**
```json
{
    "error": "Interview already completed"
}
```

**Notes:**
- Questions are adaptive based on previous performance
- `time_allowed` is in seconds (default: 120)
- Question type cycles through: Technical → Behavioral → Scenario-Based → Conceptual
- Difficulty adapts based on previous answers

---

### 4. Submit Answer
Submit and evaluate a candidate's answer.

**Endpoint:** `POST /api/interview/{session_id}/answer`

**Path Parameters:**
- `session_id` (string, required): Session identifier

**Content-Type:** `application/json`

**Request Body:**
```json
{
    "question_id": 1,
    "answer": "Here's my approach to system design...",
    "time_taken": 95
}
```

**Response (200 OK):**
```json
{
    "success": true,
    "evaluation": {
        "score": 78,
        "feedback": "Good systematic approach to the problem. You covered scalability concerns well.",
        "strengths": [
            "Clear explanation of architecture",
            "Consideration of edge cases",
            "Knowledge of modern technologies"
        ],
        "areas_for_improvement": [
            "Could elaborate more on fault tolerance",
            "Discuss specific trade-offs in more detail"
        ],
        "next_difficulty": "Hard",
        "interview_status": "in_progress",
        "questions_remaining": 7
    }
}
```

**Error Response (404):**
```json
{
    "error": "Session xyz not found"
}
```

**Field Descriptions:**
- `score`: Overall performance score (0-100)
- `feedback`: AI-generated feedback on the answer
- `strengths`: List of strong points in the answer
- `areas_for_improvement`: Suggested improvements
- `next_difficulty`: Recommended next question difficulty
- `interview_status`: Current interview status
- `questions_remaining`: Questions left in interview

**Status Possibilities:**
- `in_progress`: Interview continues
- `terminated_early`: Interview ended due to poor performance

**Notes:**
- Score breakdown: Accuracy (30%) + Clarity (25%) + Depth (25%) + Relevance (15%) + Time Efficiency (5%)
- Difficulty adapts:
  - Score ≥ 75 → Increase difficulty
  - Score ≤ 50 → Decrease difficulty
  - 50 < Score < 75 → Maintain difficulty
- Early termination if last 2 scores < threshold (default: 40/100)

---

### 5. Get Session Status
Get current interview session status and metrics.

**Endpoint:** `GET /api/interview/{session_id}/status`

**Path Parameters:**
- `session_id` (string, required): Session identifier

**Response (200 OK):**
```json
{
    "success": true,
    "status": {
        "session_id": "550e8400-e29b-41d4-a716-446655440000",
        "status": "in_progress",
        "candidate_name": "John Doe",
        "job_title": "Software Engineer",
        "questions_asked": 3,
        "total_questions": 8,
        "average_score": 72.5,
        "current_difficulty": "Medium",
        "termination_reason": null
    }
}
```

**Status Values:**
- `not_started`: Interview initialized but not yet started
- `in_progress`: Interview ongoing
- `completed`: Interview finished successfully
- `terminated_early`: Interview ended early due to performance threshold

**Notes:**
- `average_score`: Average of all scores so far (0-100)
- `current_difficulty`: Difficulty for next question
- `termination_reason`: Why interview ended early (if applicable)

---

### 6. Complete Interview
Finalize interview and generate comprehensive report.

**Endpoint:** `POST /api/interview/{session_id}/complete`

**Path Parameters:**
- `session_id` (string, required): Session identifier

**Response (200 OK):**
```json
{
    "success": true,
    "message": "Interview completed successfully",
    "report": {
        "candidate_information": {
            "name": "John Doe",
            "experience_years": 5,
            "skills": ["Python", "JavaScript", "AWS", "Docker", "System Design"]
        },
        "interview_details": {
            "position": "Software Engineer",
            "questions_asked": 8,
            "duration_minutes": 18.5,
            "interview_date": "2024-01-15 14:30:00"
        },
        "performance_metrics": {
            "overall_score": 75,
            "performance_category": "Strong",
            "interview_readiness": 75,
            "individual_scores": [78, 72, 75, 70, 80, 74, 76, 73]
        },
        "skill_assessment": {
            "Python": {"score": 82, "level": "Strong"},
            "System Design": {"score": 75, "level": "Average"},
            "AWS": {"score": 78, "level": "Strong"},
            "Communication": {"score": 76, "level": "Strong"},
            "Problem Solving": {"score": 74, "level": "Average"}
        },
        "analysis": {
            "technical_depth": {
                "assessment": "Good technical knowledge demonstrated",
                "score": 76
            },
            "communication_skills": {
                "assessment": "Clear and structured communication",
                "score": 76
            },
            "problem_solving": {
                "assessment": "Methodical approach to problem-solving",
                "score": 74
            }
        },
        "strengths": [
            "Strong system design knowledge",
            "Good communication of ideas",
            "Practical implementation experience",
            "Understanding of trade-offs"
        ],
        "areas_for_improvement": [
            "Could dive deeper into edge cases",
            "Elaborate more on fault tolerance",
            "Consider more alternative approaches"
        ],
        "role_fit_assessment": {
            "fit_percentage": 82,
            "required_skills_match": 85,
            "assessment": "Strong fit for the Software Engineer role"
        },
        "recommendations": {
            "hiring_recommendation": "STRONG HIRE - Highly recommend proceeding to next round",
            "actionable_feedback": [
                "Focus on distributed systems concepts",
                "Practice mock interviews regularly",
                "Stay updated with latest cloud technologies"
            ],
            "next_steps": "Schedule technical assessment or move to next interview round"
        },
        "detailed_breakdown": {
            "question_analysis": [...],
            "time_management": {
                "total_time_used_minutes": 18.5,
                "total_time_available_minutes": 16,
                "on_time_answers": 7,
                "on_time_percentage": 87.5,
                "assessment": "Excellent time management"
            }
        }
    }
}
```

**Report Sections:**
- `candidate_information`: Parsed resume data
- `interview_details`: Interview session metadata
- `performance_metrics`: Overall scores and breakdowns
- `skill_assessment`: Performance by skill area
- `analysis`: Detailed assessment areas
- `strengths`: Positive findings
- `areas_for_improvement`: Development opportunities
- `role_fit_assessment`: Fit for the specific role
- `recommendations`: Hiring decision and next steps

**Hiring Recommendations:**
- `STRONG HIRE` (Score ≥ 75): Excellent candidate
- `HIRE` (Score ≥ 60): Good fit
- `MAYBE` (Score ≥ 50): Needs improvement
- `NO HIRE` (Score < 50): Does not meet requirements

---

## Error Handling

### Common Error Codes

| Code | Description | Example |
|------|-------------|---------|
| 400 | Bad Request - Missing or invalid fields | Missing required fields |
| 404 | Not Found - Session ID doesn't exist | Session xyz not found |
| 500 | Server Error - Internal processing error | OpenAI API failure |

### Error Response Format
```json
{
    "error": "Descriptive error message"
}
```

---

## Rate Limits

Currently no rate limiting implemented. In production, implement:
- 10 requests/minute per IP
- 100 interviews/hour per API key

---

## Best Practices

### 1. Session Management
- Store `session_id` securely
- One session per interview attempt
- Sessions expire after 24 hours of inactivity

### 2. Timing
- Respect `time_allowed` for questions
- `time_taken` should be reasonably accurate
- Timeouts are enforced

### 3. Answer Quality
- Provide thoughtful, detailed answers
- Avoid very short answers
- Focus on clarity and completeness

### 4. Error Handling
```javascript
try {
    const response = await fetch(endpoint);
    if (!response.ok) {
        const error = await response.json();
        console.error('API Error:', error.error);
    }
} catch (error) {
    console.error('Network Error:', error);
}
```

---

## Example Workflow

### 1. Initialize Interview
```bash
curl -X POST http://localhost:5000/api/interview/start \
  -H "Content-Type: application/json" \
  -d '{
    "resume": "...",
    "job_description": "...",
    "candidate_name": "John Doe"
  }'
```

### 2. Get Questions & Submit Answers (Loop)
```bash
# Get question
curl http://localhost:5000/api/interview/{session_id}/question

# Submit answer
curl -X POST http://localhost:5000/api/interview/{session_id}/answer \
  -H "Content-Type: application/json" \
  -d '{
    "question_id": 1,
    "answer": "...",
    "time_taken": 95
  }'
```

### 3. Complete Interview
```bash
curl -X POST http://localhost:5000/api/interview/{session_id}/complete
```

---

## Support

For API issues or feature requests, please refer to the main README or contact the development team.
