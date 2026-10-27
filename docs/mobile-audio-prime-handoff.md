# Mobile Learn startup: handoff for player maintainers

Status (2026-10-27): PR #78 is closed at the mobile developer's request.
This branch is retained for the player team's inspection only. Review,
integration and release belong to that team; nothing has been deployed.

Candidate branch:
https://github.com/androidIkrom/story-player/tree/androidIkrom/fix-audio-prime-lookahead

Candidate code/test commit: `d8f75041aa52830e67f20437bfe4abbf83c48505`.

## Finding

A private Learn story had its first two narration cues at 0 and 167938 ms.
Preparation explicitly primes both and waits for both, but the audio store's
ordinary 60000 ms lookahead rejected the distant outro. Begin waited for the
20000 ms preparation timeout despite the opening media having loaded.

On an Infinix X6880/API 35, the old player took 26.774 seconds from Play to
ready (4.400 seconds native setup, then 22.374 seconds inside the player).
A warm diagnostic reopen waited about 21.5 seconds. These are pre-fix timings;
no new numerical device benchmark is claimed.

## Candidate and evidence

The retained candidate bypasses lookahead only for explicitly primed cues.
Normal playback's 60-second window, exclusion of unprimed narration during
preparation and two-download concurrency remain.

Node 22 validation at the candidate code commit: 1423 unit tests passed;
58 Chromium tests passed and five existing real-bucket dev harness tests were
skipped without their opt-in environment. New store, scheduler and mounted
browser regressions failed on unchanged main and passed with the candidate.
The commit-stamped CDN build and repository verification passed locally.
A separately marked local mobile QA build rendered private Learn playback;
it is not a production artifact. No credentials or signed media are included.

## Maintainer decisions

Confirm the intended opening-prime policy and review the candidate independently.
If accepted, use the player team's integration/release workflow and establish
supported handling for packs already pinned to older immutable builds. Preserve
SHA verification; do not overwrite immutable artifacts or rewrite stamps in
Flutter. This handoff does not request deployment or merge approval.

The mobile fullscreen/library fixes are separate:
https://github.com/FabroLabs/StoryFabroMobile/pull/167

Daily's viewer-bound preview media 404 is a separate backend authorization
finding documented in the mobile repository's `DAILY_PREVIEW_ACCESS_HANDOFF.md`.
