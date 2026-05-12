# React Native Interview Prep 2026

Modern React Native interview questions for people who want to think like production engineers, not memorize outdated trivia.

Last reviewed: **2026-05-12**  
Baseline: **React Native 0.85 stable**, New Architecture-only era, Hermes V1 default, React 19.2.x, modern React Native DevTools.

> This guide intentionally avoids legacy bridge-era interview filler. Legacy details appear only when they help explain migrations, compatibility, or modern architecture decisions.

## Navigation

- [How to Use This Guide](#how-to-use-this-guide)
- [What Counts as Current](#what-counts-as-current)
- [Junior Questions](#junior-questions)
- [Middle Questions](#middle-questions)
- [Senior Questions](#senior-questions)
- [God Mode Questions](#god-mode-questions)
- [Source Trail](#source-trail)
- [Contributing](#contributing)

## How to Use This Guide

Each question is written in interview style. The answer is intentionally short: enough to pass an interviewer checkmark, but not a full article. If you can explain the answer, add one real project example, and mention one tradeoff, you are in good shape.

The levels are not about job titles only:

- **Junior**: proves you can build and debug screens without superstition.
- **Middle**: proves you understand performance, native boundaries, and release realities.
- **Senior**: proves you can make architecture, migration, and product tradeoffs.
- **God Mode**: proves you understand React Native internals well enough to debug hard production failures and guide platform strategy.

## What Counts as Current

- React Native **0.82+** runs on the New Architecture only.
- React Native **0.84+** uses **Hermes V1 by default**.
- React Native **0.85** is the latest stable version at this review date.
- The modern architecture includes Fabric, TurboModules, Codegen, JSI, Hermes, modern DevTools, and React 19 features.
- Expo SDK releases intentionally track specific React Native versions, so interview answers should mention version alignment instead of assuming every Expo project is on latest React Native.
- Performance answers should start with measurement: React Native DevTools, performance marks, release builds, device profiling, and production telemetry.

---

## Junior Questions

Index: [J01](#j01) [J02](#j02) [J03](#j03) [J04](#j04) [J05](#j05) [J06](#j06) [J07](#j07) [J08](#j08) [J09](#j09) [J10](#j10) [J11](#j11) [J12](#j12) [J13](#j13) [J14](#j14) [J15](#j15) [J16](#j16) [J17](#j17) [J18](#j18) [J19](#j19) [J20](#j20) [J21](#j21) [J22](#j22) [J23](#j23) [J24](#j24) [J25](#j25) [J26](#j26) [J27](#j27) [J28](#j28) [J29](#j29) [J30](#j30)

<a id="j01"></a>
### J01. What is React Native actually rendering?

**Answer:** React Native renders native platform views, not HTML. Your React components describe UI in JavaScript, and React Native turns that tree into iOS and Android native view trees through the Fabric renderer.

<a id="j02"></a>
### J02. What is the difference between `View`, `Text`, and `ScrollView`?

**Answer:** `View` is the basic layout container, `Text` is required for rendering text, and `ScrollView` renders all of its children inside a scrollable area. For long or dynamic lists, prefer `FlatList` or `SectionList` because they virtualize rows.

<a id="j03"></a>
### J03. Why can a component re-render even when the screen looks unchanged?

**Answer:** React re-renders when state, props, or context used by a component changes. The visual result may be identical, but React still has to re-run render logic to compare what changed.

<a id="j04"></a>
### J04. When would you use `useState` instead of `useRef`?

**Answer:** Use `useState` when a value should trigger a re-render. Use `useRef` for mutable values that must persist between renders without updating the UI, such as timers, imperative handles, or previous values.

<a id="j05"></a>
### J05. What is the main risk of putting expensive work directly inside render?

**Answer:** Render may run often, so expensive work there can block the JavaScript thread and make gestures, navigation, or animations feel slow. Move heavy work outside render, memoize carefully, or push it to a background/native path when needed.

<a id="j06"></a>
### J06. Why should list items have stable keys?

**Answer:** Stable keys let React match items between renders. Index keys break when items are inserted, removed, or reordered, which can cause wrong state reuse, visual glitches, and unnecessary row work.

<a id="j07"></a>
### J07. Why is `FlatList` usually better than `ScrollView` for feeds?

**Answer:** `ScrollView` renders every child, which can waste memory and startup time. `FlatList` renders a window of visible and nearby items, trading some complexity for much better scalability.

<a id="j08"></a>
### J08. What does `extraData` do in `FlatList`?

**Answer:** `FlatList` is optimized with shallow prop checks. `extraData` tells it that something outside `data`, such as selected item state, should cause visible rows to update.

<a id="j09"></a>
### J09. What is a controlled input?

**Answer:** A controlled input gets its value from React state and updates that state through callbacks like `onChangeText`. It gives predictable UI state, but excessive updates in large forms should be handled carefully.

<a id="j10"></a>
### J10. What should you check before saying a React Native app is slow?

**Answer:** Check a release build on a real device, reproduce the slow path, and profile where time is spent. Development builds, simulators, console logs, and remote tooling can distort performance.

<a id="j11"></a>
### J11. What is the difference between `useEffect` and `useLayoutEffect`?

**Answer:** `useEffect` runs after paint and is right for data fetching, subscriptions, and side effects. `useLayoutEffect` runs before paint and is useful when layout measurement must update UI without visible flicker.

<a id="j12"></a>
### J12. Why can `useEffect` cause bugs with stale data?

**Answer:** Effects capture values from the render they belong to. If dependencies are missing or callbacks are not structured well, the effect can run with old props, state, or closures.

<a id="j13"></a>
### J13. What is the practical use of `React.memo`?

**Answer:** `React.memo` skips re-rendering a component when its props are shallowly equal. It helps for expensive child components, but it is not a default fix and can be useless if props change identity every render.

<a id="j14"></a>
### J14. When is `useCallback` useful?

**Answer:** `useCallback` is useful when function identity matters, such as passing callbacks to memoized children or subscription APIs. It does not make the function itself faster.

<a id="j15"></a>
### J15. How do you handle platform-specific UI differences?

**Answer:** Use platform checks, platform-specific files like `Button.ios.tsx` and `Button.android.tsx`, or shared abstractions that hide platform details. Keep differences small and explicit so the codebase does not fork into two apps.

<a id="j16"></a>
### J16. What is a safe area and why does it matter?

**Answer:** Safe areas protect content from notches, home indicators, rounded corners, and system UI. A polished app uses safe-area-aware layout instead of hardcoded padding.

<a id="j17"></a>
### J17. What makes touch handling different from web click handling?

**Answer:** Mobile touch needs feedback, cancellation, gesture conflict handling, and accessibility semantics. Components like `Pressable` model press states more accurately than treating everything like a browser click.

<a id="j18"></a>
### J18. What should every accessible button-like component provide?

**Answer:** It should expose the right role, label, state, focus behavior, and hit target. A custom `View` with `onPress` is not enough unless you add the missing accessibility semantics.

<a id="j19"></a>
### J19. Where should API secrets be stored?

**Answer:** Do not ship secrets in the mobile app bundle. Public config can live in app config, but real secrets belong on a server because users can inspect app binaries and JavaScript bundles.

<a id="j20"></a>
### J20. How should you handle loading, empty, and error states?

**Answer:** Treat them as first-class UI states, not afterthoughts. A production screen should clearly represent loading, empty data, recoverable errors, and retry paths.

<a id="j21"></a>
### J21. What is the difference between local state and server state?

**Answer:** Local state belongs only to the UI, like an open modal or selected tab. Server state comes from remote data and needs caching, invalidation, retries, and synchronization rules.

<a id="j22"></a>
### J22. Why should network requests be cancelled or ignored after unmount?

**Answer:** A response may arrive after the screen is gone. You should avoid setting state on unmounted screens and prevent stale responses from overwriting newer data.

<a id="j23"></a>
### J23. What is deep linking?

**Answer:** Deep linking opens a specific app screen from a URL or external intent. A good implementation maps URLs to navigation state and handles cold start, warm start, and invalid links.

<a id="j24"></a>
### J24. What is the difference between Expo Go and a development build?

**Answer:** Expo Go is a shared client for fast iteration with supported Expo APIs. A development build is your own app binary, so it can include custom native modules and app-specific native configuration.

<a id="j25"></a>
### J25. Why do React Native upgrades need testing on both iOS and Android?

**Answer:** React Native sits across JavaScript, native code, build tools, and platform SDKs. An upgrade can be fine on one platform and break native dependencies, permissions, layout, or build settings on the other.

<a id="j26"></a>
### J26. What is Hermes?

**Answer:** Hermes is the JavaScript engine optimized for React Native. Modern React Native uses Hermes by default, and Hermes V1 is now the default engine in current releases.

<a id="j27"></a>
### J27. What is React Native DevTools used for?

**Answer:** React Native DevTools is used to inspect components, debug JavaScript, inspect network activity, and profile performance. Modern interviews expect DevTools knowledge instead of old remote debugging habits.

<a id="j28"></a>
### J28. Why should you avoid heavy console logging in mobile apps?

**Answer:** Logging can slow JavaScript execution, make profiling noisy, and leak sensitive details. Keep logs structured, environment-aware, and removed or reduced in production.

<a id="j29"></a>
### J29. What is an error boundary?

**Answer:** An error boundary catches rendering errors below it and shows fallback UI instead of crashing the whole React tree. It does not replace native crash reporting or handling async errors.

<a id="j30"></a>
### J30. What makes a pull request in React Native safer to review?

**Answer:** Small scope, typed props, platform screenshots, tested edge states, and clear upgrade notes make review easier. For UI work, reviewers should see both iOS and Android behavior when possible.

---

## Middle Questions

Index: [M01](#m01) [M02](#m02) [M03](#m03) [M04](#m04) [M05](#m05) [M06](#m06) [M07](#m07) [M08](#m08) [M09](#m09) [M10](#m10) [M11](#m11) [M12](#m12) [M13](#m13) [M14](#m14) [M15](#m15) [M16](#m16) [M17](#m17) [M18](#m18) [M19](#m19) [M20](#m20) [M21](#m21) [M22](#m22) [M23](#m23) [M24](#m24) [M25](#m25) [M26](#m26) [M27](#m27) [M28](#m28) [M29](#m29) [M30](#m30)

<a id="m01"></a>
### M01. What changed when React Native became New Architecture-only?

**Answer:** Apps can no longer rely on opting back into the legacy architecture in current React Native. Fabric, TurboModules, Codegen, JSI, and Hermes are now the practical baseline for modern app and library work.

<a id="m02"></a>
### M02. What problem does Fabric solve?

**Answer:** Fabric is the modern renderer for React Native. It improves how React coordinates native view creation, layout, commits, and mounting, enabling concurrent React features and more predictable native rendering.

<a id="m03"></a>
### M03. What problem do TurboModules solve?

**Answer:** TurboModules modernize native module access with Codegen and JSI-based interop. They improve startup and type safety by loading modules lazily and exposing a better-defined JavaScript-to-native contract.

<a id="m04"></a>
### M04. Why is Codegen important?

**Answer:** Codegen turns typed JavaScript or TypeScript specs into native interface glue. This reduces hand-written mismatch bugs and gives native modules and components a clearer cross-platform contract.

<a id="m05"></a>
### M05. What does JSI allow that the old bridge model made hard?

**Answer:** JSI allows JavaScript and native code to hold references and call into each other without JSON-style serialization for every operation. That matters for high-frequency or large-data work like frames, audio, databases, or custom native engines.

<a id="m06"></a>
### M06. Does enabling the New Architecture automatically make an app fast?

**Answer:** No. It unlocks better capabilities, but the app still needs good rendering, data, image, list, and animation choices. You must profile the actual bottleneck.

<a id="m07"></a>
### M07. How do you approach a slow `FlatList`?

**Answer:** Measure first, then check row complexity, stable keys, `renderItem` identity, `extraData`, item memoization, images, pagination, and fixed-size optimizations like `getItemLayout`. Tune virtualization props only after you know whether the problem is CPU, memory, or blank areas.

<a id="m08"></a>
### M08. Why can nested `ScrollView` and `FlatList` be a problem?

**Answer:** A same-direction parent `ScrollView` can break the list's bounded viewport assumptions. That can disable useful virtualization behavior and cause memory or rendering problems.

<a id="m09"></a>
### M09. When should you consider FlashList or another list library?

**Answer:** Consider it when built-in lists cannot meet measured performance needs or when your list has complex dynamic measurement requirements. Still validate compatibility, maintenance, and New Architecture support before adopting it.

<a id="m10"></a>
### M10. How do worklets help performance?

**Answer:** Worklets can run selected JavaScript logic off the main JS thread in a separate runtime. They are useful for latency-sensitive gestures, animations, and some compute-heavy paths that should not block app interaction.

<a id="m11"></a>
### M11. Why is animation performance not only a JavaScript problem?

**Answer:** Animations involve React scheduling, native view updates, layout, GPU work, and gesture input. Smooth animation often requires moving updates away from React renders and measuring frame behavior on real devices.

<a id="m12"></a>
### M12. What is the significance of the new animation backend in React Native 0.85?

**Answer:** The shared animation backend moves more animation update logic into React Native core. It is intended to make Animated and Reanimated more consistent and opens the door to native-driver layout prop animations.

<a id="m13"></a>
### M13. What should you check when a dependency blocks an upgrade?

**Answer:** Check whether it supports the current React Native version, New Architecture, Hermes, required platform SDKs, and Expo SDK if relevant. Then decide whether to upgrade, patch, replace, fork, or isolate it behind a native boundary.

<a id="m14"></a>
### M14. Why do Expo SDK versions matter in interviews?

**Answer:** Expo SDK versions pin a supported React Native and React version. You should not assume an Expo app can freely jump to any React Native release without waiting for or testing the matching SDK.

<a id="m15"></a>
### M15. What is the difference between OTA updates and app store releases?

**Answer:** OTA updates can change JavaScript and assets within the same native runtime. Native module changes, permissions, build settings, and incompatible runtime changes require a new app binary.

<a id="m16"></a>
### M16. How do you make OTA updates safer?

**Answer:** Use runtime versioning, staged rollout, crash monitoring, rollback, and compatibility checks between JavaScript and native code. Never ship JavaScript that expects native APIs missing from the installed binary.

<a id="m17"></a>
### M17. What should a good mobile authentication flow handle?

**Answer:** It should handle secure token storage, refresh, logout, biometric or passcode policy if needed, deep link callbacks, expired sessions, and device compromise assumptions. Secrets should remain server-side.

<a id="m18"></a>
### M18. How would you debug a native crash from a React Native app?

**Answer:** Reproduce with the correct build type, inspect native crash logs, symbolicate the stack trace, map it to the involved native module or platform API, and correlate with JavaScript actions if available.

<a id="m19"></a>
### M19. How would you debug a JavaScript freeze?

**Answer:** Capture a performance profile, inspect long tasks, expensive renders, synchronous loops, JSON parsing, logging, and state updates. If the freeze happens during navigation or gestures, check work being done on focus or mount.

<a id="m20"></a>
### M20. Why is `InteractionManager` no longer the preferred answer for deferring work?

**Answer:** Modern docs mark `InteractionManager` as deprecated and recommend avoiding long-running work or using alternatives like `requestIdleCallback`. Interview answers should focus on splitting work, scheduling carefully, and measuring responsiveness.

<a id="m21"></a>
### M21. How do you decide between local storage, SQLite, and server cache libraries?

**Answer:** Use simple key-value storage for small preferences, SQLite for structured local data and queries, and server cache libraries for remote data synchronization. The decision depends on data shape, offline needs, consistency, and migration cost.

<a id="m22"></a>
### M22. What is a common mistake with app startup performance?

**Answer:** Doing too much synchronous work before the first useful screen. Defer non-critical initialization, reduce bundle and asset cost, avoid unnecessary providers, and measure cold start separately from warm start.

<a id="m23"></a>
### M23. What is the right way to think about state management libraries?

**Answer:** A state library should solve a concrete coordination problem, not replace component design. Server state, form state, navigation state, and global client state often need different tools.

<a id="m24"></a>
### M24. How do you prevent unnecessary screen re-renders?

**Answer:** Keep state close to where it is used, split expensive subtrees, stabilize props only where it matters, and avoid broad context updates. Use profiling before adding memoization everywhere.

<a id="m25"></a>
### M25. What makes image-heavy screens difficult?

**Answer:** Images affect network, decoding, memory, cache behavior, layout shifts, and scroll performance. A robust solution defines sizes, uses caching, serves correct formats, and avoids decoding too many large images at once.

<a id="m26"></a>
### M26. What is brownfield React Native?

**Answer:** Brownfield means embedding React Native inside an existing native app. It requires clear ownership of navigation, native dependencies, build systems, feature boundaries, and rollout strategy.

<a id="m27"></a>
### M27. Why are permissions not just a JavaScript concern?

**Answer:** Permissions are declared and enforced by the native platforms. JavaScript can request and react to them, but app manifests, Info.plist entries, store policies, and platform versions determine what is allowed.

<a id="m28"></a>
### M28. How do you test a React Native screen properly?

**Answer:** Use unit tests for logic, component tests for states and interactions, and E2E tests for critical flows on real platform behavior. Also verify accessibility, offline/error states, and release-build behavior for high-risk screens.

<a id="m29"></a>
### M29. What is the difference between a simulator issue and a device issue?

**Answer:** Simulators are useful but do not fully represent CPU, memory, GPU, camera, push notifications, biometrics, or vendor-specific Android behavior. Production performance and device APIs must be validated on real hardware.

<a id="m30"></a>
### M30. What should you mention when asked about React Native in 2026?

**Answer:** Mention New Architecture-only releases, Hermes V1 default, stronger DevTools, React 19 support, Expo/RN alignment, performance-focused libraries, and the move toward mature multi-platform support. Avoid presenting the old bridge as the normal runtime.

---

## Senior Questions

Index: [S01](#s01) [S02](#s02) [S03](#s03) [S04](#s04) [S05](#s05) [S06](#s06) [S07](#s07) [S08](#s08) [S09](#s09) [S10](#s10) [S11](#s11) [S12](#s12) [S13](#s13) [S14](#s14) [S15](#s15) [S16](#s16) [S17](#s17) [S18](#s18) [S19](#s19) [S20](#s20) [S21](#s21) [S22](#s22) [S23](#s23) [S24](#s24) [S25](#s25) [S26](#s26) [S27](#s27) [S28](#s28) [S29](#s29) [S30](#s30)

<a id="s01"></a>
### S01. How would you plan an upgrade from an old React Native app to current RN?

**Answer:** First inventory dependencies, native patches, build tools, platform SDK requirements, and New Architecture compatibility. Then upgrade in controlled steps, add telemetry, use the Upgrade Helper, test both platforms, and avoid mixing runtime-changing native updates with unrelated product work.

<a id="s02"></a>
### S02. How would you decide whether to use Expo or bare React Native for a new app?

**Answer:** Start with Expo unless native requirements, build ownership, or platform constraints clearly demand otherwise. Development builds and config plugins cover many custom native needs while preserving faster tooling and release workflows.

<a id="s03"></a>
### S03. How do you evaluate a third-party library for a production app?

**Answer:** Check maintenance, release cadence, New Architecture support, platform coverage, native code quality, issue history, bundle/build impact, and escape plan. A popular library can still be a poor fit if it owns a risky native surface.

<a id="s04"></a>
### S04. How do you design a screen that must work offline?

**Answer:** Define which data is authoritative, what can be stale, how writes queue, how conflicts resolve, and how users see sync state. Offline-first is a product contract, not only a storage choice.

<a id="s05"></a>
### S05. How do you approach performance budgets in a React Native app?

**Answer:** Set budgets for startup, screen transition time, frame drops, memory, network payloads, and crash-free sessions. Track them in CI or telemetry so performance does not depend on occasional manual profiling.

<a id="s06"></a>
### S06. What is your first move when a screen drops frames during gestures?

**Answer:** Profile the interaction on a release build and identify whether JS, native UI, layout, image decoding, or GPU work is the bottleneck. Then remove work from the critical gesture path instead of blindly memoizing components.

<a id="s07"></a>
### S07. How do React concurrent features matter in React Native?

**Answer:** They let React prioritize urgent UI work over less urgent rendering, improving responsiveness when used correctly. In React Native, their value depends on the New Architecture and on designing screens that can tolerate interruptible rendering.

<a id="s08"></a>
### S08. When would you use `startTransition` in a React Native screen?

**Answer:** Use it for non-urgent updates such as filtering a large list or updating secondary UI after input. It tells React that immediate interactions should stay responsive while the expensive update can be interrupted.

<a id="s09"></a>
### S09. How would you prevent a large context from hurting performance?

**Answer:** Split context by update frequency and responsibility, keep high-churn state local or in a selector-based store, and avoid putting broad objects into providers. Context is fine for stable dependencies but risky for frequently changing app state.

<a id="s10"></a>
### S10. How do you make navigation architecture maintainable?

**Answer:** Keep route definitions typed, deep-link mappings explicit, and screen ownership clear. Avoid business logic hidden in navigation callbacks; screens should handle domain behavior through services or hooks that can be tested.

<a id="s11"></a>
### S11. How would you handle a production-only crash after an OTA update?

**Answer:** Stop rollout, compare runtime versions, inspect crash reports, verify native API compatibility, and roll back if the JS bundle is unsafe. OTA systems need kill switches and staged rollout because native and JS versions can drift.

<a id="s12"></a>
### S12. What belongs in a native module instead of JavaScript?

**Answer:** Use native modules for platform APIs, high-throughput data, background services, secure storage, media processing, or code that must run outside JS thread constraints. Do not move logic native just because the JavaScript code is messy.

<a id="s13"></a>
### S13. How do you design a native module API?

**Answer:** Keep the JavaScript contract small, typed, versioned, and platform-aware. Prefer async APIs unless synchronous access is truly needed, and document threading, lifecycle, errors, and permission behavior.

<a id="s14"></a>
### S14. How do you decide whether a feature should be cross-platform or platform-specific?

**Answer:** Share product logic and common UI patterns, but respect platform conventions where they affect usability, permissions, navigation, or store policy. Forced sameness often creates worse apps than deliberate platform variation.

<a id="s15"></a>
### S15. How would you lead a New Architecture migration for a team?

**Answer:** Make dependency compatibility visible, remove abandoned packages, upgrade incrementally, test critical flows in release builds, and add crash/performance monitoring before rollout. The work is mostly risk management, not flipping a flag.

<a id="s16"></a>
### S16. How do you handle monorepos with React Native?

**Answer:** Align package manager behavior with Metro, native build systems, TypeScript, and dependency hoisting rules. The main risks are duplicate React copies, unresolved symlinks, native dependency paths, and slow CI.

<a id="s17"></a>
### S17. What is your strategy for app observability?

**Answer:** Combine JavaScript errors, native crashes, performance traces, network failures, release metadata, and user-impact metrics. Logs alone are not enough; you need correlation by app version, device, OS, and runtime.

<a id="s18"></a>
### S18. How do you secure sensitive mobile data?

**Answer:** Minimize what is stored, use platform secure storage for tokens, encrypt where appropriate, and assume the client is inspectable. Server-side authorization remains the real security boundary.

<a id="s19"></a>
### S19. How do you avoid regressions during React Native upgrades?

**Answer:** Keep upgrade diffs focused, run automated smoke tests, compare startup and key flows, test release builds, and monitor staged rollout. Upgrade work should have rollback planning like any other risky release.

<a id="s20"></a>
### S20. How would you choose between Reanimated, Animated, and plain React state for motion?

**Answer:** Use plain state for simple non-critical UI, Animated for supported native-driven animations, and Reanimated/worklets for gesture-coupled or highly interactive motion. The choice should match latency needs and team maintenance capacity.

<a id="s21"></a>
### S21. How should a team handle Android edge-to-edge changes?

**Answer:** Treat it as a design and QA change, not only an SDK bump. Verify status bar, navigation bar, safe areas, keyboard, modals, and screens with translucent system UI across supported Android versions.

<a id="s22"></a>
### S22. How do you manage feature flags in React Native?

**Answer:** Flags should support remote control, typed access, defaults, staged rollout, and cleanup ownership. Avoid flags that create incompatible JavaScript/native combinations unless tied to runtime versioning.

<a id="s23"></a>
### S23. How do you reason about memory leaks in React Native?

**Answer:** Look for retained subscriptions, timers, native listeners, large cached data, image memory, and native module lifecycles. Use platform memory tools plus React profiling because leaks can live on either side.

<a id="s24"></a>
### S24. How do you design for multiple form factors?

**Answer:** Use responsive layout, safe areas, input-aware interactions, and platform capability checks. Modern React Native can target phones, tablets, desktop-like windows, TV, and Quest-style environments, but assumptions about touch and fixed screen size break quickly.

<a id="s25"></a>
### S25. How would you introduce on-device AI into a React Native app?

**Answer:** Start with product value, privacy, model size, latency, battery, fallback, and observability. On-device AI may need native acceleration and careful memory management, so do not treat it as a normal API call.

<a id="s26"></a>
### S26. What is the risk of blindly adopting AI-generated React Native code?

**Answer:** It often ignores platform constraints, native configuration, accessibility, performance, security, and upgrade compatibility. Senior review should force code into established project patterns and verify behavior on real devices.

<a id="s27"></a>
### S27. How do you choose an app release strategy?

**Answer:** Combine binary releases for native/runtime changes with OTA for safe JavaScript fixes. Add staged rollout, runtime targeting, monitoring, and rollback so releases can be controlled after users install them.

<a id="s28"></a>
### S28. How do you handle a critical dependency that is unmaintained?

**Answer:** Assess usage surface, fork cost, replacement options, and security/native risk. If it touches critical native code, plan migration or ownership rather than waiting for it to break during the next RN upgrade.

<a id="s29"></a>
### S29. What is the senior answer to "React Native vs native"?

**Answer:** React Native is strong when shared product velocity and cross-platform UI matter, especially with teams invested in React. Pure native may win for platform-first experiences, extreme performance constraints, or deep OS-specific surfaces; the decision is organizational as much as technical.

<a id="s30"></a>
### S30. What makes someone senior in React Native?

**Answer:** They can connect React rendering, native platforms, build systems, performance, release safety, and product constraints. They do not just fix screens; they reduce risk across the whole mobile delivery system.

---

## God Mode Questions

Index: [G01](#g01) [G02](#g02) [G03](#g03) [G04](#g04) [G05](#g05) [G06](#g06) [G07](#g07) [G08](#g08) [G09](#g09) [G10](#g10) [G11](#g11) [G12](#g12) [G13](#g13) [G14](#g14) [G15](#g15) [G16](#g16) [G17](#g17) [G18](#g18) [G19](#g19) [G20](#g20) [G21](#g21) [G22](#g22) [G23](#g23) [G24](#g24) [G25](#g25) [G26](#g26) [G27](#g27) [G28](#g28) [G29](#g29) [G30](#g30)

<a id="g01"></a>
### G01. What are the major phases of the Fabric rendering pipeline?

**Answer:** React produces a tree, layout is calculated through Shadow Nodes, changes are committed, and native views are mounted. The important point is that React and native rendering now coordinate through a modern C++ renderer designed for concurrency.

<a id="g02"></a>
### G02. Why does synchronous layout matter in the New Architecture?

**Answer:** It lets code measure layout and update UI in the same commit path where appropriate. This avoids visible two-pass flicker for cases like tooltips, anchored overlays, and measurement-dependent layout.

<a id="g03"></a>
### G03. What is a Shadow Tree?

**Answer:** The Shadow Tree is React Native's layout representation of the UI, separate from actual native views. It lets React Native calculate layout and prepare mutations before mounting them to the platform UI layer.

<a id="g04"></a>
### G04. What is the difference between commit and mount?

**Answer:** Commit finalizes a set of UI changes in the renderer's tree. Mount applies the resulting mutations to the actual native view hierarchy.

<a id="g05"></a>
### G05. How does JSI change native interop failure modes?

**Answer:** JSI removes a lot of serialization overhead but introduces sharper concerns around object lifetime, thread affinity, runtime ownership, and native crashes. Bugs can move from "slow bridge call" to memory or concurrency issues.

<a id="g06"></a>
### G06. When is a synchronous native method justified?

**Answer:** Only when JavaScript needs an immediate value to continue correctly, such as cheap constants or tightly coupled layout/runtime queries. Synchronous calls can block execution, so heavy I/O or computation should stay async or off-thread.

<a id="g07"></a>
### G07. What does Hermes V1 becoming default imply for teams?

**Answer:** Teams get the new engine path without manual opt-in, but they must test startup, runtime behavior, native integrations, and tooling compatibility. Engine upgrades can expose assumptions in debugging, bytecode, and native JSI integrations.

<a id="g08"></a>
### G08. What are the risks of multiple JavaScript runtimes?

**Answer:** Values, object identity, scheduling, and lifetime do not automatically transfer safely between runtimes. Worklet-style systems need explicit serialization or shareable models and careful rules for native resources.

<a id="g09"></a>
### G09. Why is Codegen a strategic feature, not just tooling?

**Answer:** It makes the JS-native contract explicit and repeatable across platforms. That enables safer libraries, better upgrades, and less hand-written glue as React Native removes legacy architecture code.

<a id="g10"></a>
### G10. How do interop layers affect migration strategy?

**Answer:** Interop layers let some libraries keep working while the runtime moves forward, but they are not a reason to postpone real compatibility work forever. A senior plan treats them as a bridge for rollout, not the target architecture.

<a id="g11"></a>
### G11. Why did React Native remove more legacy architecture code gradually?

**Answer:** Gradual removal reduces binary size and complexity while limiting ecosystem breakage. It also gives library maintainers time to move APIs and native assumptions onto the New Architecture path.

<a id="g12"></a>
### G12. How do precompiled iOS binaries change build tradeoffs?

**Answer:** They reduce clean build time by avoiding local compilation of React Native core. The tradeoff is less flexibility when you need to build React Native from source for engine flags, debugging, or custom native changes.

<a id="g13"></a>
### G13. What does "DOM-like nodes via refs" mean in modern React Native?

**Answer:** Native component refs can expose a subset of DOM-style node APIs for traversal and measurement. It improves web alignment while preserving legacy measurement methods for compatibility.

<a id="g14"></a>
### G14. Why are Web Performance APIs useful in React Native?

**Answer:** APIs like `performance.mark`, `performance.measure`, and `PerformanceObserver` let teams instrument runtime behavior with concepts already used on the web. They also connect better with modern DevTools performance timelines.

<a id="g15"></a>
### G15. How do Chrome DevTools Protocol connections matter for React Native tooling?

**Answer:** CDP lets DevTools and other clients inspect runtime, network, and performance data through a standard protocol. Multiple simultaneous CDP connections allow richer workflows, such as DevTools, editor tools, and agents connected together.

<a id="g16"></a>
### G16. What is the hard part of building a high-performance native component?

**Answer:** The hard part is not drawing native UI; it is designing props, events, threading, layout behavior, memory ownership, accessibility, and update semantics that fit React's model across platforms.

<a id="g17"></a>
### G17. Why can layout animations be difficult in React Native?

**Answer:** Layout changes are owned by React, Yoga, native views, and animation systems at different points. The new shared animation backend aims to make those updates more consistent and better integrated with New Architecture rendering.

<a id="g18"></a>
### G18. What is the architectural value of worklets beyond animations?

**Answer:** Worklets provide a model for running JavaScript off the main JS runtime with lower-latency access to specific data or native capabilities. That matters for gestures, media, vision, crypto, and other responsive compute paths.

<a id="g19"></a>
### G19. What should you understand before exposing C++ or Rust to React Native?

**Answer:** You need a stable JS API, ownership rules, threading model, build integration, error mapping, and platform packaging strategy. The language choice matters less than whether the boundary is safe and maintainable.

<a id="g20"></a>
### G20. Why is React Native moving toward web API alignment?

**Answer:** Web-aligned APIs reduce conceptual differences between React DOM and React Native, helping shared libraries and developer knowledge transfer. The challenge is preserving native performance and platform semantics rather than copying the browser blindly.

<a id="g21"></a>
### G21. How should you reason about React Compiler in React Native?

**Answer:** React Compiler can reduce manual memoization pressure, but it does not remove the need for good component boundaries, stable data flow, and native performance profiling. It optimizes React code, not every mobile bottleneck.

<a id="g22"></a>
### G22. What is a dangerous assumption about Hermes performance?

**Answer:** Assuming the engine alone fixes slow screens. Hermes can improve startup and JavaScript execution, but layout, images, native work, network, renders, and memory pressure can still dominate user experience.

<a id="g23"></a>
### G23. How would you debug a crash inside a JSI native module?

**Answer:** Reproduce with symbols, inspect the native stack, verify runtime lifetime and thread access, reduce the JS call path, and add native assertions around ownership. JavaScript stack traces may be secondary or absent.

<a id="g24"></a>
### G24. Why is React Native on Meta Quest strategically interesting?

**Answer:** It shows React Native's many-platform model extending to Android-based immersive devices without inventing a separate app model. It also forces better thinking about input, resizable layouts, permissions, and platform capability checks.

<a id="g25"></a>
### G25. How do you design for platforms without Google Mobile Services?

**Answer:** Avoid assuming Play Services, mobile-only sensors, push implementations, maps, auth providers, or billing APIs are available. Put platform capabilities behind explicit checks and provide alternatives or disabled states.

<a id="g26"></a>
### G26. What is the right mental model for React Native 1.0 discussions?

**Answer:** It is less about a magic rewrite and more about maturity: stable APIs, New Architecture completion, better tooling, predictable releases, and ecosystem compatibility. Interviewers want judgment, not hype.

<a id="g27"></a>
### G27. How do release trains affect architecture planning?

**Answer:** React Native releases move on a schedule with support windows, so teams need regular upgrade capacity. Skipping many releases compounds native dependency risk and makes architecture migrations harder.

<a id="g28"></a>
### G28. How would you evaluate a custom renderer or non-mobile target?

**Answer:** Check host platform primitives, input model, accessibility, layout constraints, native module availability, build pipeline, debugging, and store/runtime rules. The React model can travel, but platform contracts still define the product.

<a id="g29"></a>
### G29. What is the deepest reason old bridge-era answers are now weak?

**Answer:** They describe constraints that are no longer the default runtime reality. Modern React Native interviews focus on Fabric, JSI, TurboModules, Hermes, concurrent React, New Architecture compatibility, and measurable production behavior.

<a id="g30"></a>
### G30. What is the God Mode answer to "How do you make React Native apps fast?"

**Answer:** Define the user-critical path, measure it on real devices, identify whether JS, native UI, layout, GPU, memory, network, or startup is limiting it, then move or remove work from that path. Architecture gives tools; engineering judgment chooses where to apply them.

---

## Source Trail

Primary sources used for the current baseline:

- [React Native Architecture: About the New Architecture](https://reactnative.dev/architecture/landing-page)
- [React Native Versions](https://reactnative.dev/versions)
- [React Native 0.82: A New Era](https://reactnative.dev/blog/2025/10/08/react-native-0.82)
- [React Native 0.83: React 19.2, New DevTools features, no breaking changes](https://reactnative.dev/blog/2025/12/10/react-native-0.83)
- [React Native 0.84: Hermes V1 by Default](https://reactnative.dev/blog/2026/02/11/react-native-0.84)
- [React Native 0.85: New Animation Backend, New Jest Preset Package](https://reactnative.dev/blog/2026/04/07/react-native-0.85)
- [React Native Comes to Meta Quest](https://reactnative.dev/blog/2026/02/24/react-native-comes-to-meta-quest)
- [React Native FlatList docs](https://reactnative.dev/docs/flatlist)
- [React Native InteractionManager docs](https://reactnative.dev/docs/interactionmanager)
- [React Native Security docs](https://reactnative.dev/docs/next/security)
- [Expo SDK version reference](https://docs.expo.dev/versions/latest/)
- [Expo SDK 56 beta changelog](https://expo.dev/changelog/sdk-56-beta)
- [Software Mansion: React Native in 2026 Trends and Predictions](https://swmansion.com/blog/react-native-in-2026-trends-our-predictions-463a837420c7/)
- [Callstack: React Native Wrapped 2025](https://www.callstack.com/blog/react-native-wrapped-2025-a-month-by-month-recap-of-the-year)
- [Infinite Red: React Native Wrapped 2025](https://shift.infinite.red/react-native-wrapped-2025-the-year-we-entered-our-polishing-era-79c6a3e5b4b7)

## Contributing

Good additions should be:

- Current for React Native 0.82+ and the New Architecture-only era.
- Useful in real interviews, not trivia.
- Short enough to review quickly.
- Backed by official docs, release notes, or widely trusted ecosystem sources.
- Clear about version-sensitive facts.

Suggested format:

```md
<a id="level-next"></a>
### LEVEL. Question?

**Answer:** Two or three concise sentences with one tradeoff or caveat.
```
