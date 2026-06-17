# CABLAKA

Aplikasi pengajuan & approval izin pegawai. Data disimpan di Supabase (PostgreSQL, gratis).

## 1. Setup Supabase

1. Daftar gratis di https://supabase.com → buat **New Project**.
2. Buka **SQL Editor** di dashboard project, jalankan:

   ```sql
   create table cablaka_storage (
     key text primary key,
     value jsonb not null,
     updated_at timestamptz default now()
   );

   alter table cablaka_storage enable row level security;
   create policy "allow all" on cablaka_storage for all using (true) with check (true);
   ```

3. Buka **Settings → API**, catat `Project URL` dan `anon public key`.

## 2. Setup lokal

```bash
npm install
cp .env.example .env
# isi .env dengan URL dan anon key dari Supabase
npm run dev
```

Buka `http://localhost:5173`.

## 3. Upload ke GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin <URL_REPO_GITHUB_KAMU>
git push -u origin main
```

File `.env` **tidak** akan ikut terupload (sudah ada di `.gitignore`) — ini sengaja, supaya key Supabase kamu tidak bocor ke publik.

## 4. Deploy ke Vercel / Netlify

1. Import repo GitHub ini di Vercel atau Netlify.
2. Di pengaturan project, tambahkan **Environment Variables**:
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_ANON_KEY`
   (nilainya sama seperti di file `.env` lokal kamu)
3. Build command: `npm run build`, output directory: `dist`.
4. Deploy.

## Catatan keamanan

- Policy SQL di atas (`using (true)`) membuat siapa pun yang punya anon key bisa baca/tulis tabel `cablaka_storage` langsung lewat API Supabase. Untuk pemakaian internal kantor ini umumnya masih wajar, tapi untuk keamanan lebih tinggi sebaiknya tambahkan Supabase Auth atau pindahkan logika approve/tolak ke Edge Function.
- Daftar password pegawai di `src/App.jsx` (variabel `USERS`) tersimpan polos (plaintext) dan ikut terbundel ke kode yang bisa dilihat siapa saja lewat "View Source" browser. Pertimbangkan untuk memindahkan validasi login ke server atau memakai hash, terutama karena ini memuat nama asli pegawai.
