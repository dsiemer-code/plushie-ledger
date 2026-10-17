# Plushie Ledger

A small web app for tracking crocheted plushies: products and stock, sales, and sales history. Works in any browser and can be added to a phone's home screen like an app.

- **The app** is static files hosted free on GitHub Pages.
- **The data** lives in a free Google Firebase project (Firestore database), tied to her sign-in. Her phone and computer see the same records.
- **Offline:** the app keeps a copy on the device. Sales recorded with no signal (at a craft fair, say) are saved on the phone and sync once it's back online. She does need to be online the first time she signs in on a device.

Until `firebase-config.js` is filled in, the app runs in **demo mode** with sample data and saves nothing. That's a good way to try it first.

## Setup (about 15 minutes, one time)

### 1. Create the Firebase project
1. Go to https://console.firebase.google.com and sign in with a Google account.
2. **Create a project** (name it e.g. `plushie-ledger`). Google Analytics can be turned off.
3. Stay on the free **Spark** plan. No card is needed.

### 2. Turn on sign-in
1. **Build → Authentication → Get started**.
2. Under **Sign-in method**, enable **Email/Password**.
3. Under **Users → Add user**, enter your wife's email and a password. This is her login.
4. There's no sign-up form in the app, so nobody else can create an account.

### 3. Create the database
1. **Build → Firestore Database → Create database**. Pick a location near you (e.g. `us-east1`) and start in **production mode**.
2. Open the **Rules** tab, replace everything with the contents of `firestore.rules` from this repo, and click **Publish**. This makes sure each person can only see their own data.

### 4. Connect the app
1. **Project settings** (gear icon) → **General** → **Your apps** → click the **`</>`** (Web) icon. Register the app (no Firebase Hosting needed).
2. Copy the `firebaseConfig` values it shows into `firebase-config.js`.
   These values are meant to be public. The sign-in and the rules are what protect the data.

### 5. Put it on GitHub Pages
1. Create a repository and upload all the files in this folder (keep them at the top level).
2. Repo **Settings → Pages** → Source: **Deploy from a branch**, branch `main`, folder `/ (root)`. Save.
3. After a minute the site is live at `https://dsiemer-code.github.io/plushie-ledger/` (if the repo is named `plushie-ledger`).
4. Back in Firebase: **Authentication → Settings → Authorized domains → Add domain** and add `dsiemer-code.github.io`. Without this, sign-in is refused on the live site.

### 6. Put it on her phone
Open the GitHub Pages link on her phone and sign in.
- **iPhone (Safari):** Share → **Add to Home Screen**.
- **Android (Chrome):** ⋮ menu → **Add to Home screen** / **Install app**.

## What's stored
```
users/{her-account-id}/products/{id}   name, price, materials cost, hours to make, stock, yarn color, notes
users/{her-account-id}/sales/{id}      plushie, quantity, price each, total, date, where it sold, note
```
Recording a sale lowers that plushie's stock; editing or deleting a sale puts the stock back.

The free plan allows 50,000 reads and 20,000 writes per day and 1 GB of storage, far more than a craft business will use.

## Backups
The Sales tab has a **Download CSV** button that saves the full sales history as a spreadsheet file. Doing that now and then gives her a copy outside Firebase.

## Testing locally
Modules don't load from `file://`, so serve the folder:
```
python3 -m http.server 8000
```
then open http://localhost:8000. `localhost` is already an authorized domain in Firebase.
