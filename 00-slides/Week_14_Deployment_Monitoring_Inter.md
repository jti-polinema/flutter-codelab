---
marp: true
theme: default
paginate: true
size: 16:9
---

<style>
section {
  font-size: 19px;
  padding: 66px 70px 82px 70px;
  color: #1b1b1b;
  background:
    linear-gradient(90deg, #f7941d 0%, #fdb913 100%) 0 0 / 100% 10px no-repeat,
    linear-gradient(155deg, #ffffff 55%, #eef0f2 100%);
  background-repeat: no-repeat;
}
section h1 { font-size: 32px; color: #111; margin: 0.3em 0; border-bottom: none; }
section h2 { font-size: 25px; color: #333; margin: 0.3em 0; }
section h3 { font-size: 20px; color: #1b2a4a; margin: 0.25em 0; }
section p, section ul, section ol { margin: 0.25em 0; }
section ul, section ol { padding-left: 1.25em; }
section li { margin: 0.16em 0; }
section table { font-size: 16px; }
section pre, section code { font-size: 14px; }
section pre { margin: 0.25em 0; line-height: 1.2; }
section blockquote { font-size: 17px; }
section::before {
  content: "POLITEKNIK NEGERI MALANG\A Department of Information Technology";
  position: absolute;
  top: 26px;
  right: 36px;
  width: 360px;
  text-align: right;
  padding-right: 92px;
  white-space: pre;
  font-size: 12px;
  font-weight: 600;
  line-height: 1.45;
  color: #1b2a4a;
  background: url('jti-polinema-logo.png') no-repeat right center / 96px 48px;
  z-index: 10;
}
section::after {
  content: "D4 Teknik Informatika  ·  " counter(page);
  position: absolute;
  left: 0; right: 0; bottom: 0;
  height: 30px;
  padding-left: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #ffffff;
  font-size: 13px;
  background:
    url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="200" height="30"><text x="100" y="20" text-anchor="middle" font-family="Arial" font-size="13" font-weight="bold" fill="%231b2a4a">jti.polinema.ac.id</text></svg>') no-repeat left top / 200px 30px,
    linear-gradient(to right, #f7941d 0 200px, #1b2a4a 200px 100%);
}
section.lead { justify-content: center; text-align: left; padding-left: 80px; }
section.lead h1 { font-size: 44px; font-weight: 800; color: #111; }
section.lead h2 { font-size: 30px; font-weight: 600; color: #666; margin-top: 0; }
section.lead h3 { font-size: 18px; font-weight: 500; color: #222; margin-top: 16px; }
section.lead p { color: #333; }
section.fit { display: flex; flex-direction: column; justify-content: center; }
section.fit h1 { font-size: 28px; margin: 0.2em 0; }
section.fit h2 { font-size: 23px; margin: 0.2em 0; }
section.fit h3 { font-size: 19px; margin: 0.2em 0; }
section.fit p, section.fit ul, section.fit ol { margin: 0.2em 0; }
section.fit li { margin: 0.12em 0; }
section.fit pre { margin: 0.2em 0; line-height: 1.15; }
section.fit pre, section.fit code { font-size: 12px; }
section.fit blockquote { font-size: 16px; margin: 0.2em 0; }
section.fit table { font-size: 15px; }
</style>

<!-- _class: lead -->

# Week 14
## Deployment & Monitoring

**Mobile Programming**

Google Play Internal Testing | Firebase Crashlytics | Analytics

> A beta release that can be tested, observed, and improved.

---

<!-- _class: fit -->

# Learning Objectives

By the end of this session, students should be able to:

1. Explain the path from a release candidate to beta testing.
2. Prepare an app version and AAB for internal testing.
3. Send crash reports and analytics events responsibly.
4. Read release signals and decide what to do next.
5. Use AI to help analyze logs without exposing sensitive data.

---

<!-- _class: fit -->

# The Mobile Release Cycle

```text
Change → CI passes → release candidate (AAB)
       → internal track → tester validation
       → monitor crashes & performance
       → fix / continue the release
```

- Week 13 produced a build; this week distributes it to testers and monitors it.
- A beta is a limited release, not proof that an app is ready for everyone.
- Record the version, build number, commit, and checks performed.

---

<!-- _class: fit -->

# Release Tracks: Start with Internal

| Stage | Audience | Purpose |
|---|---|---|
| Internal testing | Small team / invited testers | Check installation and key flows |
| Closed testing | Limited group | Gather structured beta feedback |
| Production | General users | Distribute after review |

- Start with the internal track and testers who have been invited.
- Play feature availability, account requirements, and review processes can change.
- Do not upload production data or accounts that are unnecessary for testing.

---

<!-- _class: fit -->

# Build Identity: Version and Commit

```yaml
# pubspec.yaml
version: 1.0.0+21
```

- `1.0.0` is the user-facing version; `21` is the build number.
- Increment the build number for every new upload.
- Link a release to its tag/commit, e.g. `v1.0.0-beta.1`.
- Release notes should describe changes and known limitations.

> A version without a commit and test notes is difficult to trace during an incident.

---

<!-- _class: fit -->

# Build a Release Candidate

```bash
flutter pub get
dart format --output=none --set-exit-if-changed .
flutter analyze --fatal-infos
flutter test
flutter build appbundle --release
```

- Use the correct release configuration and signing setup for the project.
- The AAB is usually created under `build/app/outputs/bundle/release/`.
- Keep the artifact with its commit and CI results; do not share it publicly.
- Check Flutter and Play Console signing guidance for current setup details.

---

<!-- _class: fit -->

# Pre-Upload Gate

- [ ] Tests and analysis pass on the candidate commit.
- [ ] `version` and build number are correct and not reused.
- [ ] App name, icon, permissions, and environment configuration are reviewed.
- [ ] No credentials, sensitive test data, or excessive debug logs are included.
- [ ] Key flows have been checked on target devices.
- [ ] Release notes and a way to report problems are available.

> A release build still needs human review.

---

<!-- _class: fit -->

# Lab: Internal Testing in Play Console

1. Create/select the app and prepare an **Internal testing** track.
2. Add testers through an allowed email list or group.
3. Upload the candidate AAB, add release notes, and review warnings.
4. Share the opt-in link with registered testers.
5. Ask testers to install and check key flows.
6. Record the version/build, device, reproduction steps, and result.

If Play Console access is unavailable, distribute a private test build with Firebase App Distribution.

---

<!-- _class: fit -->

# From Feedback to Action

| Report | Evidence to record | First action |
|---|---|---|
| Crash on screen open | Version, device, steps, stack trace | Reproduce and prioritize |
| Installation fails | Track, Android version, error | Check tester, artifact, compatibility |
| Feature does not work | Steps, expected/actual result | File an issue and add a test |

- Avoid requesting screenshots that contain personal data.
- Separate reproducible problems from assumptions.
- Do not dismiss a report just because the build succeeded.

---

<!-- _class: fit -->

# Observability: What Do We Collect?

| Signal | Example | Question |
|---|---|---|
| Crash reporting | Exception, stack trace, version | Where does the app fail? |
| Analytics | `beta_feedback_sent` event | Is the feature flow being used? |
| Logging | Limited technical messages | What happened before the error? |

- Collect only what is needed; inform users as required by the privacy policy.
- Never log tokens, passwords, message contents, or personal identifiers.
- Analytics events do not replace crash reporting or tests.

---

<!-- _class: fit -->

# Crashlytics: Capture Flutter Errors

Add the packages and follow Firebase setup for the app platform:

```bash
flutter pub add firebase_crashlytics firebase_analytics
```

After `Firebase.initializeApp()`:

```dart
import 'dart:ui';

FlutterError.onError =
    FirebaseCrashlytics.instance.recordFlutterFatalError;
PlatformDispatcher.instance.onError = (error, stack) {
  FirebaseCrashlytics.instance.recordError(
    error, stack, fatal: true,
  );
  return true;
};
```

Test only on a test build/device; confirm the event appears in the dashboard.

---

<!-- _class: fit -->

# Analytics: Meaningful Events

```dart
await FirebaseAnalytics.instance.logEvent(
  name: 'beta_feedback_sent',
  parameters: {'screen_name': 'home'},
);
```

- Choose clear event names and keep the event set manageable.
- Record an action, not form contents or user identity.
- Document parameter meanings so the team interprets data consistently.
- Respect consent, privacy settings, and institutional policy.

> Data that is not needed should not be collected.

---

<!-- _class: fit -->

# Safely Verify Telemetry

1. Run the test app and trigger a documented, non-sensitive event.
2. Send one synthetic non-fatal error on a test build.
3. For a fatal crash, follow Crashlytics test instructions only on a test device.
4. Wait for processing, then check the event, version, and stack trace.
5. Confirm the report contains no personal or secret data.

- Dashboards may take time to receive and process data.
- Do not create synthetic crashes on a release used by real users.

---

<!-- _class: fit -->

# Read the Release Dashboard

Compare beta versions/builds and look for:

- crash-free users / sessions and change from the previous build;
- new issues, affected users, and repeated stack traces;
- related devices/OS versions and reproduction steps;
- key-flow events that indicate the feature can be used;
- tester reports that match the time and version of an incident.

> Aggregate numbers are signals; reproduction and context help determine the cause.

---

<!-- _class: fit -->

# Triage and Release Decisions

```text
Find issue → check version & impact → reproduce
          → fix + test → new build
          → retest → continue / hold release
```

- Prioritize crashes that block key flows or affect many users.
- Record the decision, evidence, owner, and fix version.
- If there is a risk, stop expanding distribution and follow the track stop/rollback process.
- Do not dismiss reports or ship another build without understanding the impact.

---

<!-- _class: fit -->

# AI as a Log-Analysis Assistant

AI can help group stack traces or suggest hypotheses.

Before sharing:

1. Remove tokens, emails, user IDs, private URLs, and sensitive payloads.
2. Provide only the minimum technical context, app version, and sanitized error.
3. Ask for multiple hypotheses, supporting evidence, and reproduction steps.
4. Verify each hypothesis against code, tests, and a device.

> AI does not determine release risk and does not replace human review.

---

<!-- _class: fit -->

# Challenge: Analyze a Beta Incident

Release `1.0.0+21` shows a new crash on the detail page:

```text
type 'Null' is not a subtype of type 'String'
DetailPage.build → Product.fromJson
```

Discuss:

- What information is needed before concluding the root cause?
- How would you sanitize logs before asking AI for help?
- What test would prevent a regression?
- Should the track be held? Explain using evidence.

---

<!-- _class: fit -->

# Week 14 Assignment

In your Flutter project repository:

1. Prepare a beta candidate with a recorded version/build number and commit.
2. Build a release AAB and keep evidence of checks/tests.
3. Distribute through Internal testing or Firebase App Distribution.
4. Configure Crashlytics and one non-sensitive Analytics event.
5. Test telemetry on a test build and record dashboard results.
6. Write a release summary: issue, impact, decision, and next steps.

If a paid service/account is unavailable, document an alternative demonstration and its limitations.

---

<!-- _class: fit -->

# Evidence and Reflection

README/`docs/release-report.md` should include:

- version, build number, commit/tag, artifact format, and distribution track;
- test results, devices/OS, and tester validation steps;
- a sanitized dashboard screenshot;
- one finding/incident and the follow-up decision;
- AI used, sanitized context, and manual verification.

Reflection: What evidence would make you hold or continue a release?

---

<!-- _class: fit -->

# Key Principles

## Release
> Start with a small group; link the build to its commit and test evidence.

## Monitoring
> Collect the minimum data needed to find problems.

## Decisions
> Triage using impact, reproduction, and version context.

## AI
> Sanitize logs, verify hypotheses, and own the decision.

---

<!-- _class: fit -->

# References

- [Flutter: continuous delivery](https://docs.flutter.dev/deployment/cd)
- [Flutter: Android release](https://docs.flutter.dev/deployment/android)
- [Google Play: internal testing](https://support.google.com/googleplay/android-developer/answer/9845334)
- [Firebase Crashlytics for Flutter](https://firebase.google.com/docs/crashlytics/flutter/get-started)
- [Firebase Analytics for Flutter](https://firebase.google.com/docs/analytics/get-started?platform=flutter)
- [Firebase App Distribution](https://firebase.google.com/docs/app-distribution)

---

<!-- _class: lead -->

# Build → Distribute → Observe

## A beta release is not the finish line.

Collect evidence, understand the impact, fix it, and release deliberately.
