# Copilot Instructions for Mergington High School Activities API

## Project Overview

This is a **FastAPI-based student activity management system** for Mergington High School. The application is intentionally minimal—a learning project showcasing GitHub Copilot capabilities. It features:

- Backend API (`src/app.py`): FastAPI with in-memory activity database
- Frontend (`src/static/`): HTML/CSS/JavaScript for activity browsing and signup
- Both components run on a single server; no external database

## Architecture & Data Flow

**Key Design Pattern**: The API uses **activity names as string identifiers** (not UUIDs). This simplifies the learning project but has implications for naming stability.

```
Client (index.html/app.js) 
  → POST /activities/{activity_name}/signup?email=...
  → GET /activities
  ↓
FastAPI app (app.py)
  ↓
In-memory dict: activities = { "Activity Name": {...activity_details...} }
```

**Critical Implementation Detail**: The activities data structure is a dictionary where:
- Keys are activity names (strings like "Chess Club")
- Values contain: `description`, `schedule`, `max_participants`, `participants` (list of emails)

When adding features, remember this is **in-memory storage—data resets on server restart**.

## Key Files & Their Responsibilities

| File | Purpose | Dependencies |
|------|---------|--------------|
| `src/app.py` | FastAPI server, activity CRUD, validation | fastapi, uvicorn |
| `src/static/index.html` | HTML structure for activity list & signup form | - |
| `src/static/app.js` | Fetch activities, handle form submission, UI updates | - |
| `src/static/styles.css` | Styling | - |
| `requirements.txt` | Python dependencies (fastapi, uvicorn) | - |
| `pytest.ini` | Test configuration; pythonpath = . | - |

## Development Workflow

**Run the server:**
```bash
cd src && python app.py
```
Server starts at `http://localhost:8000`

**API Documentation:**
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

**Key Endpoints:**
- `GET /activities` → Returns all activities with participant counts
- `POST /activities/{activity_name}/signup?email=...` → Add student email to activity

## Code Patterns & Conventions

1. **Activity Identifier Pattern**: Always use the activity name string from the `activities` dict as the identifier. URL encode it when needed in frontend:
   ```javascript
   fetch(`/activities/${encodeURIComponent(activity)}/signup?email=...`, {method: "POST"})
   ```

2. **Error Handling**: Backend raises `HTTPException(status_code=404)` for missing activities. Frontend catches response errors and displays messages in the `#message` div.

3. **Frontend State Management**: No state library—activities are fetched on page load via `fetchActivities()` in app.js. The activity dropdown and activity list are repopulated from API responses.

4. **Validation**: The `signup_for_activity()` function validates that the activity exists but **does not validate**:
   - Duplicate email signup (same student can sign up multiple times)
   - Email format
   - Participant capacity limits
   
   Consider these gaps when extending.

## Adding Features

When implementing new functionality:

1. **Backend-first**: Add FastAPI endpoint in `app.py` with proper activity lookup validation
2. **Frontend follow-up**: Update `app.js` fetch calls and `index.html` as needed
3. **Test against the in-memory database**: Manual testing via browser or Swagger UI is current practice; no unit tests are present

## External Dependencies

- `fastapi` - Web framework
- `uvicorn` - ASGI server to run FastAPI

Both are minimal and well-maintained. No database, authentication, or complex dependencies.

## Notes for Agents

- This is a **learning/exercise project**, not production code—prioritize clarity over optimization
- Maintain the intentionally simple in-memory architecture unless explicitly asked otherwise
- When suggesting improvements, consider the pedagogical goal: teaching Copilot usage patterns
- Test signup flows manually; verify both the UI updates and API responses correctly
