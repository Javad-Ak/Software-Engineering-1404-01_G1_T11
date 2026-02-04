# Team 11 - Listening & Writing Microservice

## Overview

This microservice provides AI-powered listening (speaking) and writing assessment for TOEFL-style English exams. Users submit writing texts or audio recordings and receive detailed AI-generated scores and feedback.

[View Figma Design](https://www.figma.com/design/3uaZuwIjT0OU8v7w2cy7Df/SE1_T11?node-id=0-1&m=dev&t=wTZc2qAKki3quDGV-1)

## Features

✅ **AI Assessment** (Powered by Deepseek + Whisper)
- Writing: Grammar, Vocabulary, Coherence, Fluency (0-100 each)
- Speaking: Pronunciation, Fluency, Vocabulary, Grammar, Coherence (0-100 each)
- Audio transcription with Whisper API
- Personalized feedback and improvement suggestions
- Cost: ~$0.10/month for 300 submissions

✅ **Database Models**
- `Submission`: Base model with status tracking (pending/in_progress/completed/failed)
- `WritingSubmission`: Text details and word count
- `ListeningSubmission`: Audio details and transcription
- `AssessmentResult`: Detailed sub-scores and feedback

✅ **API Endpoints**
- `POST /team11/api/submit-writing/` - Submit and assess writing
- `POST /team11/api/submit-listening/` - Submit and assess speaking
- `GET /team11/dashboard/` - View submission history
- `GET /team11/submission/<uuid>/` - View detailed results

✅ **Frontend Pages**
- Landing page, exam selection, writing/listening interfaces
- Dashboard with submission history and statistics
- Detailed results page with AI feedback

## AI Integration

**API Configuration:**
- Provider: GapGPT (`https://api.gapgpt.app/v1`)
- Models: `deepseek-chat` (text), `gapgpt/whisper-1` (audio)
- Key configured in `services/ai_service.py`

**Service Layer:**
```python
from team11.services import assess_writing, assess_speaking

# Writing assessment
result = assess_writing(topic, text_body, word_count)
# Returns: scores, feedback_summary, suggestions

# Speaking assessment (transcription + analysis)
result = assess_speaking(topic, audio_file_path, duration_seconds)
# Returns: scores, transcription, feedback_summary, suggestions
```

**Test AI Service:**
```powershell
python team11/test_ai_service.py
```

## Project Structure

```
team11/
├── services/              # AI integration layer
│   ├── ai_service.py      # Core AI functions
│   └── prompts.py         # Assessment prompts
├── models.py              # Database models
├── views.py               # Views and API endpoints (AI-integrated)
├── migrations/            # Database migrations
├── templates/team11/      # HTML templates
├── static/team11/         # CSS and assets
├── figma/                 # SVG mockups for Figma
└── test_ai_service.py     # AI testing script
```

## How to Run

### Local Development

1. **Activate virtual environment and install dependencies:**
   ```powershell
   .venv\Scripts\Activate.ps1
   pip install -r requirements.txt
   ```

2. **Run migrations:**
   ```powershell
   python manage.py migrate
   ```

3. **Run development server:**
   ```powershell
   python manage.py runserver
   ```

4. **Access:**
   - Main site: http://localhost:8000/
   - Team 11: http://localhost:8000/team11/

### Docker (Production)

```powershell
docker network create app404_net
docker-compose up --build
docker-compose exec core python manage.py migrate
```

## Testing the AI Assessment

### 1. User Registration
1. Navigate to http://localhost:8000/
2. Click "ثبت نام" (Sign Up) and create an account

### 2. Test Writing (AI-Powered)
1. Go to http://localhost:8000/team11/
2. Click "شروع آزمون" → Select writing topic
3. Write your essay (150+ words recommended)
4. Submit and receive:
   - Overall score (0-100)
   - Sub-scores: Grammar, Vocabulary, Coherence, Fluency
   - Detailed feedback and 3 personalized suggestions

### 3. Test Speaking (AI-Powered)
1. Choose listening topic from exam selection
2. Record audio (30-60 seconds)
3. Submit and receive:
   - Audio transcription (Whisper API)
   - Overall score (0-100)
   - Sub-scores: Pronunciation, Fluency, Vocabulary, Grammar, Coherence
   - Detailed feedback and suggestions

### 4. View Dashboard
- Click "داشبورد من" to see all submissions
- View detailed results for each assessment

## Assessment Criteria

**Writing** (4 criteria × 0-100):
- **Grammar**: Sentence structure, tenses, agreement
- **Vocabulary**: Word choice, range, precision
- **Coherence**: Logical organization, idea progression
- **Fluency**: Natural flow, sentence variety

**Speaking** (5 criteria × 0-100):
- **Pronunciation**: Clarity, accent, intonation
- **Fluency**: Pace, pauses, natural delivery
- **Vocabulary**: Word choice and range
- **Grammar**: Sentence structure, verb usage
- **Coherence**: Logical organization

**Score Ranges:**
- 90-100: Excellent (near-native)
- 75-89: Good (strong command)
- 60-74: Satisfactory (adequate)
- 40-59: Limited (frequent errors)
- 0-39: Poor (severe errors)

## Architecture

- **Microservice Pattern**: Independent operation with own database
- **Authentication**: Shared JWT cookies via core app
- **AI Service Layer**: `services/ai_service.py` handles API calls
- **Error Handling**: Failed assessments marked with status='failed'
- **Logging**: All AI calls logged for debugging

## Contributors

Team 11 - Software Engineering Course 1404-01  
Amirkabir University of Technology
