# Personal Dashboard

A simple, offline-first personal dashboard for managing daily tasks, recurring activities, and workout plans. Built as a single HTML file with no backend - all data stored locally in your browser.

## Features

- **Today's Tasks**: Dynamic view showing all tasks due today
- **General To-Dos**: One-time tasks you need to complete
- **Recurring Tasks**: Tasks that repeat every X days (1-30 days)
- **Workout Plans**: Rotating workout routines that automatically cycle daily
- **Dark Mode**: Easy-on-the-eyes dark theme optimized for mobile
- **Offline-First**: Works completely offline, no internet required
- **Export/Import**: Backup your data as JSON files
- **Mobile Optimized**: Designed for iPhone/Chrome with responsive layout

## Getting Started

### Option 1: Open Locally

Simply open `index.html` in your web browser. That's it!

### Option 2: Deploy to GitHub Pages

1. **Create a GitHub account** at [github.com](https://github.com) (if you don't have one)

2. **Create a new repository**:
   - Click the "+" icon → "New repository"
   - Name: `personal-dashboard` (or any name you like)
   - Set to **Public**
   - Check "Add a README file"
   - Click "Create repository"

3. **Upload the file**:
   - Click "Add file" → "Upload files"
   - Upload `index.html`
   - Commit the changes

4. **Enable GitHub Pages**:
   - Go to Settings → Pages (in left sidebar)
   - Under "Source", select "Deploy from a branch"
   - Branch: `main`, Folder: `/ (root)`
   - Click "Save"

5. **Access your dashboard**:
   - Wait 1-2 minutes for deployment
   - Your URL: `https://yourusername.github.io/personal-dashboard/`
   - Bookmark it or add to home screen!

## How to Use

### Today's Tasks
Shows all tasks due today in priority order:
1. Recurring tasks (green)
2. Workout exercises (blue)
3. General to-dos (gray)

Completed tasks automatically move to the bottom.

### General To-Dos
- Add one-time tasks
- Check them off when complete
- Delete when no longer needed
- Completed tasks persist until you delete them

### Recurring Tasks
- Set tasks to repeat every 1-30 days
- Check off when complete today
- Automatically reappear when due again
- Great for: taking vitamins, watering plants, weekly reviews, etc.

### Workout Plans
- **View Mode**: See all your workout plans
- **Edit Mode**: Add/edit/delete plans and exercises
- **Auto-Rotation**: First plan is today's workout, automatically rotates to next plan each day
- **Reorder**: Use up/down arrows to change the workout schedule

### Export/Import
Click "Settings" at the bottom to:
- **Export Backup**: Download your data as JSON
- **Import Backup**: Restore from a previous backup
- Use this to sync between devices or backup regularly

## Data Storage

All your data is stored locally in your browser's localStorage:
- **Location**: Browser local storage only
- **Privacy**: Never leaves your device
- **Persistence**: Data survives page refresh
- **Backup**: Use export/import for backups and cross-device sync

## Mobile Usage

### Add to Home Screen (iOS)
1. Open in Safari
2. Tap Share button → "Add to Home Screen"
3. Now it works like a native app!

### Add to Home Screen (Android)
1. Open in Chrome
2. Tap menu (⋮) → "Add to Home Screen"
3. Access from your app drawer

## Technical Details

- **Zero Dependencies**: Pure HTML, CSS, and JavaScript
- **Single File**: Everything in one `index.html` file
- **No Backend**: Completely client-side
- **No Build Step**: Just open and use
- **Mobile-First**: Responsive design optimized for phones
- **Dark Theme**: Easy on the eyes day or night

## Browser Compatibility

Works in all modern browsers:
- Chrome (recommended for mobile)
- Safari
- Firefox
- Edge

## Tips

1. **Export Regularly**: Download backups weekly to avoid data loss
2. **Cross-Device Sync**: Export from device A, import to device B
3. **Plan Ahead**: Set up recurring tasks once, forget about them
4. **Workout Variety**: Add multiple workout plans for automatic rotation
5. **Start Fresh**: New day = completed tasks from yesterday disappear (except general to-dos)

## Troubleshooting

**Data not saving?**
- Make sure you're not in private/incognito mode
- Check that localStorage is enabled in your browser

**Can't access GitHub Pages site?**
- Wait 2-5 minutes after enabling Pages
- Check the GitHub Pages settings for the URL
- Ensure repository is set to Public

**Tasks not showing up?**
- Check the specific tab (Today/To-Dos/Recurring/Workout)
- Recurring tasks only show when due
- Workout rotates daily

## Support

This is a personal project with no official support, but feel free to:
- Fork and customize for your needs
- Report issues you find
- Share improvements

## License

Free to use, modify, and share. No attribution required.

---

Built with Claude Code 🤖
