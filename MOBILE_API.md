# Documenting mobile api contract

This document describes the minimal API endpoints the mobile app will use to:

- start sessions
- start workouts
- send raw stream sensor events
- finish workouts
- read validated rep data


## 1. Start a session

POST /api/mobile/sessions/start

Request:
{
  "team_id": "uuid",
  "coach_id": "uuid",
  "name": "optional session name"
}

Response:
{
  "session_id": "uuid",
  "status": "active"
}


## 2. End a session

POST /api/mobile/sessions/{session_id}/end

Request:
{
  "ended_at": "timestamptz"
}

Response:
{
  "session_id": "uuid",
  "status": "completed"
}


## 3. Start a workout (player + exercise)

POST /api/mobile/workouts/start

Request:
{
  "session_id": "uuid",
  "player_id": "uuid",
  "exercise_name": "Back Squat",
  "machine_name": "Hammer Strength Squat"
}

Response:
{
  "workout_id": "uuid"
}


## 4. Upload raw stream events

POST /api/mobile/workouts/{workout_id}/raw_events

Request:
{
  "events": [
    {
      "event_time": "timestamptz",
      "payload": { "position": 12.3, "velocity": 0.45 }
    }
  ]
}

Response:
{
  "received": 1
}


## 5. Complete a workout

POST /api/mobile/workouts/{workout_id}/complete

Request:
{
  "completed_at": "timestamptz"
}

Response:
{
  "workout_id": "uuid",
  "metrics": {
    "total_reps": 8,
    "avg_speed": 0.45
  }
}


## 6. Get workout summary

GET /api/mobile/workouts/{workout_id}/summary

Response:
{
  "workout_id": "uuid",
  "player_id": "uuid",
  "metrics": {
    "total_reps": 8,
    "avg_speed": 0.45
  },
  "validated_reps": [
    {
      "rep_index": 1,
      "metrics": { "peak_speed": 0.50 }
    }
  ]
}
