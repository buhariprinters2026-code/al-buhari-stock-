# AL-BUHARI XEROX & STATIONERY — Cloud Stock & Billing

Responsive web app for phones, tablets and computers. It includes inventory, purchase entry, product + service billing, customer list, reports, owner/staff access and real-time stock updates.

## Important: cloud setup is required

This is a deployable software project, not a hosted live website yet. To synchronize multiple devices, connect it to your own Supabase cloud project and deploy the website. No database or hosting account is created automatically by this source package.

## 1. Create the cloud database

1. Open https://supabase.com and create a project.
2. In the Supabase project, open **SQL Editor**.
3. Open `supabase/schema.sql` from this project, copy all its contents, and run it.
4. In **Authentication → Providers → Email**, enable Email login. For easiest first-time testing, you may temporarily disable email confirmation; enable it again for normal operation.
5. In **Project Settings → API**, copy the Project URL and anon/public key.

## 2. Configure the website

1. Install Node.js (LTS) on a computer.
2. Extract this ZIP.
3. Copy `.env.example` to `.env`.
4. Fill in:
   - `VITE_SUPABASE_URL` = your Supabase Project URL
   - `VITE_SUPABASE_ANON_KEY` = your Supabase anon/public key
5. In the project folder run:

```bash
npm install
npm run dev
```

Open the local address printed by Vite to test.

## 3. Create the owner login

1. Open the website.
2. Select **Create owner account**.
3. Register with your email and a strong password.
4. The first self-registered account creates its own AL-BUHARI shop and becomes its owner.
5. Sign in.

Do not share the owner password with staff.

## 4. Enable staff invitations

The staff invitation feature uses a Supabase Edge Function and the service-role key must NEVER be put in the website `.env`.

Install the Supabase CLI, log in, link the project, then set the function secret and deploy:

```bash
supabase login
supabase link --project-ref YOUR_PROJECT_REF
supabase secrets set SUPABASE_SERVICE_ROLE_KEY=YOUR_SERVICE_ROLE_KEY
supabase functions deploy invite-staff
```

The function also receives `SUPABASE_URL` and `SUPABASE_ANON_KEY` from the Supabase environment. If not present in your function environment, set them with `supabase secrets set`.

From the owner account, open **Staff access** and invite each staff member by email. They receive a Supabase invitation and join the same shop with the staff role.

## 5. Publish the website

Recommended simple hosting: Vercel or Netlify.

- Import/upload this project.
- Add the same `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` environment variables in the hosting dashboard.
- Build command: `npm run build`
- Output directory: `dist`
- Deploy and open the published HTTPS URL on all phones and computers.

Add the published website URL to Supabase **Authentication → URL Configuration → Redirect URLs**.

## How stock works

- Products can be stock-tracked; printing/service entries can be non-stock services.
- A purchase entry increases quantity.
- A bill calls a PostgreSQL function that locks and validates stock, creates the invoice, deducts quantity and records stock movements in one transaction.
- If the available quantity is insufficient, the whole bill is rejected and no partial deduction occurs.
- Realtime subscriptions refresh inventory and bill data on connected devices.
- Manual stock corrections are recorded in the stock movement audit log.

## Suggested first setup

Add your actual items and opening quantities in **Stock register**. Suggested groups:
- Stationery items
- Paper and Xerox materials
- Binding materials
- Photo printing materials
- Xerox / Printing services (non-stock service items)

For printing services, add entries such as B&W single-side, B&W double-side, colour full page, colour text, photo print and binding services with their selling prices. These are billable service entries and do not deduct stock by default. If you want paper/consumables to be deducted automatically when a printing service is billed, that needs a service recipe/BOM feature configured for your actual usage.

## Security notes

- The browser uses only the Supabase anon/public key; row-level security protects shop data.
- Never put the Supabase service-role key in `.env` or frontend source.
- Use HTTPS and strong unique passwords.
- Back up your Supabase database regularly.
- This starter is intended for one shop workspace per owner account. Multi-branch support, GST invoices, barcode scanning, thermal printer layouts, WhatsApp sharing and bill cancellation/returns can be added in a later version.


## Branding and product profit fields
- The supplied Al-Buhari gold logo is stored at `public/al-buhari-logo.png` and appears on the sign-in/setup screens and sidebar.
- Stock register records both unit purchase cost and unit selling price.
- Profit / unit is calculated as selling price minus purchase price.
- Stock profit is an estimate for current stock quantity: profit per unit multiplied by current quantity. It is not net profit and excludes rent, wages, utilities, wastage, discounts and other expenses.
