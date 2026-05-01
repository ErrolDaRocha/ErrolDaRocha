# Hi, I'm Errol

I'm a computer science graduate with a background in machine learning research and full-stack development. These are some recent projects I have been working on.

## Projects

### Job Search Pipeline

Job searching at volume is a data management problem. Job listings are spread across multiple platforms, with many listings irrelevant to your search. The ones worth applying for each require a tailored resume and cover letter if you want to be successful. I built my own aggregate job board to handle this process more efficiently.

#### Scraping and Enrichment

Submitting a set of search preferences from the job board triggers an n8n scraping workflow that pulls matching listings into PostgreSQL. Before anything is displayed on the frontend, each listing passes through two automated enrichment passes. The first compresses the raw job description into a dense technical summary and classifies the role into a resume track. The second scores the listing out of 10 against a personal resume and generates a plain-language explanation of the match. A pending badge at the top of the job list tracks how many listings are still being processed.

#### Board and Pipeline

Each listing is displayed as a card showing the job title, company, source platform, location, experience level, work type, time since posting, and a match score expressed as a percentage. Expanding a card reveals the AI-generated match analysis and description summary, with a toggle to view the full original job description.

The job board can be filtered by match tier (High, Mid, Low), application status (Applied, Ignored), and active job cards with generated documents. Each filter tab shows a live count that updates as new jobs are scored. A debounced search field filters across titles and company names without firing a server request on every keystroke. Job cards can be sorted by posted date, match score, company, title, or stage.

Job cards move through an application pipeline to track progress. Each card can be assigned a stage (applied, interviewing, offer received, rejected, ghosted, or not interested) and updated at any point via a modal on the card itself. For bulk updates, each card has a checkbox which allows multiple cards to be selected. Doing so brings up a bulk action bar that can update stages or request document generation across all selected cards at once.

#### Documents

Per job, you can request a resume, cover letter, or both. An AI model picks the best-fitting LaTeX template from a custom library based on the role and compiles it into a PDF. Documents are requested, tracked, and downloaded directly from the job card, which shows a pending status and updates automatically when processing is completed. The download is served as a ZIP archive through a JWT-secured link that expires after one hour. Batch deduplication prevents the processing workflow from firing more than once when multiple documents are queued at the same time.

#### Real-Time Updates

The board updates without polling. n8n POSTs to an SSE endpoint after each scrape and the client refreshes immediately. The SSE route in Nginx has buffering and caching disabled to keep the connection clean for long-lived streaming.

#### Stats

The stats page gives an overview of job search activity. KPI cards show total jobs scraped, high-match rate, applications sent, active interviews, offers received, and the interview-to-application conversion rate. Four charts cover different areas of the data, which is useful both for tracking progress and catching bugs or anomalous behaviour. One shows daily scraping volume over a 30-day window, a second chart splits jobs by pipeline stage, a third shows where listings came from across job boards, and a fourth maps out how match scores are distributed. A searchable and sortable jobs table is included for reviewing individual listings.

#### Stack

[![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=nextdotjs&logoColor=white)](https://nextjs.org) [![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white)](https://www.postgresql.org) [![n8n](https://img.shields.io/badge/n8n-EA4B71?style=flat-square&logo=n8n&logoColor=white)](https://n8n.io) [![Recharts](https://img.shields.io/badge/Recharts-22B5BF?style=flat-square&logo=recharts&logoColor=white)](https://recharts.org) [![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)](https://www.docker.com) [![Nginx](https://img.shields.io/badge/Nginx-009639?style=flat-square&logo=nginx&logoColor=white)](https://nginx.org)

### Social Media Archive

A self-hosted interface around [yt-dlp](https://github.com/yt-dlp/yt-dlp) for building and curating a local archive of social media posts. The UI is styled to match the source platforms of the content, making the archive easier to browse and search than a standard file explorer.

#### Why

Platforms delete content constantly. Accounts get suspended, posts get removed, and services shut down. Automated moderation flags content in bulk and rarely reverses decisions. Political pressure, advertiser complaints, and legal risk all shape what stays up. None of it is designed with preservation in mind.

The historical and journalistic record increasingly lives on social media: eyewitness footage, firsthand accounts, community organizing, cultural artifacts. That record sits on private servers as a byproduct of engagement-driven businesses, and it disappears when keeping it up becomes inconvenient. If you do not have a copy, the record could be lost.

#### How It Works

Posts are downloaded via yt-dlp and remuxed by ffmpeg to embed corrected metadata as container tags without re-encoding. At ingest, Chromaprint generates an audio fingerprint for each post. Deduplication runs in two passes: an exact match on the compressed fingerprint string, then a fuzzy bit-error-rate comparison on raw integer arrays at 70% similarity. Posts sharing the same audio track are grouped together even when metadata is missing or inconsistent, which is common with reposts and re-uploads.

#### Stack

[![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=nextdotjs&logoColor=white)](https://nextjs.org) [![Express](https://img.shields.io/badge/Express-000000?style=flat-square&logo=express&logoColor=white)](https://expressjs.com) [![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=nodedotjs&logoColor=white)](https://nodejs.org) [![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org) [![yt-dlp](https://img.shields.io/badge/yt--dlp-FF0000?style=flat-square&logo=github&logoColor=white)](https://github.com/yt-dlp/yt-dlp) [![ffmpeg](https://img.shields.io/badge/ffmpeg-007808?style=flat-square&logo=ffmpeg&logoColor=white)](https://ffmpeg.org) [![Chromaprint](https://img.shields.io/badge/Chromaprint-555555?style=flat-square&logo=github&logoColor=white)](https://github.com/acoustid/chromaprint) [![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white)](https://www.postgresql.org) [![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)](https://www.docker.com) [![Nginx](https://img.shields.io/badge/Nginx-009639?style=flat-square&logo=nginx&logoColor=white)](https://nginx.org)

## Infrastructure

Everything runs on a home server managed through TrueNAS SCALE. Alongside the projects listed here, the server also hosts several other open source applications.

The server was originally running apps through TrueNAS SCALE's Kubernetes-based deployment system. When that was deprecated in favour of Docker, I migrated everything to a full Docker deployment within TrueNAS.

Remote access is handled through a VPS running Pangolin, with Newt on the home server establishing an outbound tunnel. This exposes services externally without opening ports on the home network.

### Hardware

<details>
<summary>Details</summary>

<table>
  <tr><td>Motherboard</td><td>Supermicro X11SSH-F</td></tr>
  <tr><td>CPU</td><td>Intel Core i3-7100 @ 3.90 GHz (dual-core, HT, VT-x)</td></tr>
  <tr><td>RAM</td><td>32 GB DDR4 ECC @ 2400 MT/s</td></tr>
  <tr><td>Boot</td><td>2 x 120 GB SATA SSD</td></tr>
  <tr><td>Primary Storage</td><td>8 x 8 TB WD Red HDD, RAID-Z2</td></tr>
  <tr><td>App Storage</td><td>2 x 500 GB Crucial SATA SSD</td></tr>
  <tr><td>Cache</td><td>500 GB Kingston NVMe (SLOG/ZIL, L2ARC)</td></tr>
  <tr><td>HBA</td><td>LSI SAS2008 (Broadcom Fusion-MPT SAS-2)</td></tr>
  <tr><td>NICs</td><td>2.5 GbE Realtek RTL8125, 2 x 1 GbE Intel I210</td></tr>
</table>

</details>

### Software

<details>
<summary>Details</summary>

<table>
  <tr><td>OS</td><td>TrueNAS SCALE</td></tr>
  <tr><td>Containers</td><td>Docker</td></tr>
  <tr><td>Tunneling</td><td>Pangolin + Newt</td></tr>
  <tr><td>Automation</td><td>n8n</td></tr>
  <tr><td>Git</td><td>Forgejo</td></tr>
</table>

</details>

## About the Repositories

Most of my active work is tracked through a self-hosted [Forgejo](https://forgejo.org) instance. Repos here are private mirrors, so the contribution graph reflects real work. These apps were built for personal use and haven't been set up for anyone else to run. I may open them up as I develop them further.

## Contact

[LinkedIn](https://www.linkedin.com/in/errol-darocha/)
