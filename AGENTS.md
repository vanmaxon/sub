# Repository Guidelines

## Project Structure & Module Organization

`src/` contains the C++20 application. Keep request handling in `src/handler/`, subscription parsing in `src/parser/`, output generation in `src/generator/`, scripting support in `src/script/`, server adapters in `src/server/`, and reusable helpers in `src/utils/`. Public or vendored headers live in `include/`; CMake find modules are in `cmake/`. Runtime templates, example preferences, profiles, and rule data belong under `base/`. Release automation and rule-maintenance utilities live in `scripts/`, while CI definitions are in `.github/workflows/`.

## Build, Test, and Development Commands

- `cmake -S . -B build -DCMAKE_BUILD_TYPE=Release` configures a local build after required libraries (CURL, yaml-cpp, PCRE2, QuickJS, libcron, RapidJSON, and toml11) are installed.
- `cmake --build build --parallel` compiles the `subconverter` executable.
- `cmake -S . -B build-static -DBUILD_STATIC_LIBRARY=ON && cmake --build build-static --parallel` builds the reduced static library variant.
- `bash scripts/build.windows.release.sh`, `bash scripts/build.macos.release.sh`, or `bash scripts/build.alpine.release.sh` reproduces the corresponding CI release package. These scripts install or clone dependencies and may modify generated rule data, so review the worktree afterward.
- `docker build -t subconverter-local scripts` builds the container image locally.

## Coding Style & Naming Conventions

Use four-space indentation and place opening braces on the following line, matching existing C++ files. Prefer lowercase filenames, usually `snake_case` (for example, `ruleconvert.cpp`), and keep paired headers and implementations together. Follow nearby naming conventions for functions and types; avoid broad style-only rewrites. The build enables `-Wall -Wextra` or MSVC `/W4`, so new code should compile without introducing warnings. No repository-wide formatter is configured.

## Testing Guidelines

There is currently no checked-in automated test suite or CTest target. At minimum, build the affected target and perform a focused smoke test using data from `base/`. For parser or generator changes, record the input format, command or endpoint, and expected output in the pull request. Add regression tests if introducing a test harness or extending an existing one.

## Commit & Pull Request Guidelines

Recent history uses short imperative subjects such as `Fix SNI config missing for TUIC` and scoped Conventional Commit-style messages such as `feat(QuanX): ...`. Keep commits focused and name the affected protocol or output target when useful. Pull requests should explain the behavior change, link related issues, list build and smoke-test results, and include representative before/after configuration output. For bugs, confirm reproduction against the latest CI build and include relevant logs without credentials, tokens, or private subscription URLs.
