# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with this repository.

## Project Overview

Expense Tracker Mobile is a **native Android app** that is the phone companion of the Shopping Tracker web application. It is an **educational project**, built by a beginner programmer with AI assistance, so code explanations should be clear and educational.

**Goal:** take a photo of a receipt on the phone and upload it directly to Shopping Tracker, without first moving the photo to a computer.

### Terminology
- **The app** / **the mobile app** = this Android app.
- **The backend** = the Shopping Tracker web application (separate project, see below). Unqualified "the application" in the backend's own documentation means its locally installed copy; here always say "the backend" or "the mobile app" explicitly, and name "the production backend" (Azure) when meaning the deployed one.

### Core Workflow (target)
1. The user logs in once; the app receives and stores an API key from the backend.
2. The user opens the app and takes a photo of a receipt.
3. The app shrinks the photo and sends it to the backend with the API key.
4. The backend extracts the items with the LLM and stores the receipt (the app shows the outcome or hands over to the browser if the receipt needs review).

## The Backend (related project)

- Location: `C:\Users\abihun\Projects\ShoppingTracker` (Flask, SQLite, deployed to Azure App Service).
- Production URL: `https://shopping-tracker-app.azurewebsites.net`
- Local URL: `http://localhost:5001` (an Android emulator reaches the host computer at `http://10.0.2.2:5001`, not `localhost`).
- Today the backend only supports browser sessions (email + one-time code login) and a web upload form (`POST /upload`, multipart field `receipt`, JPG/PNG only, up to 15 MB). Extraction takes about a minute and some receipts come back as a draft needing review.
- **Not built yet (backend side):** issuing/revoking API keys, accepting a key (`Authorization: Bearer ...`) on an upload endpoint, a JSON response. These are backend stories and belong in the backend's backlog (`ShoppingTracker/backlog/`, SP-NNN numbering and its `/sdlc-*` skills). Keep the API contract written down in the backend's `Specification/` folder so both projects agree on it.
- Read the backend's `CLAUDE.md` and `Specification/BehaviorSpec.md` before designing anything that touches its behavior.

## Planned Technology Stack (proposal - confirm when setting up)

- **Language / UI**: Kotlin with Jetpack Compose
- **Camera**: CameraX
- **HTTP**: OkHttp / Retrofit
- **Secret storage**: the API key goes in encrypted storage (not plain preferences, never in the source code or logs)
- **Build**: Gradle (Android Studio project)
- **Distribution**: install the APK on the owner's own phone (or Firebase App Distribution); no Play Store planned
- Android only. iOS is out of scope.

## Scope

**In scope (first version):** log in once and store the API key, take a photo, resize it, upload it, show success or failure.

**Out of scope for now:** offline queue and retries, widgets, viewing the receipt history in the app, editing receipts, iOS. (The web application does all of these.)

## Development Guidelines

### For Beginners
- **Explain your code**: add comments explaining non-obvious logic, and the *why*.
- **Use simple patterns first**: do not over-engineer; prefer straightforward solutions.
- **Break down complex tasks** into small, testable pieces.
- **Educational focus**: when suggesting code, explain why it is done that way, and what the Android concepts involved are (Activity, Composable, permission, coroutine ...).
- Start simple, get a working prototype first, then improve.

### Security
- Never commit secrets: API keys, signing keystores, `local.properties`. Keep them in `.gitignore`.
- The backend URL must be `https` for the production build.
- The API key is per user and can be revoked on the backend; the app should handle "key rejected" by asking the user to log in again.

## Project Structure

Not created yet. The Android Studio project will be created in this directory (standard layout: `app/`, `gradle/`, `build.gradle.kts`, `settings.gradle.kts`). Update this section when it exists.

## Common Commands

Not set up yet. To be filled in once the Android project exists (build, install on device/emulator, run tests). Expected: Gradle wrapper commands such as `.\gradlew.bat assembleDebug` and `.\gradlew.bat test`.

## Environment Notes

- Developer machine: Windows 11. Shell commands in this environment use PowerShell (Git Bash also available).
- Android Studio is **not installed** on this machine (checked 2026-11-05). It must be installed (it includes the Android SDK and emulator) before any app code can be built. A USB-connected Android phone can be used instead of, or as well as, the emulator.

## Repository

- Local git repository in this directory, default branch `main`.
- Git identity for this repo (and for the backend repo): `Andrew <andrew.bihun@gmail.com>`, set in each repo's local git config (not global).
- GitHub remote: a **private** repository named `ExpenseTrackerMobile`, created by the owner in the browser (the GitHub CLI `gh` is not installed). Not connected yet - once it exists, add it as `origin` and push `main`.
- `.gitignore` covers Gradle/Android Studio output, `local.properties`, signing keys and `.env`-style secrets, and built APK/AAB files.

## Status

- Project directory, this file and a local git repository (with `.gitignore`) created. No code yet. Nothing committed yet. GitHub remote not connected yet.

## Open Decisions

- Whether this project gets its own backlog and SDLC workflow, or mobile stories are tracked in the backend's backlog (the backend's `/sdlc-*` skills are tied to its repo).
- Exact API contract with the backend (key issuing, upload endpoint, response format, how a receipt needing review is reported).
- Minimum Android version to support.
- Which Android phone to test on, and whether to use the emulator as well.

## Notes for AI Assistance

- This is a **learning project** - prioritize clarity over cleverness.
- The developer is **not experienced** with Android - provide context and explanations.
- Heavy commenting is welcome: explain WHY, not just WHAT.
- Do not change the backend project from here without the user asking; propose backend stories instead.
- Do not run or publish anything outward-facing (store uploads, remote repositories) without explicit confirmation.
