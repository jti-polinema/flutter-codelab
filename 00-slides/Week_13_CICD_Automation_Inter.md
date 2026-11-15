---
marp: true
theme: default
paginate: true
size: 16:9
---

<style>
section {
  font-size: 19px;
  padding: 66px 70px 62px 70px;
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
  content: "POLITEKNIK NEGERI MALANG\AJURUSAN TEKNOLOGI INFORMASI";
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

# Week 13
## CI/CD and Automation

**Mobile Programming**

GitHub Actions | Automated Tests | APK/AAB Builds | Safer Releases

> Turn every code change into a consistent, tested, and traceable process.

---

<!-- _class: fit -->

# Learning Objectives

By the end of this session, students should be able to:

1. Explain continuous integration and continuous delivery/deployment.
2. Create a GitHub Actions workflow for Flutter analysis, tests, and builds.
3. Produce APK/AAB files and store them as workflow artifacts.
4. Apply versioning and distinguish public configuration from secrets.
5. Review workflow triggers and permissions before enabling them.

---

<!-- _class: fit -->

# From Commit to Artifact

1. **Change** — a commit or pull request is created.
2. **Check** — dependencies, static analysis, and tests run.
3. **Build** — the app is packaged as an APK or AAB.
4. **Distribute** — the artifact is stored or passed to a release process.

- CI automatically checks code changes.
- CD automates release preparation or delivery.
- A green build is useful, but it does not prove the app is bug-free.

---

<!-- _class: fit -->

# When Does a Workflow Run?

| Trigger | Example use |
|---|---|
| `pull_request` | Check changes before merging |
| `push` to `main` | Validate the main branch |
| Version tag, e.g. `v1.2.0` | Prepare release artifacts |
| Manual (`workflow_dispatch`) | Run a controlled validation |

- Start with only the triggers you need.
- Pull requests from forks do not receive repository secrets.
- Do not grant write access to jobs that only read code.

---

<!-- _class: fit -->

# GitHub Actions Workflow Parts

- **Workflow**: a YAML file under `.github/workflows/`.
- **Event**: the change that starts a workflow.
- **Job**: a unit of work on a runner, such as Linux.
- **Step**: an action or shell command.
- **Artifact**: a build file that can be downloaded after a job.

> Make triggers, permissions, steps, and outputs easy to review.

---

<!-- _class: fit -->

# Flutter Pipeline: Checks

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: subosito/flutter-action@v2
    with:
      channel: stable
  - run: flutter pub get
  - run: dart format --output=none --set-exit-if-changed .
  - run: flutter analyze --fatal-infos
  - run: flutter test --coverage
```

- Run the same checks locally before pushing.
- A failing test must fail the job; do not ignore its exit code.

---

<!-- _class: fit -->

# Flutter Pipeline: Builds and Artifacts

```yaml
  - run: flutter build apk --release
  - run: flutter build appbundle --release
  - uses: actions/upload-artifact@v4
    with:
      name: android-builds
      path: |
        build/app/outputs/flutter-apk/app-release.apk
        build/app/outputs/bundle/release/app-release.aab
      if-no-files-found: error
      retention-days: 7
```

- APKs are convenient for internal testing; AABs are used in Play Store workflows.
- A successful build is not necessarily release-ready: validate signing, configuration, and target devices.

---

<!-- _class: fit -->

# Traceable Versions and Releases

Example version in `pubspec.yaml`:

```yaml
version: 1.2.0+15
```

- `1.2.0` is the semantic version: major, minor, patch.
- `15` is the build number; increment it for each new upload.
- Link a release to its commit/tag, such as `v1.2.0`.
- Keep artifacts with commit notes and test results.

---

<!-- _class: fit -->

# Not All Configuration Is Secret

- A service URL or environment name is usually configuration, not a credential.
- Private tokens, passwords, and keystores belong in GitHub Secrets.
- Never put secrets in source code, logs, commits, or tracked config files.
- `--dart-define` embeds values in the app; do not use it to hide secrets.
- Restrict permissions and artifact retention to what is needed.

> A key shipped to a client app can be extracted. Server secrets must stay on the server.

---

<!-- _class: fit -->

# AI as a Workflow Assistant

AI can draft YAML or explain a failure, but:

- verify every trigger, permission, action, and shell command;
- confirm that a failing test really fails the job;
- make sure sensitive data is not printed or sent to third parties;
- run the workflow and inspect logs and artifacts yourself.

**Challenge:** ask AI to review a workflow, then compare its findings with the actual file and run logs.

---

<!-- _class: fit -->

# Lab and Reflection

1. Add a CI workflow to a Flutter project that already has tests.
2. Run it on a pull request and inspect analysis, tests, and build results.
3. Download the APK/AAB artifact and record the commit that produced it.
4. Note the next version and plan a safe release trigger.

- Which check finds problems earliest?
- Who can access workflow artifacts and secrets?
- Is your build ready to release, or has it only compiled successfully?

---

<!-- _class: fit -->

# References

- GitHub Actions: `docs.github.com/actions`
- Building and testing Flutter: `docs.flutter.dev/deployment/cd`
- Flutter Android deployment: `docs.flutter.dev/deployment/android`
- GitHub encrypted secrets: `docs.github.com/actions/security-guides/using-secrets-in-github-actions`
- Semantic Versioning: `semver.org`

**Remember:** a good pipeline repeats the process consistently and makes results reviewable.
