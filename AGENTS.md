# Supervised Coach — Codex Project Instructions

## Project

Supervised Coach is een Next.js App Router applicatie voor wekelijkse AI-microuitdagingen en een workshop-vraagbaak voor MKB-teams die een AI-workshop bij Supervised hebben gevolgd.

## Tech Stack

| Onderdeel | Keuze |
|---|---|
| Framework | Next.js 15 App Router |
| Hosting | Vercel |
| Database + Auth | Supabase met RLS |
| Styling | Tailwind CSS v4 + shadcn/base-ui patronen |
| Email | Resend |
| Runtime AI | Anthropic SDK via `ANTHROPIC_API_KEY` |

## Commands

```bash
npm run dev                 # Development server at http://localhost:3000
npm run build               # Production build
npm run start               # Start production server
npm run lint                # ESLint
npx tsx scripts/seed-super-admin.ts info@supervised.nl <password>
```

## Architecture

- `app/` — App Router routes. `(auth)` bevat login/wachtwoord, `admin` bevat super-admin beheer, `dashboard` bevat de member/admin app, `api/cron` bevat Vercel cron jobs.
- `actions/` — Server Actions. Elke actie moet autorisatie afdwingen via `requireRole(...)`.
- `lib/supabase/` — `server`, `client`, en `middleware` gebruiken de anon key met RLS. `service` gebruikt de service-role key en mag alleen achter een rolcheck.
- `lib/anthropic.ts` — runtime AI client voor hints, challenges, en vraagbaak. Vervang deze provider niet zonder expliciete opdracht.
- `supabase/migrations/` — database schema en RLS policies.

## Authorization

RLS staat op alle tabellen. Organisatie-scoping loopt via `current_org_id()`. Super-admin functionaliteit gebruikt de service-role key alleen server-side en alleen na expliciete rolcontrole.

## Cron

Vercel roept twee endpoints aan, beveiligd met `CRON_SECRET`:

- `/api/cron/challenge-send` — elk uur, verstuurt geplande challenges.
- `/api/cron/challenge-reminders` — dagelijks 08:00, herinnert wie nog niet klaar is.

## Environment Variables

```text
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
ANTHROPIC_API_KEY=
RESEND_API_KEY=
NEXT_PUBLIC_APP_URL=
CRON_SECRET=
```

## Rules

1. Gebruik Server Actions voor mutaties vanuit de web UI.
2. Houd service-role gebruik strikt server-side en achter een rolcheck.
3. Respecteer RLS en organisatie-scoping bij elke databasewijziging.
4. Voer `npm run lint` en waar relevant `npm run build` uit voordat je een taak afrondt.
5. Gebruik `gh` voor repo, branches, pull requests.
6. Gebruik `vercel` voor deployment en environment variables.
