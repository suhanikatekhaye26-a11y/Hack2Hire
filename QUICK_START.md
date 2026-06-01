# QUICK START GUIDE - Hack2Hire Platform

## 5-Minute Setup

### Step 1: Prerequisites Check
- Python 3.8 or higher
- OpenAI API key (sign up at https://platform.openai.com/)
- pip (usually comes with Python)

### Step 2: Clone/Setup Project
```bash
cd Hack2Hire
```

### Step 3: Create Virtual Environment
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

### Step 4: Install Dependencies
```bash
cd backend
pip install -r requirements.txt
```

### Step 5: Configure Environment
```bash
# Copy example env file
cp .env.example .env

# Edit .env and add your OpenAI API key
# OPENAI_API_KEY=sk-your-api-key-here
```

On Windows, you can edit with:
```bash
notepad .env
```

### Step 6: Run the Server
```bash
python main.py
```

You should see:
```
============================================================
Hack2Hire - AI-Powered Mock Interview Platform
============================================================
Starting server on http://localhost:5000
API Documentation available at http://localhost:5000
============================================================
```

### Step 7: Access the Platform
- **Web Interface:** Open `frontend/index.html` in your browser
- **Or:** Navigate to `http://localhost:5000` if serving frontend from Flask

---

## First Interview - Step by Step

### 1. Paste Your Resume
```
Copy your resume content into the "Your Resume" text area.
Example includes:
- Skills section
- Experience section
- Education details
- Projects
```

### 2. Paste Job Description
```
Copy the job description you're applying for.
Example includes:
- Required skills
- Responsibilities
- Experience level (junior/mid/senior)
```

### 3. Start Interview
```
Click "Start Interview"
Session ID will be generated
Interview begins with first question
```

### 4. Answer Questions
```
- Read the question carefully
- Think through your answer
- Type your response
- Submit within the time limit
```

### 5. Get Instant Feedback
```
- Score for each answer
- Specific feedback points
- Suggestions for improvement
- Next question difficulty auto-adjusts
```

### 6. View Final Report
```
After all questions:
- Overall interview readiness score
- Performance breakdown by skill
- Strengths and weaknesses
- Hiring recommendation
- Actionable next steps
```

---

## Troubleshooting

### Issue: "OPENAI_API_KEY environment variable not set"
**Solution:**
1. Open `.env` file in backend folder
2. Add your API key: `OPENAI_API_KEY=sk-...`
3. Save and restart the server

### Issue: "ModuleNotFoundError" or import errors
**Solution:**
```bash
# Make sure you're in backend directory
cd backend

# Reinstall dependencies
pip install -r requirements.txt

# Make sure virtual env is activated
# Windows: venv\Scripts\activate
# macOS/Linux: source venv/bin/activate
```

### Issue: "Connection refused" on localhost:5000
**Solution:**
1. Verify server is running (check terminal for startup message)
2. Try accessing http://127.0.0.1:5000
3. Check if port 5000 is in use: Try different port in main.py

### Issue: Questions not loading
**Solution:**
1. Check OpenAI API key is valid
2. Verify you have API credits
3. Check internet connection
4. Check OpenAI service status (https://status.openai.com)

### Issue: Very slow API responses
**Solution:**
- First request takes 3-5 seconds (expected)
- Subsequent requests should be faster
- Check your internet connection
- Try again - may be OpenAI API load

---

## Testing the System

### Run Unit Tests
```bash
cd backend
pytest tests/ -v
```

### Run Sample Interview
```bash
cd backend
python sample_usage.py
```

This demonstrates:
- Starting an interview
- Getting questions
- Submitting answers
- Generating reports
- All without the web interface

---

## API Testing with cURL

### Test 1: Start Interview
```bash
curl -X POST http://localhost:5000/api/interview/start \
  -H "Content-Type: application/json" \
  -d '{
    "resume": "5+ years Python, AWS...",
    "job_description": "Senior Python Developer...",
    "candidate_name": "John Doe"
  }'
```

### Test 2: Get Question
```bash
curl http://localhost:5000/api/interview/{session_id}/question
```

### Test 3: Submit Answer
```bash
curl -X POST http://localhost:5000/api/interview/{session_id}/answer \
  -H "Content-Type: application/json" \
  -d '{
    "question_id": 1,
    "answer": "Here is my detailed answer...",
    "time_taken": 95
  }'
```

---

## Common Usage Patterns

### Pattern 1: Full Interview Flow
```
1. User fills in resume and JD
2. Clicks "Start Interview"
3. Receives first question
4. Answers 4-8 questions (based on performance)
5. Interview completes
6. Views detailed report
```

### Pattern 2: Quick Practice
```
1. Use sample resume/JD
2. Run interview
3. Get immediate feedback
4. Identify weak areas
5. Focus practice on those areas
```

### Pattern 3: Role Preparation
```
1. Find target job description
2. Tailor resume to role
3. Run multiple interview sessions
4. Track score improvement over time
5. Iterate until ready
```

---

## Configuration Tips

### To Change Number of Questions
Edit `backend/config/settings.py`:
```python
INTERVIEW_CONFIG = {
    "max_questions": 8,  # Change this
    ...
}
```

### To Change Time Per Question
```python
INTERVIEW_CONFIG = {
    "time_per_question": 120,  # Seconds, change to 180 for 3 minutes
    ...
}
```

### To Make Scoring More/Less Strict
```python
DIFFICULTY_ADAPTATION = {
    "increase_threshold": 75,  # Lower to increase difficulty sooner
    "decrease_threshold": 50,  # Raise to decrease difficulty sooner
    ...
}
```

### To Adjust Performance Threshold
```python
INTERVIEW_CONFIG = {
    "performance_threshold": 40,  # Lower to terminate sooner, raise to be more lenient
    ...
}
```

---

## Performance Tips

### To Speed Up Interviews
- Use shorter resumes (focus on relevant experience)
- Use concise job descriptions
- Answer questions thoroughly but concisely

### To Improve Scores
- Take time to think before answering
- Structure your answers clearly
- Include specific examples
- Show deep knowledge of topic

### To Make the Most of Feedback
- Note your weak areas from report
- Do targeted practice on those areas
- Re-take interview to measure improvement
- Track your scores over multiple sessions

---

## Next Steps After Setup

### 1. Explore the Documentation
- Read [README.md](README.md) for comprehensive overview
- Check [ARCHITECTURE.md](ARCHITECTURE.md) for technical details
- Review [API_DOCUMENTATION.md](API_DOCUMENTATION.md) for API reference

### 2. Run Sample Interview
```bash
cd backend
python sample_usage.py
```

### 3. Customize for Your Needs
- Modify settings in `config/settings.py`
- Add custom prompts in `utils/prompts.py`
- Extend modules as needed

### 4. Integration
- Integrate with your ATS system
- Add database persistence
- Implement user authentication
- Deploy to cloud

---

## Getting Help

### Documentation Files
- `README.md` - Full project documentation
- `ARCHITECTURE.md` - System design and architecture
- `API_DOCUMENTATION.md` - API endpoint reference
- `QUICK_START.md` - This file!

### Common Issues
- Check `Troubleshooting` section above
- Review test cases in `tests/test_interview.py`
- Check API examples in `API_DOCUMENTATION.md`

### Debug Mode
Add debugging to `main.py`:
```python
if __name__ == "__main__":
    app.run(debug=True)  # Enables debug mode
```

---

## Example Interview Session

### Sample Input
```
Resume: John Doe, 5+ years Python/AWS
Job: Senior Backend Engineer at TechCorp
Time Per Question: 120 seconds
Total Questions: 8
```

### Expected Output
```
Q1 (Easy/Technical): "Explain Python decorators"
Score: 85/100 → Next: Medium

Q2 (Medium/Behavioral): "Tell us about a difficult project"
Score: 72/100 → Next: Medium

Q3 (Medium/Scenario): "System design question..."
Score: 78/100 → Next: Hard

Q4 (Hard/Technical): "Distributed system trade-offs"
Score: 65/100 → Next: Medium

... (continue for total questions or until early termination)

Final Report:
- Overall Score: 75/100
- Performance: Strong
- Recommendation: STRONG HIRE
```

---

## Production Deployment

### Before Deploying

1. **Security**
   - Add authentication
   - Enable HTTPS/TLS
   - Add rate limiting
   - Sanitize inputs

2. **Testing**
   - Run full test suite
   - Load testing
   - Security audit

3. **Monitoring**
   - Error tracking (Sentry)
   - Performance monitoring
   - API usage tracking

### Cloud Deployment Options

#### Option 1: AWS
```bash
# Deploy to Elastic Beanstalk
eb init -p python-3.8 hack2hire
eb create hack2hire-env
eb deploy
```

#### Option 2: Google Cloud
```bash
# Deploy to Cloud Run
gcloud run deploy hack2hire \
  --source . \
  --platform managed \
  --region us-central1
```

#### Option 3: Heroku
```bash
# Deploy to Heroku
git push heroku main
```

---

## Support & Feedback

For issues or suggestions:
1. Check existing documentation
2. Review sample code
3. Check test cases
4. Debug with print statements
5. Check OpenAI API status

---

**Good luck with your interview preparation! 🚀**
