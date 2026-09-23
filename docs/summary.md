# Summary

- Introduction

  - What is UrsinaForMobile?
**UrsinaForMobile (UfM)** is a build and deployment framework that enables **Ursina Engine** projects to be packaged, built, and distributed as **native Android applications**, using **Panda3D 1.11** as the underlying runtime.

Its primary goal is to make it possible to develop games in **Python with Ursina** and deploy them to Android **without rewriting the project in another language or engine**.

UfM is not a separate game engine. Instead, it acts as a **specialized integration layer** that adapts existing Ursina and Panda3D workflows to the constraints and requirements of the Android platform.

---

## Design Goals

UrsinaForMobile was designed with the following goals in mind:

* **Reuse existing Ursina projects**
  Games written for desktop Ursina should require minimal changes to run on Android.

* **Build on top of Panda3D’s official Android support**
  UfM relies on Panda3D 1.11’s `bdist_apps` Android pipeline instead of implementing a custom or unofficial solution.

* **Provide a predictable project structure**
  Asset handling, configuration, and build steps are standardized to reduce trial-and-error.

* **Remain transparent and debuggable**
  UfM does not hide Panda3D’s build process; developers are encouraged to understand how the Android bundle is produced.

---

## What UrsinaForMobile Is *Not*

To avoid confusion, it is important to clarify what UfM does **not** aim to be:

* ❌ A fork of Ursina
* ❌ A replacement for Panda3D
* ❌ A visual editor or no-code solution
* ❌ A cross-platform mobile abstraction layer

UrsinaForMobile focuses **exclusively on Android** and assumes familiarity with Python development and basic command-line tooling.

---

## How It Works (High-Level Overview)

At a high level, UrsinaForMobile works as follows:

1. **Your game is written using Ursina**, as a normal Python project.
2. **UfM provides a predefined project layout** that separates:

   * game code
   * assets
   * build configuration
3. **Panda3D 1.11 is used as the runtime and packaging backend**.
4. The build system generates an **Android App Bundle (AAB)** using Panda3D’s `bdist_apps`.
5. The resulting bundle can be:

   * installed locally for testing
   * converted to APKs
   * uploaded to the Google Play Store

UfM mainly contributes:

* Android-specific initialization logic
* Asset and wheel handling
* Build configuration conventions
* Tooling glue between Ursina and Panda3D’s Android pipeline

---

## Why Panda3D 1.11?

UrsinaForMobile is built on **Panda3D 1.11** because this version introduces **official Android App Bundle support**, which is required for modern Google Play Store submissions.

UfM does not reimplement this functionality. Instead, it configures and orchestrates Panda3D’s existing Android build system to work seamlessly with Ursina projects.

For details about the underlying Android packaging process, refer to the official Panda3D documentation:

> *Distributing Panda3D Applications → Building for Android*

---

## Project Status and Scope

UrsinaForMobile is an **actively developed project** and should be considered **advanced tooling** rather than a beginner-oriented framework.

Some constraints (such as pinned dependency versions and platform-specific behavior) are intentional and documented. These choices are made to ensure build reproducibility and compatibility with the Android toolchain.

As the project evolves, these constraints may be relaxed or improved, but stability and clarity take priority over convenience.

---

## Who This Project Is For

UrsinaForMobile is intended for developers who:

* Are already familiar with Python
* Have basic experience with Ursina or Panda3D
* Want to deploy games to Android without switching engines
* Are comfortable working with build tools and configuration files

If you are new to Ursina or game development in general, it is recommended to first explore Ursina on desktop before targeting mobile platforms.

  - Architecture Overview
Perfect choice.
This section is **crucial** for removing the “hack” perception, because architecture = intent.

Below is a **clean, professional “Architecture Overview”** that clearly explains *layers, responsibilities, and data flow* without over-explaining. It is written to slot directly after **“What is UrsinaForMobile?”**.

---

# Architecture Overview

UrsinaForMobile is structured as a **layered system** that builds on top of existing, well-established components. Each layer has a clearly defined responsibility, and no layer reimplements functionality that already exists elsewhere.

At a high level, the architecture consists of four main layers:

1. **Game Layer (Your Code)**
2. **Ursina Engine**
3. **UrsinaForMobile Integration Layer**
4. **Panda3D Android Build & Runtime**

---

## High-Level Architecture

```
+-----------------------------+
|         Your Game           |
|  (Ursina application code)  |
+-----------------------------+
              |
              v
+-----------------------------+
|        Ursina Engine        |
| (ECS,rendering abstraction) |
+-----------------------------+
              |
              v
+-----------------------------+
|   UrsinaForMobile (UfM)     |
|  - Android initialization   |
|  - Asset & wheel handling   |
|  - Project conventions      |
+-----------------------------+
              |
              v
+-----------------------------+
|        Panda3D 1.11         |
|  - Renderer (OpenGL ES)     |
|  - Python runtime           |
|  - Android packaging        |
|  - App Bundle generation    |
+-----------------------------+
              |
              v
+-----------------------------+
|           Android           |
|  (APK / AAB, system APIs)   |
+-----------------------------+
```

---

## Layer Responsibilities

### 1. Game Layer (Your Code)

This layer contains:

* Your game logic
* Gameplay systems
* Assets
* Entry-point script (`__main__.py`)

From the perspective of the game code, UrsinaForMobile should feel largely invisible. Aside from a small Android-specific initialization step, the game code is written the same way as a desktop Ursina project.

---

### 2. Ursina Engine

Ursina provides:

* High-level game abstractions
* Scene graph and entity system
* Input handling
* Asset loading
* Rendering abstractions

Ursina itself remains **unchanged**. UrsinaForMobile does not modify Ursina’s core behavior; instead, it adapts the environment in which Ursina runs.

---

### 3. UrsinaForMobile Integration Layer

This is the core contribution of the UfM project.

UrsinaForMobile is responsible for:

* **Android-specific initialization**
  Ensuring Ursina and Panda3D are configured correctly when running on Android.

* **Project layout conventions**
  Defining a predictable structure for assets, configuration, and build artifacts.

* **Dependency and wheel management**
  Bundling the correct Python wheels (Panda3D, Ursina, and dependencies) for Android architectures.

* **Build configuration orchestration**
  Translating project settings into Panda3D-compatible build options.

UfM does not replace Panda3D’s build system. Instead, it **configures and drives it** in a way that is compatible with Ursina projects.

---

### 4. Panda3D Android Build & Runtime

Panda3D 1.11 provides the following critical components:

* Android-compatible Python runtime
* OpenGL ES renderer (`pandagles2` / `pandagles`)
* Audio, input, and platform integration
* `bdist_apps` Android App Bundle generation

The final output of the build process is an **Android App Bundle (AAB)**, which follows Google Play Store requirements.

UrsinaForMobile depends on Panda3D’s **official Android support** and does not use unofficial or experimental packaging techniques.

---

## Build-Time vs Run-Time Responsibilities

Understanding the separation between build-time and run-time is important.

### Build-Time

Handled primarily by:

* `setup.py`
* Panda3D’s `bdist_apps`
* UfM’s build configuration

This phase:

* Collects assets
* Packages Python code
* Bundles platform-specific wheels
* Produces an AAB file

---

### Run-Time

Handled by:

* Panda3D runtime on Android
* Ursina engine
* Your game code

This phase:

* Initializes the renderer
* Loads assets
* Executes game logic
* Interfaces with Android APIs

---

## Why This Architecture Matters

This layered design has several advantages:

* **Stability**
  By relying on Panda3D’s official Android support, UfM avoids fragile, custom build systems.

* **Maintainability**
  Changes in Ursina or Panda3D can be addressed in isolation.

* **Transparency**
  Developers can inspect and debug each stage of the build process.

* **Future extensibility**
  Improvements to Panda3D’s Android pipeline automatically benefit UfM.

---

## Design Trade-Offs

UrsinaForMobile intentionally prioritizes:

* Correctness over convenience
* Explicit configuration over hidden automation
* Compatibility over novelty

As a result, some setup steps may feel more manual than in mobile-first engines. This is a deliberate choice to preserve control and debuggability.

---

## Summary

UrsinaForMobile is best understood as a **bridge**:

* Between **Ursina** and **Android**
* Between **Python game code** and **native mobile packaging**
* Between **developer intent** and **platform constraints**

The following sections build on this architectural foundation and explain how to set up the environment, structure a project, and produce a working Android application.

  - Relationship with Ursina & Panda3D
  - Project Status & Limitations

- Getting Started
  - Requirements
  - Quick Start (Minimal App)
  - Cloning the Repository

- Environment Setup
  - Python Version Requirements
  - Panda3D for Android (Why 1.11)
  - Android Toolchain (ADB, JDK, BundleTool)

- Project Structure
  - Repository Layout
  - Assets System
  - The Project Folder
  - The Build System

- Configuration
  - settings.toml Reference
  - Application Metadata
  - Build Options
  - Asset Inclusion Rules

- Writing Your Game
  - Entry Point (__main__.py)
  - Android Initialization
  - Logging & Debugging
  - Using Assets Correctly

- Building for Android
  - Build Overview
  - Creating an AAB
  - Signing the App
  - Converting to APKs

- Installing & Testing
  - Installing on a Device
  - Using BundleTool
  - Troubleshooting Install Issues

- Publishing
  - Play Store Requirements
  - Versioning Rules
  - Updating Releases

- Appendix
  - FAQ
  - Common Errors
  - Known Limitations
