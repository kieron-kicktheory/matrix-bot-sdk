# matrix-bot-sdk (Kick Theory Fork)

Fork of [`@vector-im/matrix-bot-sdk`](https://github.com/element-hq/matrix-bot-sdk) with fixes for E2EE rooms.

## Changes from upstream

### 1. Don't encrypt reactions in E2EE rooms
Reactions (`m.reaction` with `m.annotation` relation type) are now sent as plaintext even in encrypted rooms. This matches the Matrix spec intent — reactions need their `m.relates_to` visible to the server and clients. Encrypting them causes display issues in clients like Cinny.

See: [matrix-spec-proposals#2905](https://github.com/matrix-org/matrix-spec-proposals/issues/2905), [matrix-rust-sdk#470](https://github.com/matrix-org/matrix-rust-sdk/issues/470)

**Changed:** `src/MatrixClient.ts` → `sendEvent()`

### 2. Room message history with decryption
Added `getRoomMessages()` method to `MatrixClient` that fetches paginated room history via `/messages` and automatically decrypts `m.room.encrypted` events using the existing crypto client. Events that can't be decrypted (missing keys) are returned as-is.

**Changed:** `src/MatrixClient.ts` → new `getRoomMessages()` method

### 3. Self-verification stub
Added `requestOwnUserVerification()` to `CryptoClient` as a safe no-op stub. The Rust SDK crypto bindings don't support interactive verification (SAS/QR), but callers (e.g. OpenClaw Matrix plugin) expect this method to exist. Instead of throwing `TypeError: not a function`, it now logs a warning and returns `null`.

**Changed:** `src/e2ee/CryptoClient.ts` → new `requestOwnUserVerification()` method

---

*Original README below:*

[![npm version](https://badge.fury.io/js/@vector-im%2Fmatrix-bot-sdk.svg)](https://www.npmjs.com/package/@vector-im/matrix-bot-sdk)

TypeScript/JavaScript SDK for Matrix bots. For help and support, visit [#matrix-bot-sdk:t2bot.io](https://matrix.to/#/#matrix-bot-sdk:t2bot.io)

# Documentation

Documentation for the project is available [here](https://turt2live.github.io/matrix-bot-sdk/index.html).

# Matrix version support

The Matrix protocol is [versioned](https://spec.matrix.org/latest/#specification-versions) to ensure endpoints and
functionality can safely rotate in and out of the ecosystem. The bot-sdk will assume it is connected to a homeserver 
with support for at least one of the last 2 versions, at the time of the bot-sdk's release. This means that if you 
connect the bot-sdk to a homeserver which is 3 or more Matrix versions out of date, things might not work for you.

It is recommended to update the bot-sdk as frequently as spec releases themselves (or faster) to avoid this situation, 
and watch the repo for updates in the event a release is delayed.

**Note**: Currently the bot-sdk does not throw an error if the server appears to be incompatible, however this might
change in the future.
