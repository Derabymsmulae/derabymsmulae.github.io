# Updating your LIVE site to the Premium build

Your products, photos, orders and settings live in Firebase and Cloudinary, so they are not touched by this update. Your `config.js` (Firebase and Cloudinary details) stays as it is.

## Step 1. Make a backup (5 minutes)
1. In your dashboard: Products > **Download backup**. Keep the file.
2. On GitHub: your repository > **Code > Download ZIP**. Keep it.

## Step 2. Upload the new site
1. Unzip `dera-PREMIUM-UPLOAD.zip`.
2. Open your live repository on GitHub > **Add file > Upload files**.
3. Drag in everything: `index.html`, `404.html`, `manifest.webmanifest`, `robots.txt`, `sitemap.xml`, `.nojekyll` and the `images` folder. If GitHub struggles with the number of files, upload the `images` folder first, commit, then the rest.
4. **Do not upload a config.js and do not delete yours.**
5. Commit. Wait 2 minutes. Open your site and press Ctrl+Shift+R (or use a private window).

## Step 3. Update the security rules (5 minutes)
Firebase > Firestore Database > Rules. Open `firestore.rules` from this package, replace BOTH `REPLACE_WITH_YOUR_EMAIL@gmail.com` with your owner login email (lowercase), paste the whole file over the old rules, click **Publish**.
Do this after Step 2. The new rules are stricter: they only accept well-formed orders, limit field sizes and block extra fields.

## Step 4. Check the new dashboard features
- **Products > Edit**: opens an editor with separate cards for Photos, Details, Price and status, and Delete. Each card saves on its own. You can replace one photo, move photos, delete one photo or the whole dera without touching anything else.
- **Pages and content**: edit your Our Story page, the home story block and the Questions list. No code.
- **Policies**: review and edit the Terms, Returns and Refunds, Privacy and Cookies pop-ups. Have a Kenyan lawyer review them.
- **Orders**: filter by status, download as CSV, and when you set an order to Paid, you are offered one click to mark its deras as Sold.
- A new-order alert appears while the dashboard is open. The dashboard signs you out after 30 minutes of inactivity.

## Step 5. Catalogue
- Already imported `products.json` earlier? Nothing to do.
- Not yet? Products > Import or back up your catalogue > choose `dashboard-import/products.json` > Import deras (leave the boxes unticked).

## Step 6. Test (10 minutes)
- [ ] Home, shop, Our Story, Delivery, Contact open; logo returns home
- [ ] Search: type "kaft", "halte" or "violet" and see suggestions; press Enter on your phone: the keyboard closes and results show
- [ ] Add to cart: the checkout requires ticking the Terms and Returns box; a test order appears in Orders
- [ ] Footer links open the policy pop-ups; the cookie notice appears once
- [ ] Edit a dera: replace one photo, then save details; confirm the other part did not change
- [ ] Phone check

## Optional: switch on the AI assistant (real language model)
The built-in assistant already works and needs nothing. The AI mode understands any phrasing, but needs a server and a paid usage account. It was tested here with simulated responses only, not against your live Firebase or the real model.
1. Upgrade Firebase to the **Blaze plan** (needs a billing card; set a budget alert in Google Cloud).
2. Get an Anthropic API key.
3. On your computer: install Node.js and the Firebase CLI (`npm install -g firebase-tools`), run `firebase login`, `firebase init functions` in a new folder, and copy the files from `assistant-server/` over the generated `functions/` folder.
4. `firebase functions:secrets:set ANTHROPIC_API_KEY` (paste the key). Set `ALLOWED_ORIGINS` to your site address(es).
5. `firebase deploy --only functions`. Copy the function URL.
6. Dashboard > Settings > **AI assistant server address**, paste the URL, Save.
If the server is down or slow, the assistant automatically falls back to the built-in mode.

## Rolling back
Re-upload the files from your Step 1 ZIP, or in GitHub open Commits, pick the earlier one and revert.
