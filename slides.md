---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: "#061718"
# some information about your slides (markdown enabled)
title: Honeycomb Technical Presentation
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# open graph
seoMeta:
  # By default, Slidev will use ./og-image.png if it exists,
  # or generate one from the first slide if not found.
  ogImage: auto
  # ogImage: https://cover.sli.dev
---

# Honeycomb Presentation

Presentation of past projects and how they map to Honeycombs App Requirements

<div @click="$slidev.nav.next" class="mt-12 py-1" hover:bg="white op-10">
  Get Started <carbon:arrow-right />
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

# Case Study — Hooman Dashboard

Realtime updates, authorization workflow and notifications system

<img src="/hooman-dashboard.png">

---

## Realtime Sync Engine

- **Goal**: Fresh data for everyone instantly; resilient to concurrent edits.
- **Optimistic UI**; server as source of truth (idempotent mutations).
- **Offline reads and Writes**:
  - Storing data locally
  - Running mutations when going back online
  - Ideal for mobile app usage.
- **Good DX for fast iteration:**:

```ts
const data = useQuery(z.query.task.where("assigned_id", "=", "user123").one());
```

--

### Honeycomb Benefit

- Makes sure all online users have the latest data
- Accurate pricing calculations

---

## Authorization Model

- **Goal**: Making sure the authorization model is applied consistently.
- **Logic-based authorization** in code (role + scope), no DB row policies.
- **All data mutations** go through validation (input + auth).
- **Two-level tenancy**: `workspaceId` → `clientId` (subset of workspace).

--

### Honeycomb Benefit

- Will use a similar logic based pattern to determine Platform Admin, Regional Manager, Account Manager, Grader, Seller and client/region scopes.

---

## Notification System

- **Pluggable Channels** A good usecase for dependency injection
- **Should you get notified?** Managable logic.
- **Auditing**: every send logged with correlation IDs (link to entity/version).

---

```ts
export async function notifyAll(
  tx: Transaction<typeof schema>,
  notification: Omit<Notification, "id" | "created_at">,
  postCommitTasks: PostCommitTask[],
  channels: NotificationChannel[] = [databaseNotificationChannel(tx)]
) {
  await Promise.all(
    channels.map((channel) => channel(notification, postCommitTasks))
  );
}

// Example channel
export function databaseNotificationChannel(
  tx: Transaction<typeof schema>
): NotificationChannel {
  return async (notification) => {
    await tx.mutate.notification.insert({
      ...notification,
      id: nanoid(),
      created_at: Date.now(),
    });
  };
}
```

---

# Our View on Mobile Application Development

- **Use the platform** Swift/Kotlin, React Native or Flutter?
- **Write Once** Use everywhere (we like that!)
- **Large Ecosystem** [Expo Packages](https://github.com/expo/expo/tree/main/packages)
- **Can still use native code**

---

# The Honeycomb Exchange Application

---

<img src="/search-flow.png" class="w-[80%]">

---

<img src="/transaction-flow.png" class="w-[80%]">

---

## Secure Margin Calculation Engine

- Server-side, centralized margins in DB
- Logic-based permissions; endpoint input validation
- Indexed tables; cache/memoize hot paths

---

## Low-Quality Networks

- Optimistic updates; sync on reconnect
- Custom conflict resolution for carts/crates
- Clear user feedback to reapply/fix when invalid

---

## Auditable Flows

- Append-only records with timestamps + correlation IDs
- Track: crate deltas, chat archives, cart history

---

## Closing Impact

- Reliable UX
- Secure, validated pricing
- Transparent, auditable history
- Scalable architecture aligned to the BRD

---
