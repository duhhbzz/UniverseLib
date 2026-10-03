# GK2 / Unity 6.3 UniverseLib Review

This branch is a review/staging branch for the Graveyard Keeper 2 UnityExplorer compatibility work inherited from Yascob99/UniverseLib.

## Fork lineage

- duhhbzz/UniverseLib
- forked from Yascob99/UniverseLib
- originally forked from sinai-dev/UniverseLib

## GK2-specific delta

The fork's `main` is exactly two commits ahead of commit `97cdfa66226134c436ad95e1db40a40c9e2a14af`:

1. `cf4bb546a19f0275c20acc287366e2b1a080dfd0` — **Unity 6.3 fix for Gk2**
   - modifies `src/Utility/UnityHelpers.cs`;
   - stops assuming the Unity scene handle is always an `int`;
   - uses reflection for Unity 6000+ to convert between the newer `SceneHandle` representation and `int`;
   - covers scene handle reads, scene construction from an integer handle, and internal scene-name lookup.

2. `1d918fccfca11d916715ff1f598e3e5e5b370fca` — **The rest of UnityExplorer 6.3 fix**
   - modifies `UniverseLib.Mono/UniverseLib.Mono.csproj`;
   - adds direct references to GK2's `netstandard.dll` and `System.Runtime.dll`;
   - currently contains a machine-specific Steam install path;
   - repeats the existing `UnityEngine.Modules` package version already supplied by `Directory.Build.props`.

## Upstream issue

The relevant UnityExplorer tracking issue is yukieiji/UnityExplorer#104.

The documented Unity 6.3 break is:

`System.MissingMethodException: Method not found: System.String UnityEngine.SceneManagement.Scene.GetNameInternal(Int32)`

Unity 6.3 changed scene handles to a specialized type, so code assuming an integer scene handle breaks.

## Build target

Yascob reported that the working UnityExplorer build was built for **Mono Bleeding Edge**. The runtime scene-handle fix itself is written against UniverseLib's Mono path and is intended to be usable beyond only that packaging target.

## Cleanup before treating this as canonical

Do not treat `UniverseLib.Mono.csproj` on inherited `main` as portable yet.

Before we keep or publish a canonical GK2 build:

1. Test whether the two direct GK2 managed-assembly references are actually required.
2. If they are required, replace the hard-coded Steam path with an MSBuild property such as `GK2ManagedDir` and an optional local default.
3. Avoid committing any game DLLs.
4. Build UnityExplorer against the resulting UniverseLib DLL.
5. Runtime-test object browsing, scene navigation, inspector use, C# console, and UI inspection in GK2 v1.008.

The goal is a reproducible developer-only UnityExplorer setup. GK2+ itself should not depend on UniverseLib or UnityExplorer.
