Architecture for auth -> profiles -> roles:
    Users will sign up and log in with Supabase Auth stored in auth.users.
    We make a profiles table. Stores name and role.
    A role is coach, player, or admin (admin is an optional super-user coach).
    Profile connects to coaches table, if role == coach.
    Profile connects to players table, if role == player.
    Sessions, workouts, reps and metrics link to those.

Entity Relationship Diagram:

profiles
    id - uuid, primary key, foreign key to auth.users.id
    full_name - text
    role - enum('coach', 'player', 'admin')
    created_at - timestamptz, default to now()

teams
    id - uuid, primary key
    name - text
    sport - text
    created_at - timestamptz, default to now()

coaches
    id - uuid, primary key
    full_name - text
    email - text
    team_id - uuid, foreign key to teams.id
    created_at - timestamptz, default to now()

players
    id - uuid, primary key
    full_name - text
    team_id - uuid, foreign key to teams.id
    jersey_number - int, optional
    created_at - timestamptz, default to now()

sessions
    id - uuid, primary key
    team_id - uuid, foreign key to teams.id
    coach_id - uuid, foreign key to coaches.id (who ran the session)
    name - text
    started_at - timestamptz
    ended_at - timestamptz, optional
    status - text (for example: 'active', 'completed')
    metrics - jsonb, aggregate metrics for the entire session (e.g. total reps, avg speed)
    created_at - timestamptz, default to now()

reps
    id - uuid, primary key
    session_id - uuid, foreign key to sessions.id
    player_id - uuid, foreign key to players.id
    exercise_name - text
    machine_name - text
    set_number - int
    rep_number - int
    weight - numeric
    average_rep_speed - numeric
    created_at - timestamptz, default to now()

workouts
    id - uuid, primary key
    session_id - uuid, foreign key to sessions.id
    player_id - uuid, foreign key to players.id
    exercise_name - text
    machine_name - text
    metrics - jsonb, summary metrics for this workout (e.g. total_reps, avg_speed, best_rep_speed)
    created_at - timestamptz, default to now()

validated_reps
    id - uuid, primary key
    workout_id - uuid, foreign key to workouts.id
    rep_index - int (1, 2, 3… within that workout)
    metrics - jsonb, per-rep metrics (e.g. peak_speed, range_of_motion)
    created_at - timestamptz, default to now()

raw_stream_events
    id - bigserial, primary key
    workout_id - uuid, foreign key to workouts.id
    event_time - timestamptz (when this sample was recorded)
    payload - jsonb, raw device data (e.g. position, velocity, etc.)
    created_at - timestamptz, default to now()

Relationships:
    auth.users: 1 -> 1 profiles

    teams: 1 -> many coaches
    teams: 1 -> many players
    teams: 1 -> many sessions

    coaches: 1 -> many sessions

    sessions: 1 -> many reps
    sessions: 1 -> many workouts

    players: 1 -> many reps
    players: 1 -> many workouts

    workouts: 1 -> many validated_reps
    workouts: 1 -> many raw_stream_events
