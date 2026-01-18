# Goal: Persist and Present Workout History with MongoDB

## Success Criteria
- All workout sessions (CV trainer and Streamlit app) are stored in MongoDB with user, timing, and performance metrics.
- Streamlit UI shows historical workouts, aggregated stats, and charts for the signed-in user.
- Legacy JSON history is migrated (or archivable) without data loss.
- Repository methods and critical flows are covered by automated tests.
- Deployment instructions exist for local MongoDB and hosted (Atlas) setups.

## Workstreams & Deliverables

### 1. Environment & Dependencies
**Goal:** Enable the app to communicate with MongoDB securely and reliably.
- Add `pymongo` (or `motor`) to requirements and document installation.
- Load Mongo connection details from environment variables (`MONGO_URI`, `MONGO_DB`).
- Create `Core/db.py` to manage the client singleton and handle connection errors.
- Provide sample `.env.example` with Mongo credentials placeholders.

### 2. Data Modeling & Schema
**Goal:** Define scalable MongoDB collections for workouts and users.
- Model `users` and `workouts` collections (with optional embedded metrics).
- Specify required fields: `user_id`, `exercise`, timestamps, duration, reps, calories, notes, source.
- Add indexes for `{ user_id: 1, start_time: -1 }` and `{ exercise: 1 }`.
- Document validation rules and conventions (ISO timestamps, metric units).

### 3. Data Ingestion Updates
**Goal:** Ensure every workout gets persisted, regardless of entry point.
- Replace JSON writes in `WorkoutSession.end_session` with repository inserts (with optional JSON export toggle).
- Add a repository layer (`Core/workout_repository.py`) abstracting CRUD + aggregations.
- In Streamlit, capture workout completion (button or automatic) and persist via the same repository.
- Introduce basic user selection/auth to tag sessions correctly.

### 4. History Presentation in Streamlit
**Goal:** Surface historical data intuitively for users.
- On app startup, load user workouts via repository calls.
- Build history tab/section with:
  - Recent sessions table (sortable/filterable by date & exercise).
  - Key metrics cards (total reps, calories, time this week/month).
  - Progress visuals (Altair/Plotly line chart for volume, pie chart for exercise mix).
- Implement filters (date range, exercise, source) driving repository queries.

### 5. Analytics & Insights
**Goal:** Deliver actionable feedback using MongoDB aggregations.
- Implement aggregation pipelines for streaks, PR detection, and per-exercise trends.
- Expose helper functions returning pre-aggregated stats for UI widgets.
- Plan for exporting reports (CSV/JSON) using the same data source.

### 6. Testing, Operations & Migration
**Goal:** Safeguard data integrity and streamline deployment.
- Add unit tests (using `mongomock` or Dockerized Mongo) for repository methods and UI save flow.
- Create migration script to import `workout_data/sessions.json` into Mongo (idempotent).
- Document backup strategy and recommended Atlas configuration.
- Update README with setup, environment variables, and troubleshooting.

## Timeline & Sequencing (Suggested)
1. Environment & Dependencies
2. Data Modeling & Repository scaffolding
3. CV trainer integration
4. Streamlit save flow and history UI
5. Analytics enhancements
6. Migration + testing + documentation polish

## Risks & Mitigations
- **Mongo unavailable at runtime:** implement graceful fallbacks and clear errors, keep optional JSON export.
- **User identity ambiguity:** define minimal auth or user selection workflow before persisting.
- **Aggregation performance on large datasets:** index proactively and paginate UI queries.
- **Deployment complexity:** provide scripts/docker compose for local testing and Atlas notes for production.

## Open Questions
1. Should user authentication be implemented now or simulated with manual selection?
2. Do we need to retain legacy JSON files for offline use after migration?
3. What analytics are highest priority (streaks, calories, PRs)?
4. Will other clients (mobile, API) consume the same MongoDB data soon?
