# Clinic Queue

A live patient queue for the clinic: the screener issues numbers and the doctor, nurse, physio and dentist stations call them. There is also a waiting-room board. All stations share one queue through Firebase. Only ticket numbers are stored, never patient names.

## Part A: Set up Firebase (free, about 10 minutes)

1. Go to https://console.firebase.google.com and click **Create a project**. Name it something like `clinic-queue`. You can turn Google Analytics off.
2. **Add a web app:** on the project home page, click the **</>** (Web) icon, give it a nickname, and click **Register app**. You don't need Firebase Hosting. Firebase then shows a `firebaseConfig = { ... }` block. Keep this page open; you'll paste it in Part B.
3. **Turn on the database:** go to **Build → Realtime Database → Create database**.
   - Location: **Singapore (asia-southeast1)**.
   - Choose **Start in locked mode**.
4. **Set the database rules:** in Realtime Database, open the **Rules** tab. Replace everything there with the code below and click **Publish**:
   ```json
   {
     "rules": {
       "state": {
         ".read": "auth != null",
         ".write": "auth != null"
       }
     }
   }
   ```
   With these rules, only signed-in staff can see or change the queue.
5. **Turn on staff sign-in:** go to **Build → Authentication → Get started → Email/Password → Enable → Save**.
6. **Create the staff login:** in Authentication, open the **Users** tab and click **Add user**. You can make one shared login for the whole clinic, for example `staff@yourclinic.com` with a strong password, or one login per staff member.

## Part B: Put your config into the page

Open `index.html` in a text editor such as Notepad. Near the top of the `<script>` section, find the block that says **STEP 1: PASTE YOUR FIREBASE CONFIG HERE**. Replace the placeholder `firebaseConfig = { ... }` with the one Firebase showed you in Part A, step 2, and save the file.

Make sure the `databaseURL` line is included. If Firebase's snippet doesn't have it, copy the URL shown at the top of the Realtime Database page; it looks like `https://clinic-queue-xxxx-default-rtdb.asia-southeast1.firebasedatabase.app`.

The `apiKey` in this config is not a password. It's normal for it to be public. The database rules and the staff login are what keep the queue safe.

## Part C: Publish on GitHub Pages

1. Sign in at https://github.com. Click **+** (top right), then **New repository**. Name it `clinic-queue`, set it to **Public**, and click **Create repository**.
2. Click **uploading an existing file**. Drag in `index.html` and `README.md`, then click **Commit changes**.
3. Go to **Settings → Pages**. Under *Build and deployment*, set Source to **Deploy from a branch**, Branch to **main**, and folder to **/ (root)**. Click **Save**.
4. Wait about 1–2 minutes. Your site will be at:
   **https://fcke109.github.io/clinic-queue/**

## Station bookmarks

Each link opens straight to that station's screen. Sign in once on each device and it stays signed in.

| Station | Link |
|---|---|
| Front door screener | https://fcke109.github.io/clinic-queue/#screener |
| Doctor | https://fcke109.github.io/clinic-queue/#doctor |
| Nurse (BT / P / M) | https://fcke109.github.io/clinic-queue/#nurse |
| Physio | https://fcke109.github.io/clinic-queue/#physio |
| Dentist | https://fcke109.github.io/clinic-queue/#dental |
| Waiting-room TV | https://fcke109.github.io/clinic-queue/#board |

## Daily use

- **Start of day:** click **Reset all** and type `RESET` to set every queue back to 001.
- **Changing the page later:** edit `index.html` on GitHub (pencil icon) and commit. The site updates in about a minute.
