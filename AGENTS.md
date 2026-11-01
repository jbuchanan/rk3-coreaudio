# Personal RK3 CoreAudio fork

This is the primary project for Josh's Rig Kontrol 3 audio/MIDI driver work.
Fork: https://github.com/jbuchanan/rk3-coreaudio
Upstream: https://github.com/mihaimetal/rk3-coreaudio

## Branches and provenance

- `josh/combined-audio` is the maintained integration branch and fork default.
- `master` tracks the upstream baseline; do not fold local integration work into it.
- `fix/engine-sync-lifetime` is the focused upstream contribution (PR #2).
- `installed-2026-10-31` tags merge `8a62a49`, the exact source revision installed
  on Josh's Mac: upstream latency PR #1 head `4e775d9` plus the memory fix.
- Documentation commits after that tag do not change the installed binary.

Fetch upstream changes, review the diff and evidence, and integrate deliberately.
Keep fixes in small separate commits. Never assume an upstream PR will merge.
Preserve the installed tag; use new dated tags for subsequent installations.
The prior rk3-userspace repository retains the MIDI-only prototype and migration
history. Its USB-owning MIDI plug-in must not run alongside this audio driver.

## Runtime and validation

The CoreAudio driver owns USB. The per-user MIDI LaunchAgent is explicitly
approved; its endpoints are named RigKontrol 3. Josh's Apollo remains available
for audio. Pedal calibration is CC11 on channel 1, raw 32–424, non-inverted;
preserve the existing user configuration instead of copying another unit's range.

The installed binary and signature were checked, the helper is running, and
CoreAudio initialization was observed. Hardware audio, Logic assignments,
reconnects and sustained memory/latency behavior have not been validated here.
Reported upstream loopback figures are author measurements, not our results.

Run `make test driver midihelper all enginetest iotest` and verify the bundle
with `codesign --verify --strict build/RigKontrol3.driver`. The synchronization
regression tests use AddressSanitizer. Queue timeout cancels work before execution;
already-running work is joined. A wedged executing USB call may still hold the
caller: do not claim a hard execution deadline or universal memory safety.

Do not install or restart audio services merely to build/test. Follow the user's
current authorization for deployment and preserve rollback artifacts.
