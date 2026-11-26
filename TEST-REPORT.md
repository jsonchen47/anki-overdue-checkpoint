# Test report — version 0.1.0

Tested November 25, 2026 with desktop Anki 25.09.4, its bundled Python 3.13.5, and macOS on Apple silicon.

## Results

**26 automated tests passed**, using Anki's actual Rust scheduler and temporary synthetic collections. Coverage includes:

- The 60/61-day threshold, future cards, disabled mode, and custom delays.
- Complete recall/checkpoint/resume cycles with Hard, Good, and Easy under both FSRS and SM-2.
- Preservation of FSRS memory states, real answer ratings, repetition counts, and lapse counts.
- Again on the first review and Again at confirmation; ordinary relearning replaces the saved interval.
- Atomic undo/redo of the answer, saved state, due date, and review history.
- Saved state surviving collection close/reopen.
- Early reviews keeping the check date and late reviews completing one pending check.
- Manual reschedules, external-client reviews, and invalid markers discarding stale intervals.
- Current deck maximum intervals and native intervals already shorter than seven days.
- Actual rescheduling filtered decks returning cards to their home deck; preview-only decks remaining untouched.
- Preservation of other custom scheduler data, malformed data, the custom-data size limit, and invalid settings.
- Preparing once per scheduling-state object, waiting for asynchronous custom scheduling, preserving another hook's cancellation, and preparing before button labels render.

**Native GUI tests passed under FSRS and SM-2.** Each started a separate Anki application with an isolated profile, no sync credentials, and the built `.ankiaddon` package. Each exercised:

- A qualifying overdue card showing seven days on the actual rendered answer buttons and saving a seven-day due date after Good.
- A pending confirmation restoring the saved 320-day interval.
- A failed confirmation using normal relearning and clearing the saved interval.
- A card exactly 60 days overdue keeping normal scheduling.
- An existing custom scheduling script completing before the add-on applied its policy.
- The settings dialog displaying defaults and saving changed values.
- Anki's native archive installer accepting the package as compatible.

The follow-up delay was simulated by changing only the synthetic collection's creation date and reopening it. The system clock and personal collection were not changed. These tests do not represent a week of real study.

## Artifacts

- `test-results/scheduler-tests.txt`: automated test results.
- `test-results/gui-smoke-sm2.json` and `gui-smoke-fsrs.json`: GUI assertions and rendered button text.
- `test-results/review-checkpoint-sm2.png` and `review-checkpoint-fsrs.png`: review screenshots.
- `test-results/settings-sm2.png` and `settings-fsrs.png`: settings screenshots.

## Remaining limits

Version 0.1.0 is published on AnkiWeb as an early release tested on Anki 25.09.4. Windows, Linux, other Anki releases, mobile clients, a real study session in the user's add-on environment, and combinations with third-party scheduling add-ons are untested. Mobile clients cannot run the rule. Bulk rescheduling can replace checkpoint dates. With FSRS, restoring the saved interval intentionally takes precedence over a shorter new FSRS proposal.

The private method wrapped for rendering is compatibility-sensitive; the manifest limits installation to the tested Anki version. Source, tests, installation instructions, and an AnkiWeb listing draft are included for review before publication.
