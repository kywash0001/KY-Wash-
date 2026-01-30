# KY Wash - Quick Start Guide

## 🚀 Getting Started in 5 Minutes

### Step 1: Install Dependencies
```bash
cd ky-wash
npm install
```

### Step 2: Set Up Supabase (Optional but Recommended for Multi-User)

1. Go to https://supabase.com and create a free account
2. Create a new project
3. Go to Project Settings > API
4. Copy your Project URL and anon key

### Step 3: Configure Environment (Optional)

```bash
# Copy the example file
cp .env.local.example .env.local

# Edit .env.local and add your Supabase credentials
```

If you skip this step, the app will work with localStorage only (single browser/device).

### Step 4: Set Up Database (If using Supabase)

In Supabase SQL Editor, run:

```sql
CREATE TABLE global_state (
  id TEXT PRIMARY KEY,
  data JSONB NOT NULL,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

ALTER PUBLICATION supabase_realtime ADD TABLE global_state;

INSERT INTO global_state (id, data) 
VALUES ('main', '{"machines":[],"washerWaitlist":[],"dryerWaitlist":[],"usageHistory":[],"issues":[],"feedback":[],"chatMessages":[],"teamMembers":[],"lastUpdate":0}'::jsonb);
```

### Step 5: Run the App

```bash
npm run dev
```

Open http://localhost:3000

## 🎯 First Use

### Create Your Account
1. Click "Create Account"
2. Enter:
   - Student ID (e.g., "S001")
   - Phone Number (e.g., "0123456789")
   - Password (at least 6 characters)
3. Click "Create Account"

### Try the Features
- **Machines Tab**: Start a washer or dryer (choose Normal or Extra mode)
- **Report No One**: If a machine is running but nobody is there, click "Report No One" (need 2 reports to reset)
- **Report Machine Done**: If someone forgot to collect clothes, click "Report Machine is Done" (immediate reset)
- **Chat**: Click the chat icon in bottom-right to send messages
- **Waitlist**: Join a waitlist if all machines are busy

### Admin Access
- Click "Admin Login" on main page
- Password: `JJ1234#`
- Lock/unlock machines, view issues, export data

## 🔧 Key Features

### For Users
✅ Real-time machine status updates
✅ Audio notifications when cycle completes
✅ **Report No One** - Report machines with no one using them (2 reports to reset)
✅ **Report Machine is Done** - Report when owner forgot to collect (instant reset)
✅ Usage history and spending tracking
✅ Statistics and pattern analysis
✅ Community chat
✅ Report machine issues
✅ Dark mode

### For Admins
✅ Machine lock control
✅ View reported issues
✅ Real-time dashboard
✅ Usage history with filters
✅ CSV export

## 💡 Tips

1. **Testing Multi-User**: Open multiple browser windows/tabs to see real-time sync
2. **Admin Dashboard**: Check machine status and waitlists in real-time
3. **Dark Mode**: Toggle with the sun/moon icon in top-right
4. **Timers**: Normal mode = 1 min, Extra mode = 40 min (configurable in code)

## ⚠️ Important Notes

- **Admin Password**: JJ1234# (change in production)
- **Machine Timing**: Currently set to 1 min (normal) and 40 min (extra) for testing
- **Storage**: Works with localStorage by default; add Supabase for multi-device sync
- **Real-time**: With Supabase, all users see updates within 2 seconds

## 🐛 Common Issues

**Problem**: State not syncing between browsers
**Solution**: Set up Supabase and configure .env.local

**Problem**: Timers not working
**Solution**: Check browser console for errors, refresh page

**Problem**: Can't create account
**Solution**: Check all fields are filled, password is 6+ characters

## 📱 Mobile Testing

The app is responsive. Test on mobile by:
1. Run `npm run dev`
2. Get your local IP: `ipconfig` (Windows) or `ifconfig` (Mac/Linux)
3. Access from mobile: `http://YOUR_IP:3000`

## 🎨 Customization

### Change Machine Timings
Edit `/lib/globalState.ts`:
```typescript
const duration = mode === 'normal' ? 60 : 2400; // Change these values
```

### Change Pricing
Edit `/lib/globalState.ts`:
```typescript
const cost = mode === 'normal' ? 5 : 6; // Change these values
```

### Add More Machines
Edit `/lib/globalState.ts` in `initializeMachines()`:
```typescript
for (let i = 1; i <= 10; i++) { // Change 6 to desired number
```

## 📚 Need Help?

Check the full README.md for:
- Complete feature list
- Detailed setup instructions
- Database schema
- Architecture explanation
- Future improvement suggestions

---

**Ready to wash?** Start the server and create your account! 🧺
