Veiss – Security & RLS Model

Roles
Roles are stored in `profiles.role` (enum):
- coach
- player
- admin

`public.current_role()` is a helper that returns the current user's role based on `auth.uid()`

High-level Rules
- All API access requires an authenticated Supabase user.
- Players are read-only for training data.
- Coaches and admins can create and modify training data.
- Admins can also manage coaches.

Table Rules (simplified)
- profiles  
  - Users can read and update their own profile.  
  - Admins can read and update all profiles.
- teams, players  
  - All roles can read.  
  - Coaches/admins can create and update.  
- coaches  
  - All roles can read.  
  - Only admins can create/update/delete.
- sessions, reps, workouts, validated_reps, raw_stream_events  
  - All roles can read.  
  - Only coaches/admins can insert/update/delete.