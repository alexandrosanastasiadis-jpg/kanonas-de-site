# "tak tik" Product & Technical Plan

## 1. Product Goals and UX Flows
### Vision & Value Proposition
- Deliver a short-form video experience optimized for quick creative expression and curated discovery.
- Emphasize safety, community participation, and transparent curation to differentiate from competitors.
- Support creators with lightweight publishing tools and feedback loops that encourage continuous iteration.

### Target Personas
1. **Creators** – individuals or small teams sharing short, thematic clips.
2. **Curators** – internal or community editors responsible for quality control and discovery.
3. **Viewers** – casual audiences looking for quick, themed entertainment.

### Core UX Flows
#### Startscreen
1. Launch app → splash animation with "tak tik" logotype.
2. Authentication options (email/pass, social login) with prominent CTA.
3. Secondary CTA for browsing curated highlights without login.
4. Safety reminder banner and link to community guidelines.

#### Feed
1. Authenticated user lands on personalized feed with vertical scroll.
2. Each card shows video preview, metadata (creator, category, badges), and engagement actions.
3. Swipe left/right for quick reactions, double-tap to like, press-and-hold for moderation/report options.
4. Sticky header with category filter chips and quick access to curated collections.

#### Upload
1. Tap "+" → record or upload from gallery (15–120 seconds, portrait preferred).
2. Trim editor, sound overlay, captioning (auto-suggest hashtags and categories).
3. Publish screen surfaces visibility options, community guidelines checklist, and scheduled post toggle.
4. Submission triggers background upload with progress, with success screen providing share links.

#### Comments & Interactions
1. Tap comment icon → bottom sheet with threaded comments.
2. Inline moderation controls (report, mute user), AI toxicity flagging prompts.
3. Creator tools for pinning, highlighting "tak tik" editor picks, and enabling creator-to-creator collabs.

## 2. Cross-Platform Frontend Architecture
- **Framework**: React Native with Expo for rapid iteration and shared codebase.
- **Navigation**: React Navigation (stack + tab) handling Startscreen onboarding, Feed, Upload, Notifications/Profile.
- **State Management**: React Query for server sync, Zustand for local UI state.
- **Component Strategy**:
  - `FeedScreen`: FlatList with reusable `VideoCard` component, integrated with expo-av for playback.
  - `PlayerOverlay`: Controls (play/pause, scrubber), metadata, engagement actions.
  - `UploadWorkflow`: Multi-step wizard leveraging `expo-image-picker`, `expo-camera`, and custom transcoding status indicator.
  - `CommentsSheet`: Bottom-sheet component using `@gorhom/bottom-sheet` with real-time updates via WebSockets.
- **Offline & Performance**: Pre-fetch next videos, adaptive bitrate streaming via HLS, caching thumbnails with `react-native-fast-image`.
- **Theming**: Tailwind-style utility classes using NativeWind, consistent typography aligned with "tak tik" brand.

## 3. Backend API (Node.js + Express)
### Service Overview
- Runs on Node 20, Express 5, PostgreSQL via Prisma ORM.
- Authentication via JWT (short-lived access, refresh tokens) with social login providers (OAuth 2.0).
- Media processing orchestrated with AWS Lambda + SQS for asynchronous tasks.

### API Modules & Endpoints
1. **Auth**
   - `POST /auth/register`, `POST /auth/login`, `POST /auth/refresh`, `POST /auth/logout`.
   - Social auth: `POST /auth/oauth/:provider/callback`.
2. **Users & Profiles**
   - `GET /users/:id`, `PATCH /users/:id`, `GET /users/:id/videos`, follow/mute/block actions.
3. **Videos**
   - `POST /videos` (initiate upload → returns signed URL + job id).
   - `POST /videos/:id/complete` (finalize metadata once upload finishes).
   - `GET /videos/feed` (personalized), `GET /videos/trending`, `GET /videos/:id`.
   - `POST /videos/:id/like`, `POST /videos/:id/report`, `DELETE /videos/:id` (creator or moderator only).
4. **Comments & Interactions**
   - `GET /videos/:id/comments`, `POST /videos/:id/comments`, `POST /comments/:id/report`, `POST /comments/:id/moderate`.
   - Real-time updates via Socket.IO namespace `/comments`.
5. **Curation & Discovery**
   - `POST /curation/highlights`, `GET /curation/collections`, `POST /curation/categories`.
   - Manual curation queue endpoints for editors: `/curation/queue` with approve/reject actions.
6. **Analytics & Feedback**
   - `POST /analytics/events`, `GET /analytics/dashboard` (role-based access), `POST /feedback`.

### Data Model Highlights
- `User`, `Video`, `VideoTranscodeJob`, `Comment`, `Report`, `Category`, `Collection`, `Reaction`, `ModerationAction`, `FeedbackEvent`.
- Soft deletion with audit trail, row-level security for user-generated content.

### Security & Compliance
- Rate limiting (Redis), IP/device fingerprinting for abuse detection, GDPR-compliant data retention and deletion flows.

## 4. Cloud Storage, CDN & Transcoding
- **Storage**: AWS S3 with bucket separation (`tak-tik-uploads`, `tak-tik-transcoded`).
- **CDN**: CloudFront distribution serving HLS playlists and thumbnails.
- **Transcoding Pipeline**:
  1. User uploads to pre-signed S3 URL.
  2. S3 event → triggers Lambda → pushes job to AWS MediaConvert (or Elastic Transcoder) with presets (240p–1080p HLS).
  3. Upon completion, MediaConvert notifies via SNS → Lambda updates `VideoTranscodeJob` status and publishes to WebSocket topic.
- **Monitoring**: CloudWatch metrics/alarms, S3 lifecycle policies for storage optimization, checksum validation for uploads.

## 5. Moderation & Community Safety
- **Reporting Workflow**: In-app reports go to moderation queue with SLA timers; severity-based routing (e.g., violence, harassment).
- **Automated Filters**: Integrate AWS Rekognition + Google Cloud Video AI for nudity/violence detection; Perspective API for toxicity scoring on comments.
- **Human Review Tools**: Moderator dashboard with timeline scrubber, annotation, strike system, and ban/appeal flows.
- **Community Safety**: Keyword filters, auto-mute for repeated offenses, safety education prompts before posting.

## 6. Discovery & Curation Mechanisms
- **Manual Curation**: Editor CMS built on top of `/curation` endpoints for feature slots, highlight reels, and seasonal campaigns.
- **Categories & Tags**: Structured taxonomy (primary category + optional tags). Feed ranking blends personalization with category rotation.
- **Algorithmic Signals**: Engagement velocity, completion rate, creator reliability score, manual boosts.
- **Collaborations**: Encourage duets/remixes; highlight curated playlists featuring complementary creators.

## 7. Analytics, Feedback & Iterative Beta
- **Analytics Stack**: Event ingestion via Segment → routed to BigQuery + Looker Studio dashboards. Real-time metrics via Redis Streams.
- **KPIs**: DAU/MAU ratio, average watch time, video completion, report rates, curator approval time, Net Promoter Score.
- **Feedback Channels**: In-app surveys, support chat (Intercom), creator council sessions, public roadmap portal.
- **Beta Program**:
  1. Closed beta with 100 curated creators and 1,000 viewers.
  2. Weekly feedback syncs, instrumentation review, and rapid feature toggles via LaunchDarkly.
  3. Iterate on onboarding, playback performance, moderation response, and discovery balance prior to public launch.

---

### Implementation Roadmap (High Level)
1. Establish branding assets and UX prototypes aligned with flows (Weeks 1–3).
2. Stand up core backend services, storage, and CI/CD pipelines (Weeks 2–6).
3. Develop React Native MVP with feed, player, upload, and comment features (Weeks 3–8).
4. Integrate moderation tools, discovery enhancements, and analytics (Weeks 6–10).
5. Launch beta, gather insights, and iterate toward full release (Weeks 9–12).
