# PortfolioSite

Portfolio website presenting Mario's gameplay trailers (looping videos) to companies.
Mario is a senior Unity developer learning web development, backend and cloud with this project.

## How we work
- **Claude gives instructions, Mario writes the code, Claude reviews.** Do not write the site's code
  unless Mario explicitly asks. Give each step as small tasks with a "done when" checklist, explain new
  concepts, and give hints rather than full solutions.
- Relate concepts to Unity where it helps (e.g. IntersectionObserver ≈ OnBecameVisible, CDN ≈ Addressables remote hosting).
- Repo: https://github.com/MarioCaldas/PortfolioSite (branch `main`).

## Video hosting (done)
- AWS account, IAM user `mario` for CLI/console (root user only for billing). Default CLI region `eu-west-1`.
  Do not use `eu-south-2`: it is an opt-in region and returns InvalidClientTokenId.
- Zero-spend budget alert is set.
- Private S3 bucket `mario-caldas-portfolio-videos` (Block Public Access on), served only through
  CloudFront with Origin Access Control: `https://d3bbeeebcy1qqu.cloudfront.net`
- URL pattern: `https://d3bbeeebcy1qqu.cloudfront.net/videos/<Name>.mp4` and `.../<Name>.jpg` (poster)
- Trailers (all portrait, 960×1280, H.264, faststart): CasinoRush, ColorArmy, DrawAndShoot, PetContest,
  ReaperRun, SlimeBreeder, SniperElite, SpinnerTable, SpinningMan, ZRescue (~145 MB total)
- Originals and `process.sh` live in `~/Desktop/PortfolioVideos`. `./process.sh` compresses new trailers
  into `web/`, makes posters, and syncs to S3. Replacing a file with the same name needs a CloudFront
  invalidation or a new file name.
- Verified: 200/206 range requests, cache Hit, direct S3 access 403, HTTP→HTTPS redirect.

## Plan
**Phase 1: frontend (plain HTML/CSS/JS)**
1. Page skeleton + local server (Live Server). `index.html`, `css/style.css`, `js/main.js`; semantic
   header/main/footer; one hard-coded test `<video autoplay muted loop playsinline>`. ← **current step**
2. Responsive dark grid of portrait cards (CSS grid, 4 → 1 columns)
3. Trailer data array in JS (title, file name, description, engine, role) and cards rendered from it.
   Game descriptions/roles still to be provided by Mario.
4. Lazy playback: posters first, `preload="none"` + `data-src`, IntersectionObserver plays visible cards, pauses hidden ones
5. Viewer: click card → large player with sound and controls; Esc / backdrop click closes
6. Publish (second S3 bucket + CloudFront, or GitHub Pages)

**Phase 2: backend.** ASP.NET Core (C#, Mario's strength) `GET /api/videos`, SQLite + EF Core; frontend fetches the list.

**Later:** admin upload to S3 (auth, pre-signed URLs) → private per-company links with view tracking →
custom domain, GitHub Actions deploy, Docker, Terraform (optional AZ-204 path).
