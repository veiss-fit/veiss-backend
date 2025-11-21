Architecture for auth -> players -> roles:
    Users will sign up and log in with Supabase Auth stored in auth.users.
    We make a profiles table. Stores name and role.
    A role is coach, player.
    Profile connects to coaches table, if role == coach.
    Profile connects to players table, if role == player.
    Sessions and reps link to those

Entity Relationship Diagram:
teams
    id - uuid, primary key
    name - text
    sport - text
    created_at = timestamptz, default to now()

coaches
    id - uuid, primary key
    full name - text
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
    coach_id - uuid, foreign key to coaches.id, like who ran the session
    name - text
    started_at - timestamptz
    ended_at - timestamptz
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

teams: 1 -> many coaches
teams: 1 -> many players
teams: 1 -> many sessions
coaches: 1 -> many sessions
sessions: 1 -> many reps
players: 1 -> many reps
