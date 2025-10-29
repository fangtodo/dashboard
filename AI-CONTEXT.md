# AI Context Guide for Personal Dashboard

**Project:** Single-file Personal Dashboard / To-Do App
**Location:** `/Users/alexfang/SmithRX/tmp/index.html`
**Live URL:** https://fangtodo.github.io/dashboard/
**GitHub Repo:** https://github.com/fangtodo/dashboard

---

## Project Overview

This is a **single-file HTML application** (no backend, no build process) that manages:
- General one-time to-dos
- Recurring tasks (custom day intervals)
- Rotating workout plans with daily exercises
- All data stored in browser localStorage

**Key Constraint:** Everything must remain in ONE `index.html` file with embedded CSS and JavaScript.

---

## Architecture

### File Structure
```
index.html          # Main app (embedded <style> and <script>)
icon.png            # iOS home screen icon (180x180px)
README.md           # User-facing documentation
AI-CONTEXT.md       # This file (for future AI context)
ICON-INSTRUCTIONS.md # Icon setup guide (can be deleted)
```

### Data Model (localStorage)

**Storage Key:** `myDashboardData`

```javascript
{
  generalTodos: [
    {
      id: 1,                           // Unique ID
      text: "Buy groceries",           // Task description
      completed: false,                // Completion status
      createdAt: "2025-10-29T10:00:00.000Z"  // ISO timestamp
    }
  ],

  recurringTasks: [
    {
      id: 101,                         // Unique ID
      text: "Take vitamins",           // Task description
      intervalDays: 1,                 // 1-30 days
      lastCompleted: "2025-10-29T08:00:00.000Z"  // ISO timestamp or null
    }
  ],

  workoutPlans: [
    {
      id: 1,                           // Unique ID
      name: "Plan A: Push Day",        // Plan name
      exercises: [                     // Array of exercise names
        "Push-ups",
        "Bench Press",
        "Overhead Press"
      ]
    }
  ],

  workoutState: {
    lastRotationDate: "2025-10-29T00:00:00.000Z",  // Last rotation ISO timestamp
    todayExercisesCompleted: [0, 2],   // Array of completed exercise indices
    currentPlanId: 1                   // Tracks which plan is active (prevents stale completion)
  }
}
```

---

## Core Functionality

### 1. General To-Dos (index.html:998-1021)
- Add new tasks via input field
- Toggle completion with checkbox
- Delete tasks permanently
- Tasks persist until manually deleted
- **Location in code:** `addTodo()`, `toggleTodo()`, `deleteTodo()`

### 2. Recurring Tasks (index.html:1023-1054)
- Custom interval from 1-30 days
- Tracks `lastCompleted` timestamp
- Shows as "due" if `(now - lastCompleted) >= intervalDays`
- Can be checked/unchecked on Today tab
- **Location in code:** `addRecurringTask()`, `isTaskDue()`, `getIntervalText()`

### 3. Workout Plans (index.html:1107-1244)
- **View Mode:** Read-only display of all plans
- **Edit Mode:** Add/edit/delete plans and exercises with backup/restore capability
- **Auto-Rotation:** First plan in array is "today's workout"
  - Automatically rotates to next plan each new day
  - Rotation logic: `workoutPlans.shift()` then `workoutPlans.push()`
- **Reordering:** Up/down arrows to manually change schedule
- **Exercise Completion:** Tracked by index in `todayExercisesCompleted` array
- **Bug Prevention:** `currentPlanId` tracks active plan to reset completion when plan changes
- **Location in code:** `rotateWorkoutIfNeeded()`, `checkWorkoutPlanChange()`, `enterEditMode()`, `saveEditMode()`, `cancelEditMode()`

### 4. Today's Tasks Tab (index.html:902-965)
Aggregates all tasks due today in priority order:
1. **Recurring tasks** (due today) - green badges
2. **Workout exercises** (from first plan) - blue badges
3. **General to-dos** (all) - no badge

**Sorting Logic:**
```javascript
// PRIMARY: Completion status (incomplete tasks first)
if (a.completed !== b.completed) {
    return a.completed ? 1 : -1;
}
// SECONDARY: Type order (recurring → workout → general)
return a.order - b.order;
```

**Location in code:** `getTodaysTasks()`, `render()`

---

## Critical Implementation Details

### Auto-Rotation Logic (index.html:860-887)
```javascript
function shouldRotateWorkout(data) {
    return !isToday(data.workoutState.lastRotationDate);
}

function rotateWorkoutIfNeeded(data) {
    if (shouldRotateWorkout(data) && data.workoutPlans.length > 1) {
        const firstPlan = data.workoutPlans.shift();  // Remove first
        data.workoutPlans.push(firstPlan);            // Add to end

        data.workoutState.lastRotationDate = new Date().toISOString();
        data.workoutState.todayExercisesCompleted = [];  // Reset completion
        data.workoutState.currentPlanId = data.workoutPlans[0].id;

        saveData(data);
    }
}
```
**Called on every render** to check if new day.

### Plan Change Detection (index.html:890-900)
Prevents stale exercise completion when user manually reorders plans:
```javascript
function checkWorkoutPlanChange(data) {
    if (data.workoutPlans.length > 0) {
        const currentPlan = data.workoutPlans[0];
        if (data.workoutState.currentPlanId !== currentPlan.id) {
            // Plan changed - reset completion
            data.workoutState.todayExercisesCompleted = [];
            data.workoutState.currentPlanId = currentPlan.id;
            saveData(data);
        }
    }
}
```

### Edit Mode Backup System (index.html:1107-1137)
```javascript
let editModeBackup = null;

function enterEditMode() {
    const data = loadData();
    editModeBackup = JSON.parse(JSON.stringify(data.workoutPlans));  // Deep clone
    isEditMode = true;
    render();
}

function cancelEditMode() {
    if (editModeBackup) {
        const data = loadData();
        data.workoutPlans = editModeBackup;  // Restore backup
        saveData(data);
    }
    editModeBackup = null;
    isEditMode = false;
    render();
}
```

### Recurring Task Toggle (index.html:1068-1077)
Allows checking/unchecking from Today tab:
```javascript
if (type === 'recurring') {
    const task = data.recurringTasks.find(t => t.id === id);
    if (task) {
        // If completed today, uncheck it; otherwise mark completed
        if (task.lastCompleted && isToday(task.lastCompleted)) {
            task.lastCompleted = null;
        } else {
            task.lastCompleted = new Date().toISOString();
        }
    }
}
```

---

## UI/UX Design Decisions

### Tab System
- **Today:** Aggregated view (no delete buttons, read-only)
- **To-Dos:** Manage general tasks (with delete buttons)
- **Recurring:** Manage recurring tasks (with delete buttons, shows last completed date)
- **Workout:** View/edit workout plans (edit mode with save/cancel)

### Color Scheme (Dark Mode)
- **Background:** `#1a1a2e` → `#16213e` gradient
- **Recurring badge:** `#2d5a3f` (desaturated green) on `#0d2818` background
- **Workout badge:** `#334e68` (desaturated blue) on `#0f1e2e` background
- **Primary action:** `#3b82f6` (blue)
- **Delete button:** `#dc2626` (red)
- **Text:** `#e4e4e7` (light gray)

**User preference:** Subtle, desaturated colors. No aggressive/bright colors.

### Mobile Optimization (index.html:554-601)
- Touch-friendly tap targets (min 44px)
- Prevents iOS zoom on input focus (`font-size: 16px`)
- Full-width buttons on mobile
- Flexbox wrapping for responsive layout
- iOS PWA meta tags for home screen app

### Settings Modal (index.html:256-312, 723-738)
- Export/Import hidden in modal (not prominent buttons)
- Click outside modal to close
- Centered overlay design

---

## Key Code Locations (Line Numbers)

| Feature | Function/Section | Lines |
|---------|------------------|-------|
| Data structure | `getDefaultData()` | 741-768 |
| Load/Save | `loadData()`, `saveData()` | 771-805 |
| Date utilities | `isToday()`, `formatTodayDate()` | 817-837 |
| Recurring logic | `isTaskDue()`, `getIntervalText()` | 840-857 |
| Workout rotation | `rotateWorkoutIfNeeded()` | 865-887 |
| Plan change detection | `checkWorkoutPlanChange()` | 890-900 |
| Today's tasks | `getTodaysTasks()` | 902-965 |
| Tab switching | `switchTab()` | 967-996 |
| Add todo | `addTodo()` | 998-1021 |
| Add recurring | `addRecurringTask()` | 1023-1054 |
| Toggle completion | `toggleTodo()` | 1057-1087 |
| Delete task | `deleteTodo()` | 1090-1105 |
| Edit mode | `enterEditMode()`, `saveEditMode()`, `cancelEditMode()` | 1107-1137 |
| Workout management | `addWorkoutPlan()`, `deleteWorkoutPlan()`, etc. | 1140-1244 |
| Render logic | `render()`, `renderTodoItem()`, `renderWorkoutEditor()` | 1247-1391, 1394-1458 |
| Export/Import | `exportData()`, `importData()` | 1468-1515 |

---

## Common Modification Scenarios

### Adding a New Feature
1. Update data structure in `getDefaultData()` (line 741)
2. Update `loadData()` to handle migration (line 771)
3. Add UI elements in HTML section (line 604-713)
4. Add functions in JavaScript section (line 715-1521)
5. Update `render()` to display new feature (line 1394)

### Changing Colors/Styles
- All CSS is embedded in `<style>` tag (line 12-602)
- Dark mode colors: line 20-26
- Badge colors: line 195-213
- Button colors: line 109-134

### Modifying Workout Rotation
- Change rotation logic in `rotateWorkoutIfNeeded()` (line 865)
- Currently rotates daily, moves first plan to end
- To change: modify array manipulation logic

### Adjusting Task Priority
- Change order assignment in `getTodaysTasks()` (line 902)
- Current: recurring=1, workout=2, general=3
- Lower number = higher priority

---

## Bug Fixes Applied (Historical Context)

### Bug: Exercise completion persisted when changing workout plan
**Fix:** Added `currentPlanId` tracking and `checkWorkoutPlanChange()` function

### Bug: Completed tasks only moved to bottom of their section
**Fix:** Changed sort to prioritize completion status before type order

### Bug: Can't uncheck recurring tasks from Today tab
**Fix:** Added toggle logic to check if `lastCompleted` is today, then clear it

### Bug: Delete buttons showed on Today tab
**Fix:** Added `showDelete` parameter to `renderTodoItem()`

### Bug: No way to abandon edit mode changes
**Fix:** Implemented `editModeBackup` system with save/cancel buttons

---

## Testing Checklist

When making changes, test these scenarios:

- [ ] Add/complete/delete general todo
- [ ] Add recurring task (1 day, 7 days, 30 days)
- [ ] Check recurring task from Today tab, then uncheck
- [ ] Add workout plan, add/delete exercises
- [ ] Reorder workout plans with up/down arrows
- [ ] Enter edit mode, make changes, cancel (should revert)
- [ ] Enter edit mode, make changes, save (should persist)
- [ ] Complete some exercises, reorder plans (should reset completion)
- [ ] Complete tasks from Today tab (should move to bottom)
- [ ] Export backup JSON
- [ ] Import backup JSON (should restore data)
- [ ] Refresh page (data should persist)
- [ ] Open in new browser/incognito (should start fresh)
- [ ] Test on mobile (responsive layout, touch targets)

---

## Future Enhancement Ideas

### Potential Features
- [ ] Due dates for general todos
- [ ] Priority levels (high/medium/low)
- [ ] Categories/tags for tasks
- [ ] Search/filter functionality
- [ ] Dark/light mode toggle
- [ ] Custom workout rest days
- [ ] Workout history/stats
- [ ] Streaks counter
- [ ] Undo/redo functionality
- [ ] Drag-and-drop reordering (currently up/down buttons)
- [ ] Rich text notes
- [ ] Reminders/notifications (requires service worker)

### Known Limitations
- localStorage size limit (~5-10MB per domain)
- No cloud sync (manual export/import only)
- No collaboration features
- No offline service worker (just cached by browser)
- Exercise reordering uses buttons, not drag-and-drop
- No data encryption

---

## Deployment Notes

### Current Setup
- **Hosting:** GitHub Pages (free)
- **Repository:** https://github.com/fangtodo/dashboard
- **Branch:** `main` (root directory)
- **Files to upload:**
  - `index.html` (required)
  - `icon.png` (required for iOS icon)
  - `README.md` (optional, for repo documentation)

### Making Updates
1. Edit `index.html` locally
2. Test thoroughly in browser
3. Upload to GitHub (overwrites old file)
4. Wait 1-2 minutes for GitHub Pages to update
5. Hard refresh browser (Cmd+Shift+R) to clear cache
6. On mobile: remove app from home screen, re-add to get latest version

### iOS Home Screen Icon
- **File:** `icon.png` (180x180px PNG)
- **Meta tags in HTML:**
  ```html
  <meta name="apple-mobile-web-app-title" content="Tasks">
  <link rel="apple-touch-icon" href="icon.png">
  <link rel="icon" type="image/png" href="icon.png">
  ```
- To change icon: replace `icon.png` and re-add to home screen

---

## Important Reminders for AI

1. **DO NOT split into multiple files** - everything must stay in `index.html`
2. **DO NOT add build tools** - no webpack, no npm, no compilation
3. **DO NOT add external dependencies** - no CDN links, no libraries
4. **DO preserve localStorage compatibility** - migrations must be backward-compatible
5. **DO test on mobile** - primary use case is iPhone Chrome
6. **DO maintain dark mode** - user preference for subtle, desaturated colors
7. **DO preserve single-file architecture** - this is a core requirement

---

## Quick Start for AI

When the user comes back with a request:

1. **Read this file first** to understand context
2. **Read `/Users/alexfang/SmithRX/tmp/index.html`** to see current implementation
3. **Ask clarifying questions** about the desired change
4. **Make changes directly to index.html** (use Edit tool, not Write)
5. **Explain what changed** and what lines were modified
6. **Remind user to test thoroughly** before uploading to GitHub

---

**Last Updated:** October 29, 2025
**Project Status:** ✅ Complete and deployed
**Version:** v1.0 (production)
