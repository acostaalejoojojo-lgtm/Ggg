# Security Specification - Glidrovia

## Data Invariants
1. **User Identity Integration**: A user's UID in the `users` collection must match the document ID.
2. **Resource Ownership**: Documents in `games` and `videos` must have a `creatorUid` field that matches the creator's authentication UID.
3. **Immutability**: `createdAt` fields must be immutable after creation.
4. **Temporal Integrity**: `createdAt` and `updatedAt` must always be validated against `request.time`.
5. **Strict Schema**: All document writes must contain only allowed fields with correct types.

## The Dirty Dozen Payloads (Rejection Targets)
1. **Identity Theft**: Creating a document in `/users/attacker_uid` with `uid: "victim_uid"`.
2. **Admin Spoofing**: Updating a user profile to set `role: "admin"`.
3. **Resource Hijacking**: Creating a game with `creatorUid: "victim_uid"`.
4. **Timestamp Forgery**: Creating a document with a `createdAt` value in the future or past.
5. **Shadow Field Injection**: Adding a field `isVerified: true` to a user profile.
6. **ID Poisoning**: Using a 1MB string as a document ID.
7. **Cross-Collection Orphan**: Creating a game pointing to a non-existent `creatorUid`.
8. **PII Leak**: A signed-in user trying to read the private settings of another user.
9. **Status Fast-Forward**: Updating a game status directly to "deleted" without proper checks.
10. **Array Explosion**: Adding 10,000 items to a `friends` list.
11. **Type Confusion**: Sending a string for the `robux` field (which should be a number).
12. **Blanket Query**: Requesting all user private settings via a list query.

## Test Runner Logic
Verified via `firestore.rules.test.ts` (conceptual) to ensure all above payloads return `PERMISSION_DENIED`.
