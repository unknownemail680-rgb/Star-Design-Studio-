# STAR DESIGN STUDIO | Complete Website + Admin Backend

આ ZIP માં React website, Admin Panel અને Supabase backend માટેનું SQL schema સામેલ છે. Website mobile/desktop responsive છે. Website માં 14 categories, search, design gallery/detail, favourites અને enquiry form છે. Admin માં login, category management, design publish/delete, mobile પરથી photo upload અને customer enquiries જોવા મળે છે.

## મહત્વની વાત

Backend નો code અને database schema તૈયાર છે, પરંતુ તમારા નામે Supabase project અને hosting account હું અહીંથી બનાવી શકતો નથી. તેના માટે તમારે તમારા account માં project બનાવવો પડશે અને તેની Project URL તથા public anon key નાખવી પડશે. આ બે values વગર કોઈ પણ website live backend સાથે connect થઈ શકતી નથી.

## Android મોબાઇલ પરથી backend જોડવાની રીત

### 1) Supabase project બનાવો
1. `https://supabase.com` ખોલો અને account બનાવો / login કરો.
2. **New project** દબાવો. Project name `star-design-studio` રાખો, database password સાચવી રાખો અને region નજીકનું પસંદ કરો.
3. Project તૈયાર થાય પછી **SQL Editor → New query** ખોલો.
4. આ ZIP માંથી `supabase/schema.sql` ખોલીને આખું SQL copy-paste કરો અને **Run** દબાવો.

### 2) તમારો Owner Admin login બનાવો
1. Supabase માં **Authentication → Users → Add user** ખોલો.
2. તમારો email અને password નાખીને user બનાવો.
3. બનેલા user પર tap/click કરીને તેનો **UUID** copy કરો.
4. SQL Editor માં આ command માં `PASTE_USER_UUID_HERE` ને તમારા UUID થી બદલીને Run કરો:

```sql
insert into public.admin_users (user_id, role)
values ('PASTE_USER_UUID_HERE', 'owner')
on conflict (user_id) do nothing;
```

આ પગલું જરૂરી છે. માત્ર Auth user બનાવવાથી Admin Panel permission મળતી નથી. આ setup માં public signup બંધ રાખો. Admin access ફક્ત `admin_users` table માં ઉમેરેલા account ને જ મળે છે.

### 3) Website ને Supabase સાથે connect કરો
1. Supabase → **Project Settings → API** ખોલો.
2. **Project URL** અને **anon / publishable public key** copy કરો.
3. Project files માં `.env.example` ની copy બનાવી `.env` નામ આપો.
4. તેમાં values નાખો:

```env
VITE_SUPABASE_URL=https://YOUR-PROJECT.supabase.co
VITE_SUPABASE_ANON_KEY=YOUR_PUBLIC_ANON_KEY
```

**ક્યારેય `service_role` અથવા secret key website code માં ન નાખશો.** Website browser માં ચાલે છે એટલે public anon key જ વાપરવાની છે; database security RLS policies સંભાળે છે.

### 4) Website live કરો

1. ZIP extract કરો.
2. Files ને GitHub repository માં upload કરો.
3. `https://vercel.com` પર GitHub વડે login કરીને **Add New → Project** પસંદ કરો અને repository import કરો.
4. Vercel ના **Environment Variables** માં ઉપરની બંને values ઉમેરો.
5. Build command `npm run build` અને output directory `dist` રાખો (Vite સામાન્ય રીતે auto-detect કરે છે).
6. **Deploy** દબાવો. Deploy થયા પછી મળેલી website link ખોલો.

## Admin Panel કેવી રીતે વાપરવું

- Website પર **ADMIN** ખોલો અને ઉપર બનાવેલા owner email/password થી login કરો.
- **Categories:** નવી category ઉમેરો અથવા category દૂર કરો. Category image URL આપી શકો છો.
- **Designs:** Design name, category, photo URL અથવા phone માંથી upload, description અને material લખીને Publish કરો.
- **Enquiries:** Website પર આવેલ customer enquiries જુઓ.
- Photo upload `design-images` Supabase Storage bucket માં થાય છે.

## Backend માં શું છે?

- `categories`: category નામ, image અને sort order.
- `designs`: image, category, description, material, style, colour, featured/published flags અને multiple gallery image URLs માટે field.
- `enquiries`: customer details અને status.
- `site_settings`: studio name, WhatsApp number, Instagram URL, email, about text અને hero title માટે settings table.
- `admin_users`: owner/admin allowlist. RLS policies database સ્તરે admin permission ચકાસે છે.
- Supabase Storage: uploaded design photos.

## હમણાંની મર્યાદા / આગળનું કામ

આ build માં Admin UI ના મુખ્ય functions તૈયાર છે, પણ schema માં રહેલી advanced fields (multiple gallery images, featured toggle, enquiry status update, site settings editor, category reorder અને additional admin invite screen) માટે UI હજુ ઉમેરવાનું બાકી છે. WhatsApp form હાલ generic WhatsApp chat ખોલે છે; તમારા business WhatsApp number ને UI/settings સાથે જોડવાનું આગળનું integration છે. Live deploy પહેલાં તમારો WhatsApp number, Instagram URL, email અને logo આપશો તો તેને final branding માં મૂકી શકાય.

હાલના gallery/category photos sample Unsplash inspiration images છે, તમારા પોતાના projects નથી. Admin માં તમારા 3ds Max/Corona renders upload કર્યા પછી તે તમારી portfolio gallery માં દેખાશે.

## Troubleshooting

- `Invalid login credentials`: Supabase Authentication માં user/email/password તપાસો.
- Login થાય પણ Admin data add ન થાય: `admin_users` માં તમારો UUID ઉમેર્યો છે કે નહીં તે તપાસો.
- Images upload ન થાય: SQL schema run થયું છે અને `design-images` bucket દેખાય છે કે નહીં તપાસો.
- Website blank દેખાય: ZIP ને સીધું file manager માંથી ખોલવાનું નથી. Vite build/hosting દ્વારા ચલાવવાની છે.
