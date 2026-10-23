# Clinic Queue

A live patient queue for the clinic: the screener issues numbers and the doctor, nurse, physio and dentist stations call them. There is also a Session IC dashboard for the waiting-room TV. All stations share one queue through Firebase. Only ticket numbers are stored, never patient names.

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

## Part C: Publish on Firebase Hosting

The site is hosted by Firebase, in the same project as the database. You need the Firebase CLI (`npm install -g firebase-tools`, then `firebase login`).

From this folder, run:

```
firebase deploy
```

This publishes `index.html` and the database rules in `database.rules.json`. The site is at:
**https://samwmc-778b2.web.app/**

## Station bookmarks

Each link opens straight to that station's screen. Sign in once on each device and it stays signed in.

| Station | Link |
|---|---|
| Front door screener | https://samwmc-778b2.web.app/#screener |
| Doctor | https://samwmc-778b2.web.app/#doctor |
| Nurse (BT / P / M) | https://samwmc-778b2.web.app/#nurse |
| Physio | https://samwmc-778b2.web.app/#physio |
| Dentist | https://samwmc-778b2.web.app/#dental |
| Session IC dashboard (TV) | https://samwmc-778b2.web.app/#board |

**More than one person at a station:** each station has up to 3 counters, and each counter calls its own next patient from the shared queue. Pick the counter at the top of the station screen, or bookmark it directly by adding the counter number, for example https://samwmc-778b2.web.app/#doctor/2 for Doctor counter 2. Each device remembers its counter. To change how many counters a station has, edit `counters: 3` in the `STATIONS` list in `index.html`.

## Daily use

- **Start of day:** click **Reset all** and type `RESET` to set every queue back to 001.
- **Session IC dashboard (TV):** open the **Session IC dashboard** tab, press **Turn on sound** so each call plays a chime and reads the number aloud, then press **Full screen**. Sound has to be turned on again whenever the TV page is reloaded.
- **Pressed "I have called this number" by mistake?** Press **Undo** in the message at the bottom of the screen within a few seconds.
- **Changing the page later:** edit `index.html`, run `firebase deploy`, then commit and push the change to GitHub so the repository matches the live site.
