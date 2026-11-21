# Studymate — Cambridge O Level Study App

Mobile-first (max-width 460 px) interactive study app for Cambridge O Level students.

## Included Subjects
- **Physics (5054)** — Complete (21 dedicated topic pages, 15 interactive 2D canvas simulations, Paper 3/4 virtual practicals, Roman Urdu help, 35 real-life WebP photos)
- **Mathematics (Syllabus D, 4024)** — Complete (16 dedicated topic pages, 4 interactive 2D canvas Explore tools, Paper 1 quick questions & Paper 2 M1/A1/B1 structured questions)
- **Syllabus outlines ready for expansion:** Computer Science (2210), Business Studies (7115), English Language (1123), Urdu (3248), Islamiyat (2058), Pakistan Studies (2059), and Global Perspectives (2069)

## Deploy to GitHub Pages
1. Create a new GitHub repository and upload all files from this folder (`index.html`, `images/`, `.nojekyll`, `studymate-design-spec.md`, `README.md`).
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, select **Deploy from a branch**, pick `main` and `/ (root)`, and click **Save**.
4. Your app will be live at `https://<your-username>.github.io/<repo-name>/` within a minute.

## Files
- `index.html` — Production GitHub Pages entry point loading optimised `images/*.webp` assets
- `images/` — 35 optimised 4:3 WebP photographs (880×660)
- `studymate-standalone.html` — Single-file offline build with all images embedded
- `studymate-design-spec.md` — Master design & content specification for adding new topics
