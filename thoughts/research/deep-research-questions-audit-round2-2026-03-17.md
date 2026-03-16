---
type: research
title: "Deep Research Questions: Post-Audit Round 2"
date: 2026-03-17
tags: [deep-research, audit, security, architecture, payments, cinema, supabase, react, telegram]
source: 5-domain code audit (edge functions, frontend, cinema, payments, shared)
relevance: 1.0
tier: active
---

# Deep Research Questions: Post-Audit Round 2

Generated from comprehensive 5-domain code audit of GiftMixer codebase.
60+ findings across Edge Functions, Frontend, Cinema Pipeline, Payments, Shared Utils.

---

## BLOCK A: Security & Payment Architecture (P0)

### Q1. Telegram Bot API webhook HMAC validation
Our payment-webhook validates `x-telegram-bot-api-secret-token` header, but doesn't do HMAC signature verification. What is the correct way to validate Telegram Bot API payment webhooks in 2026? Is the secret token header sufficient, or do we need HMAC-SHA256 on the payload body? Show the exact verification algorithm and any known replay attack mitigations. Compare with Telegram's `checkTelegramAuthorization` for Mini App initData.

### Q2. Atomic lightning balance operations in Supabase/PostgreSQL
We have a race condition: client checks balance, then calls `spend_lightning()` RPC separately. Two concurrent requests can both pass the check before either deduction commits. What is the best pattern for atomic check-and-deduct in Supabase PostgreSQL? Should we use `SELECT ... FOR UPDATE` + single RPC, or advisory locks, or serializable isolation? Show battle-tested SQL for a credits/wallet system that handles concurrent requests safely. Include the idempotency pattern for preventing double-spend.

### Q3. Server-side cost validation for AI generation APIs
Our edge function trusts the client-declared cost (lightning amount). What are best practices for server-side cost calculation in pay-per-generation AI platforms? Should the server independently compute cost from request params (model, resolution, duration) and reject mismatches? How do platforms like Replicate, RunPod, and FAL.ai handle cost pre-authorization? Is there a standard pattern for "hold → generate → settle" (like credit card auth holds)?

### Q4. Rate limiting in Supabase Edge Functions (Deno)
We have no rate limiting on expensive generation endpoints (generate-sticker, recompose-scene, generate-video-async). What are production-grade rate limiting approaches for Supabase Edge Functions? Options: in-memory (lost on cold start), Redis via Upstash, PostgreSQL-based token bucket, Cloudflare Workers integration? Compare approaches by latency overhead, cost, and reliability. Show implementation for per-user rate limiting with configurable tiers (free=5/hour, premium=15/hour, pro=unlimited).

### Q5. Telegram Stars payment flow: webhook reliability and reconciliation
Our Mini App redirects to /payment/success immediately after `openInvoice()` callback returns 'paid', but the payment-webhook may arrive later (or not at all). What is the recommended flow for Telegram Stars payments in Mini Apps? Should the client poll for confirmation? How do you handle webhook delivery failures? What's the reconciliation strategy? Are there Telegram Bot API endpoints to verify payment status server-side?

---

## BLOCK B: Frontend Architecture & React Patterns (P1)

### Q6. Zustand store race conditions with async operations
Our posterStore.ts (1095 lines) has race conditions: rapid Generate/Discard/Generate clicks cause AbortController conflicts. What is the canonical pattern for handling async operations in Zustand stores? Should we use requestId tracking, optimistic updates with rollback, or external state machines (XState)? Show the pattern for safely managing concurrent async actions in a single Zustand store with proper abort/cleanup.

### Q7. Fabric.js memory leak prevention in React
Our EditorCanvas.tsx adds `canvas.on('path:created')` listeners but doesn't remove them on unmount. `wrapper.innerHTML = ''` for cleanup may leak. What is the correct cleanup pattern for Fabric.js canvas in React 18? Show the proper useEffect cleanup that handles: dispose, event listener removal, object references, and interaction with StrictMode double-mount. Are there known memory leak patterns specific to Fabric.js 6.x?

### Q8. React Query vs raw fetch in Zustand — when to use what?
Our app uses React Query for some data (configured with retry logic in App.tsx) but raw fetch in Zustand stores and custom hooks. This creates inconsistency: React Query retries on network failure, raw fetch doesn't. What is the recommended architecture for combining Zustand + React Query in a production React 18 app? When should data live in React Query cache vs Zustand store? How to avoid duplicating fetch logic?

### Q9. TypeScript strict mode migration strategy for 1000+ hook codebase
Our tsconfig has `strict: false`, `noImplicitAny: false`. We have 1052 useEffect/useCallback/useMemo calls and 24 files with `as any`. What is the safest incremental migration path to `strict: true`? Can we enable individual strict flags one at a time (strictNullChecks first)? What tooling exists for automated strict mode migration? How many breaking changes should we expect per 100 files with current React/Zustand patterns?

### Q10. Zustand selector optimization — avoiding unnecessary re-renders
Our stores are accessed with broad `useStore()` without selectors, causing re-renders on any state change. What is the performance impact of missing Zustand selectors in a 30+ route app with 6 stores? Show the auto-selector pattern (zustand/auto) or middleware approach. Are there ESLint rules to enforce selector usage? How does `useShallow()` from zustand/react compare to manual selectors?

---

## BLOCK C: Cinema Pipeline & Video Architecture (P1)

### Q11. FAL.ai video URL expiration and CDN caching strategy
Our generated video URLs from FAL.ai may expire between generation and compilation (user waits, then compiles). What is FAL.ai's URL expiration policy for generated assets (images, videos)? Are URLs permanent or time-limited? If time-limited, what's the TTL? Should we immediately copy generated URLs to our own Supabase Storage? What's the cost/latency trade-off of proxying through our CDN vs direct FAL URLs?

### Q12. FFmpeg video compilation: format normalization best practices
Our compile-cinema-video hardcodes 720p portrait resolution. Users may generate clips with mixed providers (Kling=1080p, WAN=480p, Hailuo=1080p). What's the best FFmpeg filter chain for normalizing mixed-resolution, mixed-fps, mixed-codec video inputs before concat? Should we scale all to lowest common denominator or highest? What are the key FFmpeg flags for smooth cross-resolution concat without artifacts? Show the filter_complex for scaling + padding + fps normalization.

### Q13. Video-to-Video (V2V) implementation on FAL.ai
We have V2V UI (donor video input) but no backend implementation. Which FAL.ai models support video-to-video (motion transfer / style transfer)? Specifically: does Kling 3.0 support V2V? Does WAN 2.6 have a V2V endpoint? What about Hailuo? Show the API payload structure for V2V on FAL.ai with motion extraction from donor video applied to reference image.

### Q14. Lip-sync integration with video compilation pipeline
We have lip-sync via Sync Labs generating per-scene lip-synced video, but it's not connected to the compilation step. What is the correct architecture for integrating lip-sync into a multi-scene video compilation pipeline? Should lip-sync run before or after concat? What happens to audio tracks during FFmpeg concat? How do professional tools handle per-scene lip-sync with scene transitions? Show the FFmpeg command chain for: lip-sync per scene → concat → final audio mix.

### Q15. Provider fallback strategy for video generation
Our sticker pipeline has robust fallback chains (primary → secondary → tertiary), but cinema has zero fallback — single provider failure = user error. What's the recommended fallback architecture for AI video generation where each provider has different prompt formats, aspect ratio support, and pricing? Should we pre-adapt the prompt for multiple providers at generation time, or adapt on-failure? How to handle partial failures (3 of 5 scenes succeed)?

---

## BLOCK D: Supabase & Edge Functions Architecture (P1)

### Q16. Supabase Edge Function error monitoring: Sentry vs alternatives
We have zero error tracking in production — no Sentry, no LogRocket, nothing. What is the recommended error monitoring solution for Supabase Edge Functions (Deno runtime)? Does Sentry's Deno SDK work in Supabase Edge Functions? What about alternatives: Axiom, Baselime, Highlight.io? Show the minimal integration for capturing unhandled errors + custom events in a Supabase Edge Function. How to correlate client-side errors with server-side?

### Q17. Environment variable validation patterns for Deno Edge Functions
6 of our edge functions use `Deno.env.get("KEY")!` (non-null assertion) which crashes if the env var is missing. What is the best practice for environment variable validation in Supabase Edge Functions? Should we validate all at cold start and return 500 early? Is there a Deno equivalent of `envalid` or `zod-env`? Show the pattern for a shared `validateEnv()` utility that fails gracefully with descriptive errors.

### Q18. Supabase Edge Function authentication: JWT vs Telegram initData vs webhook secret
Our 57 edge functions use 4 different auth patterns: JWT (requireAuth), no auth (CORS only), webhook secret, Telegram initData. What is the recommended authentication architecture for a Telegram Mini App backed by Supabase? Should ALL user-facing functions use JWT? How to validate Telegram initData server-side in Deno? Is there a middleware pattern that auto-detects auth type? How does the Supabase team recommend auth for Mini Apps?

### Q19. Distributed rate limiting for Supabase without Redis
Our in-memory rate limits (admin-panel, parse-gift) reset on cold start and don't work across instances. What are production-grade distributed rate limiting options for Supabase Edge Functions WITHOUT Redis? Options: PostgreSQL token bucket table, Supabase Realtime for counter sync, Upstash Redis (external), Cloudflare Rate Limiting. Compare latency, cost, reliability for a Mini App with 10K-100K users.

### Q20. Automatic refund system for failed AI generations
Our refund system is literally `console.warn('Refund not yet implemented')`. What is the correct architecture for automatic refunds in a credits-based AI generation platform? Should refunds be synchronous (immediate on failure) or async (reconciliation job)? How to handle: timeout refunds, partial generation refunds, provider-error refunds? Show the SQL + edge function pattern for atomic spend-then-refund with transaction_id linking.

---

## BLOCK E: Data Integrity & DevOps (P2)

### Q21. PostgreSQL generation_logs: schema design for AI generation analytics
We need a generation_logs table that links to lightning_transactions for full cost audit trail. What is the optimal schema for an AI generation log table that supports: per-provider analytics, cost tracking, fallback chain logging, quality scoring? Show the CREATE TABLE with proper indexes for: provider performance dashboards, daily burn rate queries, per-user cost analysis. Include the foreign key to lightning_transactions.

### Q22. IndexedDB for Telegram Mini App draft state persistence
Telegram WebView can kill our app at any time, losing Cinema draft state. We already use IndexedDB for image cache. What is the best IndexedDB schema for persisting complex draft state (scenes, prompts, selected archetypes, generated clips) in a Telegram Mini App? How to handle versioning and migration? What's the storage limit in Telegram WebView (iOS vs Android)? Show the Dexie.js or idb-keyval pattern for auto-save with debounce.

### Q23. Supabase database migration strategy for live production
We have hardcoded tier limits in SQL migrations. What's the best practice for managing configurable constants (tier limits, pricing, feature flags) in Supabase PostgreSQL for a live production app? Should these be in a `system_config` table, environment variables, or migration-managed? How to update pricing without downtime? Show the pattern for a config table with typed JSON values and cache invalidation.

### Q24. Deno std library version management in Supabase Edge Functions
Our edge functions use `deno.land/std@0.168.0` and `@0.177.0` — mixed and outdated. What is the current recommended Deno std library version for Supabase Edge Functions (as of March 2026)? Has jsr.io replaced deno.land/std? What breaking changes exist between 0.168 and current? Show the migration path for updating all imports across 57 edge functions safely.

### Q25. Cost multiplier SSOT: single source of truth for pricing across client and server
We have cost multipliers defined in both frontend (videoProviders.ts: 3.4x) and backend (generate-video-async: 3.2x) — they don't match. What is the architectural pattern for maintaining a single source of truth for pricing/cost configuration that both React frontend and Supabase Edge Functions consume? Options: shared JSON file, Supabase table with client cache, build-time code generation, API endpoint. Compare approaches for a Telegram Mini App.

---

## BLOCK F: Improvement Zones & Competitive Edge (P2)

### Q26. Kling 3.0 Elements API: maximum identity preservation
We use `elements[0].frontal_image_url` for face-lock but haven't optimized it. What are the best practices for Kling 3.0 Elements API identity preservation? How many reference_image_urls should we provide (1, 2, 4)? Which angles work best (frontal, 3/4, profile)? Does adding more references improve consistency or cause conflicts? What's the optimal cfg_scale for each reference count? Show the API payload for maximum character consistency.

### Q27. WAN 2.6 R2V (Reference-to-Video): character consistency on budget
We're considering WAN R2V as a cheaper alternative to Kling Elements for character consistency. What is WAN 2.6's R2V endpoint on FAL.ai? What parameters does it accept (reference image, prompt, duration)? How does R2V quality compare to Kling Elements for character consistency? What are the limitations (single character only, no multi-shot)? Show side-by-side comparison of R2V vs Elements for anime/chibi characters.

### Q28. Service Worker caching strategy for Telegram Mini App
We have a Workbox service worker caching fonts and runtime assets. But Telegram Mini App documentation says Service Workers are not supported. Is this still true in 2026? Which Telegram client versions support SW? If SW is unreliable, what's the fallback caching strategy (Cache API directly, IndexedDB, HTTP cache headers)? How do other production Telegram Mini Apps handle offline/caching?

### Q29. Sentry integration for hybrid React + Supabase Edge Functions
We need unified error tracking. What is the 2026 state of Sentry SDK for: (a) React 18 with Vite, (b) Supabase Edge Functions (Deno), (c) Telegram Mini App WebView? Can we correlate client errors with server errors via trace ID? How to filter Telegram WebView noise (network interruptions, memory kills) from real bugs? Show the minimal setup for both client and edge function with shared project.

### Q30. Telegram Mini App performance: video playback limits and memory management
Our PreviewPlaylist may render 3+ `<video>` tags simultaneously. We know 2-3 are safe, >3 causes memory pressure on iOS. What are the exact limits for simultaneous video playback in Telegram Mini App WebView (iOS Safari vs Android Chrome)? How to implement IntersectionObserver-based lazy loading/unloading of video elements? Is there a way to detect memory pressure before the app is killed? Show the React pattern for virtualized video list in Telegram Mini App.

---

## Priority for Research

### Tier 1 (Block before next sprint):
Q1, Q2, Q3, Q5, Q11, Q16, Q20

### Tier 2 (Architecture decisions):
Q4, Q6, Q9, Q15, Q18, Q25

### Tier 3 (Optimization & features):
Q12, Q13, Q14, Q21, Q22, Q26, Q27

### Tier 4 (Nice-to-have):
Q7, Q8, Q10, Q17, Q19, Q23, Q24, Q28, Q29, Q30
