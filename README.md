# KY Wash - Laundry Management System

A comprehensive Next.js-based laundry management system with real-time updates, machine monitoring, and usage tracking.

## Features

### User Features
- **Account Management**: Create account and login with Student ID, Phone Number, and Password
- **Machine Management**: 
  - 6 Washers and 6 Dryers with real-time status
  - Two wash/dry modes: Normal (1 min - RM5) and Extra (40 min - RM6)
  - Live countdown timers
  - Global visibility of all machine statuses
- **Reporting System**:
  - **Report No One**: If a machine is running but nobody is using it, users can report it. After 2 reports, the machine automatically resets and becomes available
  - **Report Machine is Done**: If the owner forgot to collect clothes and mark as "Clothes Collected", other users can report it. After 1 report, the machine resets and becomes available
  - Reports are tied to specific users to prevent spam
- **Waitlist System**: Join washer/dryer waitlists to see demand
- **Notifications**: Audio alerts when machine cycle completes
- **Usage History**: Track spending and usage patterns
- **Statistics**: 
  - Personal washer/dryer usage statistics
  - System-wide washing trends and peak hour suggestions
  - Weekly usage charts
- **Profile Management**: Edit student ID, phone number, and password
- **Feedback System**: Submit ratings and feedback
- **Community Chat**: Real-time chat with other users
- **User Guide**: Complete instructions for using the system
- **Report Issues**: Report machine problems directly

### Admin Features
- **Machine Lock Control**: Lock/unlock machines for maintenance
- **Issue Management**: View and manage reported issues
- **Live Dashboard**: Real-time view of all machines and waitlists
- **Usage History**: 
  - Filterable by month, year, and week
  - Export to CSV
  - Manual record deletion
- **Analytics**: Complete overview of system usage

### Technical Features
- **Global State Management**: Real-time synchronization across all users
- **Persistent Storage**: Data stored in localStorage and Supabase
- **Dark Mode**: Toggle between light and dark themes
- **Responsive Design**: Mobile-friendly interface
- **Real-time Updates**: All changes reflect immediately for all users

## Setup Instructions

### Prerequisites
- Node.js 18+ installed
- Supabase account (free tier works)

### Installation

1. **Clone and Install Dependencies**
```bash
cd ky-wash
npm install
```

2. **Supabase Setup**

Create a free Supabase account at https://supabase.com

Create a new project and get your credentials:
- Go to Project Settings > API
- Copy the Project URL and anon/public key

3. **Environment Configuration**

Create a `.env.local` file:
```bash
cp .env.local.example .env.local
```

Edit `.env.local` and add your Supabase credentials:
```
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key-here
```

4. **Database Schema Setup**

In your Supabase dashboard, go to SQL Editor and run:

```sql
-- Create global_state table for real-time sync
CREATE TABLE global_state (
  id TEXT PRIMARY KEY,
  data JSONB NOT NULL,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Enable real-time for global_state
ALTER PUBLICATION supabase_realtime ADD TABLE global_state;

-- Insert initial state
INSERT INTO global_state (id, data) 
VALUES ('main', '{
  "machines": [],
  "washerWaitlist": [],
  "dryerWaitlist": [],
  "usageHistory": [],
  "issues": [],
  "feedback": [],
  "chatMessages": [],
  "teamMembers": [],
  "lastUpdate": 0
}'::jsonb);

-- Optional: Create indexes for better performance
CREATE INDEX idx_global_state_updated_at ON global_state(updated_at);
```

5. **Run the Development Server**
```bash
npm run dev
```

Open http://localhost:3000 in your browser.

## Usage

### First Time Setup
1. Go to http://localhost:3000
2. Click "Create Account"
3. Enter your Student ID, Phone Number, and Password
4. Click "Create Account" to register

### User Access
- **Login**: Use your Student ID, Phone Number, and Password
- **Dashboard**: Access all features through the navigation tabs

### Admin Access
- **Password**: JJ1234#
- Click "Admin Login" on the main page
- Enter the admin password

## How It Works

### Real-Time Synchronization
- All changes are saved to localStorage immediately
- Changes sync to Supabase every 2 seconds
- All clients poll Supabase every 2 seconds for updates
- Real-time subscriptions ensure instant updates

### Machine States
- **Available**: Ready to use
- **In Use**: Currently running a cycle
- **Maintenance**: Locked by admin

### Timer Logic
- Timers count down every second
- Normal mode: 1 minute (60 seconds)
- Extra mode: 40 minutes (2400 seconds)
- Timers sync across all users in real-time

### Reporting System
- **Report No One Button**:
  - Appears for all users except the machine owner
  - Only visible when machine is running (timer counting down)
  - Requires 2 unique user reports to trigger action
  - Action: Machine resets to available, usage marked as cancelled, no charge applied
  - Purpose: Prevent machines from being "blocked" when nobody is actually using them

- **Report Machine is Done Button**:
  - Appears for all users except the machine owner
  - Only visible when cycle is completed or user selected "On the Way"
  - Requires 1 report to trigger action (immediate)
  - Action: Machine resets to available, usage marked as completed, charge applied
  - Purpose: Free up machines when owner forgot to mark "Clothes Collected"

- Users cannot report the same machine multiple times
- Report counts are displayed to all users
- Reports reset when machine becomes available

### Notification System
- Audio beep plays when cycle completes
- Repeats every 2 seconds until acknowledged
- User can select "On the Way" or "Clothes Collected"
- Status visible to all users

## File Structure

```
ky-wash/
├── app/
│   ├── page.tsx              # Login/Create Account
│   ├── dashboard/
│   │   └── page.tsx          # Main user dashboard
│   ├── admin/
│   │   └── page.tsx          # Admin dashboard
│   ├── layout.tsx            # Root layout
│   └── globals.css           # Global styles
├── components/               # Reusable components
├── lib/
│   ├── globalState.ts        # State management
│   └── supabase.ts          # Supabase client
├── types/
│   └── index.ts             # TypeScript types
└── public/                  # Static assets
```

## Default Credentials

### Test User (Create your own)
- Student ID: (Your choice)
- Phone Number: (Your choice)
- Password: (Your choice)

### Admin
- Password: `JJ1234#`

## Features in Detail

### Machines Tab
- View all 6 washers and 6 dryers
- Join/leave waitlists
- Start machines with mode selection
- Real-time timer countdown
- Report machine issues
- Acknowledge completed cycles

### History Tab
- Total spending breakdown
- Detailed usage history table
- Status tracking (Completed/In Progress/Cancelled)

### Stats Tabs (Washer/Dryer)
- Personal usage statistics
- Pattern analysis
- System-wide trends
- Weekly usage charts

### Profile Tab
- Edit student ID
- Update phone number
- Change password (with confirmation)

### Feedback Tab
- 5-star rating system
- Unlimited text feedback
- View team members

### User Guide Tab
- Complete usage instructions
- Waitlist explanation
- Best practices

### Admin Dashboard
- Machine lock/unlock control
- View and delete reported issues
- Real-time machine status
- Current waitlists
- Filterable usage history
- CSV export functionality

## Technology Stack

- **Frontend**: Next.js 14, React, TypeScript
- **Styling**: Tailwind CSS
- **Icons**: Lucide React
- **Database**: Supabase (PostgreSQL)
- **Real-time**: Supabase Realtime
- **State Management**: Custom global state with localStorage

## Performance Considerations

- Local storage provides instant updates
- Supabase ensures data persistence
- Real-time subscriptions for live updates
- Efficient polling strategy (2-second intervals)
- Optimistic UI updates

## Security Notes

- Admin password should be changed in production
- Passwords stored in localStorage (for demo purposes)
- In production, use proper authentication (Supabase Auth)
- Implement proper API security rules in Supabase

## Future Improvements (Suggestions)

1. **Enhanced Security**
   - Implement Supabase Auth for proper user authentication
   - Hash passwords instead of storing plain text
   - Add email verification
   - Implement role-based access control

2. **Advanced Features**
   - Push notifications for mobile devices
   - Email/SMS alerts when machine cycle completes
   - Reservation system with time slots
   - Payment integration for automatic billing
   - Machine maintenance scheduling
   - QR code scanning for quick machine access

3. **Analytics & Reporting**
   - Advanced usage analytics dashboard
   - Cost analysis and budgeting tools
   - Peak hour heat maps
   - Predictive maintenance alerts
   - User behavior insights

4. **User Experience**
   - Native mobile apps (iOS/Android)
   - Progressive Web App (PWA) support
   - Multi-language support
   - Accessibility improvements
   - Tutorial walkthrough for new users

5. **Machine Management**
   - Different machine types (delicate, heavy-duty)
   - Variable pricing based on time of day
   - Energy usage tracking
   - Automated maintenance reminders
   - Integration with IoT-enabled machines

6. **Social Features**
   - User ratings and reviews
   - Community announcements
   - Laundry tips and tricks sharing
   - Group laundry scheduling

7. **Administrative Tools**
   - Advanced reporting and exports
   - User management interface
   - Automated billing and invoicing
   - Inventory management for detergents
   - Performance metrics and KPIs

8. **Technical Improvements**
   - WebSocket connections for real-time updates
   - Service worker for offline functionality
   - Improved caching strategies
   - Performance monitoring
   - Automated testing suite

9. **Integration Capabilities**
   - Calendar integration (Google, Outlook)
   - Payment gateways (Stripe, PayPal)
   - Student ID card scanning
   - Campus notification systems
   - Building management systems

10. **Environmental Features**
    - Energy consumption tracking
    - Carbon footprint calculator
    - Eco-friendly wash cycle suggestions
    - Water usage monitoring

## Troubleshooting

### Supabase Connection Issues
- Verify your `.env.local` file has correct credentials
- Check Supabase project is active
- Ensure real-time is enabled for global_state table

### State Not Syncing
- Check browser console for errors
- Verify localStorage is enabled
- Clear browser cache and reload

### Timer Not Working
- Ensure JavaScript is enabled
- Check for console errors
- Try refreshing the page

## Support

For issues or questions:
1. Check the User Guide tab in the application
2. Review the troubleshooting section above
3. Contact system administrator

## License

This project is created for educational purposes.

---

**Note**: This is a demo application. For production use, implement proper security measures, authentication, and error handling.
