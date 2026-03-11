# 🏸 Bisdakmintoners
### Badminton Club Management App

A full-featured, single-file web app for managing badminton club sessions, queue match-ups, chip-in payments, and group projects. Built with plain HTML/JS and Firebase Firestore — no build tools required.

---

## 📦 File Structure

```
bisdakmintoners/
├── index.html        ← The entire app (HTML + CSS + JS in one file)
├── netlify.toml      ← Netlify deployment configuration
└── README.md         ← This file
```

---

## 🚀 Deployment Guide

### Step 1 — Create a Firebase Project

1. Visit [https://console.firebase.google.com](https://console.firebase.google.com)
2. Click **Add project** → name it `bisdakmintoners` → click **Continue** through the steps
3. Go to **Build → Firestore Database** → **Create database**
4. Choose **Start in test mode** → select a region → click **Enable**

### Step 2 — Get Your Firebase Config

1. In your Firebase project, click the ⚙️ gear icon → **Project Settings**
2. Under **Your apps**, click the **`</>`** (Web) icon
3. Register the app with any nickname (e.g. `bisdakmintoners-web`)
4. Copy the entire `firebaseConfig` object shown

### Step 3 — Add Config to the App

Open `index.html` and find this block (around line 799):

```javascript
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

Replace each placeholder value with your actual Firebase credentials.

### Step 4 — Push to GitHub

```bash
git init
git add .
git commit -m "Bisdakmintoners v2"
git remote add origin https://github.com/YOUR_USERNAME/bisdakmintoners.git
git push -u origin main
```

### Step 5 — Deploy on Netlify

1. Go to [https://app.netlify.com](https://app.netlify.com)
2. Click **Add new site** → **Import an existing project** → **GitHub**
3. Authorize Netlify and select your repository
4. Configure build settings:
   - **Build command:** *(leave empty)*
   - **Publish directory:** `.`
5. Click **Deploy site**

Your app will be live at a URL like `https://bisdakmintoners.netlify.app`.

> **Auto-deploy:** Every time you push a new commit to GitHub, Netlify will automatically redeploy your app.

---

## 🔐 Role-Based Access

Login uses a simple **4-digit passcode per role**. Select your role on the login screen, enter the passcode, and — for Members — choose your name from the dropdown.

| Role | Default PIN | Access Level |
|------|:-----------:|--------------|
| 👑 **Super Admin** | `9999` | Full access: members, sessions, queue, chip-in, projects, history, and passcode settings |
| 🛡️ **Admin** | `2222` | Generate match-ups, manage sessions, record results, manage chip-in & projects |
| 🏸 **Member** | `1111` | Select your name, view your queue position, input your own chip-in & project payments |
| 👁️ **Guest** | *(no PIN)* | View-only access to all pages — no edits allowed |

> Passcodes can be changed anytime by the Super Admin under **Settings**.

---

## 📱 Pages & Features

### 📊 Dashboard
- Live stats: total members, sessions, projects, and members with outstanding balances
- **Latest Session** card — shows date, venue, and all attendees with avatars
- **Outstanding Balances** panel — lists every member with unpaid chip-in or project balances across all sessions and projects, sorted by amount owed
- Quick-pay shortcut directly from the dashboard

---

### 👥 Members
- Add, edit, and delete club members
- Fields: **Full Name**, **Nickname** (shown in queue for quick identification), **Skill Level**, **Contact**
- Skill levels: `Beginner` · `Intermediate` · `Advanced` — used for balanced match-ups and Advanced Battle Mode
- Color-coded avatars auto-generated from each member's name

> Members page is visible to Super Admin, Admin, and Guest. Members log in and are identified by name, not this page.

---

### 🏸 Queue

#### For Admins & Super Admins
Complete queue management for **1 court, doubles format (4 players per match)**.

**Creating a Session**
- Set the date, venue, notes, and per-head chip-in amount
- Select which members are attending that session
- The system initializes the queue order from the attendee list

**Generating a Match-Up**
- Click **⚡ Generate Next** to create the next match
- The system takes the next 4 players from the queue
- Teams are **skill-balanced**: strongest paired with weakest, 2nd paired with 3rd
- The current match is displayed on the **Court 1** visual with Team A vs Team B

**Recording Results**
- Click **Team A Wins**, **Draw**, or **Team B Wins**
- Queue automatically rotates:
  - **Winners** → move to the front of the waiting queue
  - **Losers** → go to the back of the queue
  - **Draw** → both teams go to the back

**Other Controls**
- **🔀 Shuffle** — randomize the entire queue order at any time
- Queue list shows each player's position, skill badge, and **ON COURT** tag if currently playing
- Advanced players in the queue are highlighted with a red left border

---

#### ⚔️ Advanced Battle Mode

A special feature that schedules **Advanced-only showdown matches** at a configurable interval within the normal queue rotation.

**How to enable:**
1. Open the **Advanced Battle Mode** panel on the Queue page
2. Toggle the switch **ON**
3. Set the **interval** — e.g. `3` means every 3rd match will be an Advanced Battle

**How it works:**
- When a match number is a multiple of the set interval (e.g. match 3, 6, 9...), the system checks how many **Advanced** players are attending that session
- If **4 or more Advanced players** are present → they are pulled from the attendee list, shuffled randomly, and split into **2 Advanced-only teams of 2**
- If **fewer than 4 Advanced players** are present → the battle is skipped with a notification, and a regular balanced match runs instead
- Advanced Battle matches do **not disrupt the normal queue** — waiting players keep their positions and the rotation continues as usual after the battle

**Visual indicators:**
- 🔥 Animated **glowing red banner** on the Court display during an Advanced Battle
- **⚔️ ADVANCED BATTLE — Elite Showdown!** header replaces the regular court label
- **⚔️ ADV** / **🏸 REG** type tags on every match in the history table
- Queue order header shows *"⚔️ ADV battle in X matches"* countdown so the admin knows what's coming
- **Next ADV battle: match #N** shown in the settings panel
- Advanced players in the queue list have a **red left border** for easy spotting

> Advanced Battle Mode settings (on/off + interval) are saved in the browser's local storage so they persist across page reloads on the same device.

#### For Members
Members see a simplified queue view — select a session, view the current court match-up and queue order, and check their own position.

---

### 💰 Chip-In

Tracks each member's contribution for a session's shared expenses (court fees, shuttlecocks, etc.).

- Select any session from the dropdown to view its chip-in status
- **Per-head amount** is set when creating the session (or updated later by Admins)
- Each attendee's row shows: amount paid, balance remaining, and payment status
- Payment statuses: ✓ **Paid** · ◐ **Partial** · ✕ **Unpaid**
- Admins can update any member's payment; Members can update only their own
- **Mark as Fully Paid** shortcut button in the payment form
- Collection progress bar shows % of total amount collected vs. total due
- Stats cards show: Per Head amount · Total Collected · Remaining balance

---

### 📋 Group Projects

Track shared club expenses beyond regular sessions — jersey printing, equipment purchases, tournament fees, events, and more.

**Creating a Project**
- Set project name, description, target date, and status (`Active` / `Completed` / `Cancelled`)
- Choose payment type:
  - **Fixed amount** — same cost for every participant (e.g. ₱500 per jersey)
  - **Custom per member** — each member can have a different amount due
  - **Both** — set a default fixed amount that can be overridden per member when needed
- Select which members are part of this project (default: all members; can be customized per project)

**Tracking Payments**
- Each member's row shows: amount due, amount paid, balance, and status badge
- Admins can update any payment; Members can update only their own entry
- **Override individual amount due** — useful when sizes, quantities, or custom orders differ per member
- Collection progress bar per project
- Total collected vs. total project cost displayed at the top

**Project Cards**
- Overview grid shows all projects with name, description, target date, member count, collection progress bar, and status badge
- Click any project card to open its full payment detail view

---

### 📜 History

A complete audit log of every queue session.

- Each session card shows: date, venue, notes, attendee count, per-head amount, regular match count, and Advanced Battle match count
- **Attendance table** per session: member name, skill level badge, attended checkmark
- **Payment columns** (when a per-head amount is set): amount paid, outstanding balance, payment status
- **Win/Loss record** per player per session — calculated from match results
- **Full Details →** button opens a session detail modal with the full attendee list and a complete match-by-match results log, including match type tags (⚔️ ADV / 🏸 REG)

---

### ⚙️ Settings *(Super Admin only)*

- **Change Passcodes** — update the 4-digit PIN for Member, Admin, and Super Admin roles; changes save to Firebase and apply across all devices instantly
- **Data Summary** — quick stats: total members, sessions, projects, and total matches played

---

## 🔄 Queue Rotation Logic

```
Session created → All attendees added to queue in selected order

Admin clicks ⚡ Generate Next Match:
  │
  ├─ Advanced Battle Mode ON?
  │   ├─ YES: Is this match number a multiple of the interval?
  │   │   ├─ YES + 4 or more ADV players attending
  │   │   │     → ⚔️ Advanced Battle: random ADV-only teams
  │   │   ├─ YES + fewer than 4 ADV players
  │   │   │     → Skip battle, run regular match with notification
  │   │   └─ NO → Regular match
  │   └─ NO → Regular match
  │
  └─ Regular Match: Take next 4 from queue → Balance by skill
        Strongest + Weakest  →  Team A
        2nd strongest + 3rd  →  Team B

Admin records result:
  ├─ Winners → Move to front of waiting queue (play again sooner)
  ├─ Losers  → Sent to back of queue
  └─ Draw    → Both teams sent to back of queue
```

---

## 🔥 Firebase Firestore Collections

The app automatically creates and manages these Firestore collections:

| Collection | Key Fields |
|---|---|
| `members` | name, nickname, skill, contact, createdAt |
| `sessions` | date, venue, notes, perHead, attendees[], queueOrder[], matches[], payments[] |
| `projects` | name, description, targetDate, status, fixedAmount, totalCost, members[], createdAt |
| `settings` | `passcodes` document: { member, admin, superadmin } |

Each `matches[]` entry inside a session contains:

```json
{
  "id": "m1234567890",
  "matchNum": 3,
  "matchType": "advanced",
  "team1": ["memberId1", "memberId2"],
  "team2": ["memberId3", "memberId4"],
  "result": "teamA",
  "createdAt": "2025-01-15T10:30:00.000Z",
  "recordedAt": "2025-01-15T10:45:00.000Z"
}
```

---

## 🔒 Firebase Security Rules

The default test mode rules allow open read/write access. This is fine for internal club use. For a more secure setup, replace the default rules in your Firebase console (**Firestore → Rules**) with:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

For production with public access, consider enabling **Firebase Authentication** and restricting writes to authenticated users.

---

## 🛠️ Customization Notes

| What to change | Where in `index.html` |
|---|---|
| Currency symbol (₱) | Search and replace `₱` throughout the file |
| Skill level names | Member modal `<select>` options + `buildBalancedTeams()` function |
| Default passcodes | `STATE.passcodes` object near the top of the `<script>` block |
| Court count | Would require changes to `renderQueueBody()` and match generation logic |
| PIN length | `maxlength` on PIN `<input>` elements + validation in `doLogin()` |

---

## 📋 Quick Reference — Default Passcodes

| Role | PIN |
|---|:---:|
| 👑 Super Admin | `9999` |
| 🛡️ Admin | `2222` |
| 🏸 Member | `1111` |
| 👁️ Guest | *(none)* |

> **Security tip:** Change all passcodes immediately after your first login via **Settings → Passcodes**.

---

*Built with ❤️ for Bisdakmintoners Badminton Club 🏸*
