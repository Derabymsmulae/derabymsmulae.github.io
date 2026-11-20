# Full-stack audit: what was fixed, what remains, what I recommend

## A. Fixed in this release
**Security**
- Stored cross-site scripting hole: order quantities were shown in the dashboard without escaping. Anyone able to write an order directly to the database could run code in your admin session. Everything is now escaped (tested with a hostile order).
- Added a Content Security Policy, a clickjacking guard, and `https://`-only validation for links saved in Settings.
- Orders now use a single order ID, so a retry cannot create duplicates. Cart quantities are capped, and checkout entries are length- and format-checked (M-Pesa code must be 8 to 12 letters or digits).
- Hardened Firestore rules: only known fields, size limits, type checks and valid currency.
- The dashboard flags orders whose total does not match listed prices, and signs out after 30 minutes idle.
- If the shop data fails to load, customers now see a retry message instead of sample products.
**Scale and speed**
- The whole catalogue is one document, so each visit costs about two small reads. Catalogue and settings are cached for 2 minutes in the browser, and orders are checked fresh at checkout. Admin lists are paginated, orders load in pages of 100, and the new-order check reads one document.
- Hero image prioritised, below-the-fold sections render lazily, Cloudinary preconnect, loading placeholders.
**Features you asked for**
- Search by partial words, typos, synonyms and price ("under 500"), with suggestions; Enter closes the phone keyboard and shows results.
- Independent edit and delete for photos, details, price and status.
- Editable Our Story, home story and Questions; popup policies with checkout agreement stored on each order; cookie notice.
- "How to order" and the repeated feature strip removed; the order steps are now the first Question.
**Extras**: orders CSV download, order status filter, one-click "mark as sold" when paid, shareable links to each dera (`?p=`), product structured data for Google.

## B. Needs your decision or input
1. Policies are drafts. I made two assumptions from your brief: the 2-day window applies to every return or exchange request, and refunds go back through M-Pesa after the item is received and inspected. Confirm both.
2. Real prices for the deras still at KES 700, back photos for single-photo deras, and clean photos for the 3 pending kaftans.
3. Your M-Pesa number in Settings. Until then customers are told to ask on WhatsApp.
4. Kenyan legal review: Terms, Returns, Privacy, and whether you must register with the Office of the Data Protection Commissioner.

## C. Limits of the free architecture (honest)
| Issue | Why | Fix |
|---|---|---|
| Order totals come from the browser | No server code | Cloud Functions to recompute totals and stock (Blaze plan) |
| Payment is manual | M-Pesa STK Push needs a server and Safaricom Daraja approval | Functions plus Daraja |
| Spam orders possible | No per-visitor limit | Firebase App Check with reCAPTCHA (Blaze) |
| No automatic backups | Scheduled exports need billing | Scheduled Firestore export; meanwhile use Download backup weekly |
| Single owner login, no MFA | Free Auth tier | Paid identity tier or a custom role system |
| Products cannot be found individually by Google | The site is one page; only `?p=` links exist | A statically generated site (a larger rebuild) |
| Catalogue document limit (about 1 MB) | Single document design | Fine to roughly 800 products with Cloudinary links; beyond that, split into several documents |
| Unsigned Cloudinary preset | Browser upload | In Cloudinary, restrict the preset: allowed formats, max file size, folder; later use signed uploads |

## D. My recommended order of work
1. Now: upload this build, publish the new rules, set prices and your M-Pesa number, review policies.
2. Within a month: buy the `.co.ke` domain; set Cloudinary preset restrictions; set a weekly reminder to download the backup; add UptimeRobot (free) to watch the site.
3. When orders grow: Blaze plan plus Functions for verified totals, App Check, STK Push, automatic "sold" on payment, and order notifications to your WhatsApp or email.
4. Later: static product pages for search engines, customer accounts and order tracking.

## E. Scale answers
- Visitors: GitHub Pages and the Cloudinary CDN carry traffic. The database is the only counted resource: about two reads per visit means the free tier's daily read allowance (check Firebase's pricing page for the current figure) covers many thousands of visits a day, and beyond that the cost per read is small.
- Orders: each order is one write. Writes and the dashboard are comfortably inside the free allowance for a growing small shop.
- Maintainability: all wording, policies, products and photos are editable in the dashboard; the code lives in one file plus `config.js`, so a developer can take it over easily.
