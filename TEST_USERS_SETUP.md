# Test Users Setup Guide

## Quick Setup Steps

### Step 1: Create Users in Supabase Dashboard

1. Go to your Supabase Dashboard
2. Navigate to **Authentication** > **Users**
3. Click **Add User** (or **Invite User**)
4. Create two test users:

   **Admin User:**
   - Email: `admin@test.com`
   - Password: `admin123` (or any password you prefer)
   - Auto Confirm: ✅ (check this box)

   **Intern User:**
   - Email: `intern@test.com`
   - Password: `intern123` (or any password you prefer)
   - Auto Confirm: ✅ (check this box)

### Step 2: Add Email Column to Profiles (if not already added)

First, make sure your profiles table has an email column. Run this in SQL Editor:

```sql
ALTER TABLE public.profiles 
ADD COLUMN IF NOT EXISTS email text;
```

### Step 3: Create Profiles

After creating the users, go to **SQL Editor** and run:

```sql
-- Create admin profile
INSERT INTO public.profiles (id, full_name, role, email)
VALUES (
  (SELECT id FROM auth.users WHERE email = 'admin@test.com' LIMIT 1),
  'Admin User',
  'admin',
  'admin@test.com'
)
ON CONFLICT (id) DO UPDATE
SET role = 'admin', full_name = 'Admin User', email = 'admin@test.com';

-- Create intern profile
INSERT INTO public.profiles (id, full_name, role, email)
VALUES (
  (SELECT id FROM auth.users WHERE email = 'intern@test.com' LIMIT 1),
  'Intern User',
  'intern',
  'intern@test.com'
)
ON CONFLICT (id) DO UPDATE
SET role = 'intern', full_name = 'Intern User', email = 'intern@test.com';
```

### Step 4: Test Login

You can now log in with:
- **Admin**: `admin@test.com` / `admin123`
- **Intern**: `intern@test.com` / `intern123`

## Alternative: Direct User ID Method

If you know the user IDs directly (from the Users table), you can use:

```sql
INSERT INTO public.profiles (id, full_name, role, email)
VALUES 
  ('YOUR_ADMIN_USER_ID', 'Admin User', 'admin', 'admin@test.com'),
  ('YOUR_INTERN_USER_ID', 'Intern User', 'intern', 'intern@test.com')
ON CONFLICT (id) DO UPDATE
SET role = EXCLUDED.role, full_name = EXCLUDED.full_name, email = EXCLUDED.email;
```

Replace `YOUR_ADMIN_USER_ID` and `YOUR_INTERN_USER_ID` with the actual UUIDs from your auth.users table.

