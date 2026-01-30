# KY Wash - Feature Updates

## 🆕 New Features Added

### 1. Report No One Button

**Purpose**: Prevent machines from being blocked when nobody is actually using them.

**How it works**:
- Appears on ALL users' pages except the machine owner
- Only visible when a machine is running (timer counting down, notification state is "none")
- Requires 2 unique user reports to trigger action
- Shows report count: "1/2 reports (need 2 to reset)"

**What happens after 2 reports**:
- Machine automatically resets to "available" status
- Usage history is marked as "cancelled"
- No charges applied to the original user
- Machine becomes available for others to use immediately
- All report counts reset to zero

**User Experience**:
- Users can only report once per machine session
- Button shows "Reported No One" after user reports (disabled state)
- Real-time updates show report count to all users
- Confirmation dialog asks user to confirm before reporting

**Example Scenario**:
1. Student A starts a washer but doesn't actually use it
2. Student B sees the machine running and clicks "Report No One"
3. Student C also clicks "Report No One"
4. Machine automatically resets and becomes available
5. Student D can now start the machine

---

### 2. Report Machine is Done Button

**Purpose**: Free up machines when the owner forgot to mark "Clothes Collected" after collecting their laundry.

**How it works**:
- Appears on ALL users' pages except the machine owner
- Only visible when:
  - Cycle is completed (notification state is "completed"), OR
  - User selected "On the Way" (notification state is "on-the-way")
- Requires only 1 report to trigger immediate action
- Shows report count: "Reported by X user(s)"

**What happens after 1 report**:
- Machine automatically resets to "available" status
- Usage history is marked as "completed" (cycle was finished)
- Charges ARE applied to the original user (they used the machine)
- Machine becomes available for others to use immediately
- All report counts reset to zero

**User Experience**:
- Users can only report once per machine session
- Button shows "Reported Machine Done" after user reports (disabled state)
- Real-time updates show report count to all users
- Confirmation dialog asks user to confirm before reporting
- Takes immediate action (no need for multiple reports)

**Example Scenario**:
1. Student A's wash cycle completes, notification sounds
2. Student A selects "On the Way" to indicate they're coming
3. 15 minutes pass, Student A forgot to collect and mark "Clothes Collected"
4. Student B clicks "Report Machine is Done"
5. Machine automatically resets and becomes available
6. Student C can now start the machine

---

## 🔄 Global Visibility Features

All report actions are synchronized across ALL users in real-time:

1. **Report Counts**: When someone reports, all users see the updated count immediately
2. **Machine Status**: When a machine resets due to reports, all users see it become available
3. **Button States**: If you've reported, your button shows as disabled across all your devices
4. **Real-time Sync**: Updates propagate within 2 seconds to all connected users

---

## 🔔 Notification System (Maintained)

The existing notification system continues to work perfectly:

1. **Audio Alert**: Continuous beeping every 2 seconds when cycle completes
2. **Owner Actions**: 
   - "On the Way" - Indicates user is coming to collect
   - "Clothes Collected" - Resets machine to available
3. **Visual Indicators**: 
   - Animated pulse effect on action buttons
   - "Pending Collection" message shown to other users
4. **Cross-user Visibility**: All users see the current notification state

---

## 💡 How Features Work Together

### Scenario 1: Honest Mistake
- User forgets to collect clothes
- Other users can report "Machine is Done"
- Machine becomes available quickly
- Original user is charged (they used it)

### Scenario 2: Abandoned Machine
- User starts machine but never shows up
- Other users report "No One"
- After 2 reports, machine resets
- Original user is NOT charged (didn't actually use it)

### Scenario 3: Normal Usage
- User starts machine and uses it properly
- User marks "Clothes Collected" when done
- No reports needed
- System works smoothly

---

## 🛡️ Anti-Abuse Measures

1. **One Report Per User**: Each user can only report a specific machine once
2. **Owner Cannot Report**: Machine owner cannot report their own machine
3. **Context-Sensitive**: Buttons only appear in appropriate situations
4. **Report Reset**: When machine becomes available, all reports clear
5. **Confirmation Dialogs**: Users must confirm before reporting

---

## 📊 Technical Implementation

### Data Structure
```typescript
interface Machine {
  // ... existing fields
  noOneReports: string[];        // Student IDs who reported "no one"
  machinesDoneReports: string[]; // Student IDs who reported "done"
}
```

### State Management
- Reports stored in global state
- Synchronized to Supabase in real-time
- Persisted in localStorage
- Automatically reset when machine becomes available

### Report Logic
```
Report No One:
  IF timer > 0 AND notificationState === 'none' AND !isOwner
    SHOW button
    IF reports >= 2
      Reset machine, mark as cancelled, no charge

Report Machine Done:
  IF (notificationState === 'completed' OR 'on-the-way') AND !isOwner
    SHOW button
    IF reports >= 1
      Reset machine, mark as completed, apply charge
```

---

## 🎯 User Interface Changes

### Machine Card Layout
```
┌─────────────────────────────┐
│  W1              [Available] │
│                              │
│  Student ID: S001            │
│  Phone: 0123456789           │
│  Mode: Normal (1 min)        │
│                              │
│  ⏱️  0:45                     │
│                              │
│  ─────────────────────────   │  ← Separator for non-owners
│                              │
│  [⚠️ Report No One]          │  ← Only when timer running
│  1/2 reports (need 2)        │  ← Report count display
│                              │
│  OR                          │
│                              │
│  [⚠️ Report Machine is Done] │  ← Only when completed
│  Reported by 1 user(s)       │  ← Report count display
└─────────────────────────────┘
```

### Button States
- **Active**: Orange/Red, clickable
- **Reported**: Gray, disabled, shows "Reported"
- **Hidden**: Not visible when criteria not met

---

## ✅ Testing Checklist

- [x] Report No One appears only for non-owners during countdown
- [x] Report No One requires 2 reports to reset
- [x] Report Machine Done appears only when cycle completed
- [x] Report Machine Done requires 1 report to reset
- [x] Users cannot report same machine twice
- [x] Reports sync across all users in real-time
- [x] Report counts display correctly
- [x] Machines reset properly after reports
- [x] Usage history updated correctly (cancelled vs completed)
- [x] Charges applied correctly based on report type
- [x] Notification system still works
- [x] Global visibility maintained
- [x] No errors in console

---

## 📝 User Instructions

### To Report No One:
1. See a machine running but nobody using it
2. Click "Report No One" button
3. Confirm in dialog
4. Wait for another user to also report
5. Machine resets automatically after 2 reports

### To Report Machine Done:
1. See a completed machine with clothes still inside
2. Click "Report Machine is Done" button
3. Confirm in dialog
4. Machine resets immediately

### Best Practices:
- Only report if you're CERTAIN nobody is using the machine
- Only report "Done" if you can see the clothes are collected
- Be considerate - mistakes happen!
- Use the regular "Report Issue" for machine problems

---

## 🚀 Future Enhancements (Optional)

1. **Admin Review**: Allow admins to review and reverse false reports
2. **Report History**: Track who reported what for accountability
3. **Reputation System**: Users with good reporting history get priority
4. **Time-based Auto-reset**: Auto-reset after X minutes of inactivity
5. **SMS Notifications**: Alert owner when machine is reported
6. **Photo Evidence**: Require photo when reporting for verification

---

## 📧 Support

If you encounter any issues with the reporting system:
1. Check that you're not the machine owner
2. Verify the machine is in the correct state (running or completed)
3. Refresh the page to ensure you have latest updates
4. Contact admin if problem persists

---

**Last Updated**: January 30, 2026
**Version**: 2.0 with Reporting Features
