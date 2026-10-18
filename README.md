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
users/{her-account-id}/products/{id}   name, price, materials cost, hours to make, stock, yarn color, photo, pattern link, notes
users/{her-account-id}/sales/{id}      plushie, quantity, price each, total, date, where it sold, event, note
users/{her-account-id}/events/{id}     name, first/last day, location, booth fee, notes
```
Recording a sale lowers that plushie's stock; editing or deleting a sale puts the stock back.

**Product photos** are shrunk to about 640 px and stored with the product in Firestore, so they need no paid Firebase Storage plan and work offline.

**Recording a sale:** tap **Record sale**. Add one line per plushie the buyer takes (**+ Add another plushie**), with how many and the price each. The price fills in from the plushie's default price and can be typed over for a discount. A plushie that isn't set up yet can be typed in with **+ New plushie…**; it's added to Products with 0 in stock. Each plushie is saved as its own line, linked to the rest of the sale, and the lists show the sale as one row.

**Pattern link:** each plushie can store the web address of its crochet pattern. The product card then shows a **Pattern ↗** button that opens it in a new tab.

## Events (craft shows)
She sells at a few craft shows a year, so the app is organized around shows rather than months.

The **Events** tab holds craft shows and markets: name, dates, location and booth fee. Each one shows its sales, plushies sold, and profit after materials and the booth fee. Past shows are grouped by year with that year's totals.

When recording a sale, the **Event** box starts on the show happening today. With no show today, it starts on the event of the last sale she recorded, which helps when catching up on sales after a show. **+ New event…** creates a show right from the sale screen.

The **Overview** tab shows:
- the running totals while a show is on, or the next show with the number of plushies in stock otherwise
- this year's totals, with last year's for comparison
- sales by show for the last six shows (tap one to see its sales)

## Importing old sales
Sales tab → **Import CSV**. From Excel, use **File → Save As → CSV**. The first row needs headings: `Date, Plushie, Quantity, Price each` (or `Total`). `Sold through`, `Event`, `Note` and `Materials each` are optional. **Blank template** downloads an empty file with the right headings, and a file from **Download CSV** imports as-is.

- Dates can be `2024-03-14`, `3/14/2024`, `3/14/24`, `Mar 14, 2024`, or Excel date numbers.
- A preview shows what will be added and lists rows it can't read, by row number.
- Sales already in the ledger are skipped, so importing the same file twice is safe.
- Plushies not yet in Products can be added automatically, with 0 in stock. Event names not yet in Events can be added too, dated from their sales.
- Imported sales don't change current stock.

The free plan allows 50,000 reads and 20,000 writes per day and 1 GB of storage, far more than a craft business will use.

## Backups
The Sales tab has a **Download CSV** button that saves the full sales history as a spreadsheet file. Doing that now and then gives her a copy outside Firebase.

## Testing locally
Modules don't load from `file://`, so serve the folder:
```
python3 -m http.server 8000
```
then open http://localhost:8000. `localhost` is already an authorized domain in Firebase.
