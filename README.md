MindSense AI
A privacy-conscious AI companion for emotional reflection and everyday well-being.

MindSense AI is a personal project exploring how technology might give people a supportive place to pause, put their feelings into words, and reflect on their day—without presenting an AI system as a therapist or asking people to give up control of their private thoughts.
Why MindSense AI?
Life can move quickly. Stress, loneliness, and emotional overwhelm do not always arrive at a moment when someone knows whom to call or how to explain what they are feeling. Sometimes, the first step is simply finding a little space to think clearly and put the experience into words.

Mental health is a major public-health concern. The World Health Organization estimates that 5.2% of adults worldwide experienced depression in 2023, and reports that more than 720,000 people die by suicide each year. These figures describe a much larger challenge than any single app can solve. They are a reminder that support, prevention, and access to qualified care matter. (WHO: Depression · WHO: Suicide)

My own experience with mental illness inspired me to explore how technology might make reflection and everyday emotional support easier to access. I started building MindSense AI around a simple idea: a person should be able to reflect without feeling that every private thought has to become permanent data.

I think of MindSense as a private notebook with a thoughtful conversation partner beside it—not an all-knowing therapist. The notebook helps someone record and revisit their own experience; the companion can listen, reflect, and offer a small optional next step. It should also be clear about what it cannot do and when human support is more appropriate.
What is MindSense AI?
MindSense AI is a non-clinical AI companion for emotional reflection. It brings together mood check-ins, private journaling, reflective conversations with Mindy, weekly insights, reminders, and a small set of optional well-being exercises.

The aim is not to diagnose a person, label their emotions as medical facts, or replace their relationships and support network. It is to make a few useful actions—checking in with yourself, writing something down, noticing patterns, or trying a simple exercise—feel easier to start.

MindSense is not a medical device or therapy service, and it is not designed for emergencies. It does not diagnose or treat mental health conditions and cannot replace qualified professionals or emergency services.
What the product is designed to do
- Make reflection easier. Record a mood check-in or journal entry and revisit personal patterns over time.
- Offer a supportive conversation. Mindy is designed for brief, reflective exchanges rather than authoritative or clinical advice.
- Suggest small, optional actions. A bounded exercise library offers simple well-being activities without presenting them as prescriptions.
- Treat privacy as a design constraint. Journal entries, check-ins, and local conversation history are stored in an encrypted local vault. Messages sent to Mindy are transmitted to the configured backend and model to generate a reply; the service is designed not to retain or log transcript text. That design and its deployment settings still need to be verified before public release.
- Put safety checks before fluent answers. Deterministic screening can route certain high-confidence messages to fixed support wording before normal generation. An output guard and fallback path add another check, but they cannot guarantee that every crisis will be recognised.
- Keep the user in control. Personal insights are intended to support reflection—not to define the user's identity, diagnose a condition, or make decisions for them.
Research vision
MindSense AI is also an evolving research and engineering project. I want to explore what responsible emotional-support software might look like when privacy, uncertainty, and safety are considered from the beginning rather than added at the end.

The project currently explores several questions:
1. Safety and conversational AI: Can a deterministic screening layer handle clear, high-confidence cases before a generative model responds, while reducing avoidable false alarms?
2. Language and context: How do negation, indirect expressions, slang, transliteration, and code-switching affect the detection of distress—particularly across English, Hindi, Bengali, and Hinglish?
3. Privacy-conscious personalization: How much useful reflection can be supported with local encrypted records and minimal server-side retention, rather than keeping a long-term archive of private conversations?
4. Bounded recommendations: Can a curated set of well-being exercises make suggestions more predictable and easier to review than unconstrained generated advice?
5. Evaluation instead of assumptions: How should an emotional-support system be assessed for missed warning signs, false positives, response quality, fallback behavior, latency, and user understanding?
These are questions to investigate, not results I am claiming to have proven. The current safety patterns are an engineering baseline, not a clinically validated crisis classifier. Mood estimates are exploratory signals, not calibrated measures of a person's mental state. The next research step is independent evaluation with held-out examples, native-speaker review, and qualified mental-health review before public launch.
How it works
Android app
  ├─ Mood check-ins, journal, conversation UI, insights and reminders
  ├─ Encrypted local vault for personal records
  └─ Safety screening on the client
          │
          │ HTTPS request for a Mindy reply
          ▼
FastAPI backend
  ├─ Authentication and request validation
  ├─ Deterministic safety gate
  ├─ Companion orchestration and optional exercise selection
  ├─ Configured language-model service
  └─ Output audit and safe fallback
The app is built with TypeScript, Vite, and Capacitor for Android. The service layer uses Python and FastAPI. Firebase Authentication supports account flows. The language-model service is configured separately from the mobile app, so the deployed model and its performance must be evaluated in the actual release environment.
A few engineering choices
Safety before generation. Clear, high-confidence safety matches should not depend only on a language model interpreting the message correctly. The deterministic layer provides a predictable route for recognised patterns; context can still be missed, so the system needs broader testing and human review.

Local-first personal records. The journal, check-ins, and local conversation history use AES-256-GCM encryption in the app's vault, with Android secure-storage integration for key material. This reduces the amount of sensitive content the service needs to retain, but it does not mean every part of the system is “zero knowledge”: a backend or model host can process a message while generating a response.

Curated exercises. The exercise registry keeps suggestions within a known set of activities. The app treats them as optional well-being ideas, not clinical interventions.

One safety vocabulary, two runtimes. The project includes tooling to generate shared safety patterns for the Python backend and TypeScript client, making it easier to keep their behavior aligned and test for regressions.
Current implementation status
The current source bundle includes:
- An Android-focused TypeScript/Capacitor app with authentication flows, mood check-ins, journal, chat with Mindy, insights, reminders, profile, and account-deletion flow.
- A FastAPI backend with companion orchestration, safety screening, consent/data operations, and API hardening.
- An encrypted local vault and privacy-related flows.
- A curated exercise registry and preference feedback for recommendations.
- Shared safety patterns, an evaluation harness, backend/mobile tests, Docker deployment assets, backup scripts, and draft public privacy/account-deletion pages.
The production runbook records 79 mobile tests and 21 backend tests passing when it was prepared. These are project-reported test results; they are not evidence of clinical safety or complete end-to-end readiness.

Release status: pre-release. The Android project has been generated and hardened, but the runbook says the signed release bundle still needs to be built and verified on the developer's machine. Real-device testing, evaluation against the intended live model, load testing, an independent safety review, native-speaker review of localized crisis phrases, and completion of Play Console requirements remain before public launch.
Mobile app
Requirements: Node.js 20+, JDK 21, Android Studio, and Android SDK Platform 36 for the current Android target.
cd app
cp .env.example .env
# Edit .env with your API origin and Firebase web-app configuration.
# Never commit .env or service-account credentials.
npm ci
npm test
npm run android:sync
npm run android:open
On Windows PowerShell, use Copy-Item .env.example .env to create the environment file. After syncing, open the Android project in Android Studio and test it on a physical device. The release command and signing steps are documented in the runbook.
Backend tests
Use the Python version and dependencies specified by the project, then run:
pip install -r requirements-api.txt pytest
pytest -q
A working backend also requires environment configuration and Firebase credentials; do not expose an unconfigured development server to the public internet. Follow the runbook before deployment.
Evaluation and responsible release
Before release, the project needs more than passing unit tests. The planned validation includes:
- A held-out crisis-safety set reviewed by qualified professionals and native speakers, including English, Hindi, Bengali, and Hinglish.
- Analysis of false negatives and false positives across context, negation, quotations, slang, and indirect language.
- Evaluation of the actual deployed model for response quality, fallback frequency, and p50/p95 latency.
- Real-device tests for authentication, encryption, deletion, reminders, offline behavior, accessibility, and recovery expectations.
- Dependency and secret scans, a backup restoration drill, and verification of privacy and account-deletion pages.
- Accurate Google Play health-app, Data safety, content-rating, privacy, and account-deletion declarations.
Do not interpret an initial hand-written test set as proof that the system detects every crisis. Safety performance must be reported with the dataset, test method, limitations, and failure cases made clear.
Known limitations
- Crisis screening relies partly on deterministic language patterns and can miss indirect, ambiguous, or multilingual expressions, or flag benign messages incorrectly.
- Emotion and mood estimates are exploratory and must not be presented as diagnoses or validated clinical measurements.
- The real-model evaluation and load test remain to be completed against the production configuration.
- A backend or model host can process the text sent for response generation, even if transcript logging is disabled.
- Local-first storage means a lost device may also mean lost journal data unless a tested encrypted export/recovery feature is added.
- Voice chat, iOS support, and the in-app interface for managing remembered preferences are not part of the verified current release.
Contributing
Contributions that improve accessibility, privacy, testing, language coverage, safety evaluation, or deployment reproducibility are welcome. For safety-sensitive changes, include the intended behavior, reviewed examples, and regression tests. Please do not submit identifiable journal entries, private chat logs, or data without appropriate permission.
License
A license has not yet been selected. Until a LICENSE file is added, do not assume the project is open for reuse. Before choosing one, review the licensing terms for dependencies, model weights, and datasets.
Built by Esha Banerjee · Contact

MindSense AI is an independent, non-clinical software project. It is not a substitute for professional mental-health care and is not for emergencies. In India, call 112 for emergency assistance; Tele-MANAS can be reached at 14416. Re-check helpline details before
