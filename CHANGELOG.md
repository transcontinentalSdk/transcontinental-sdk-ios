# Changelog

## [2.x] - Unreleased

## [2.4.3] - 2025-10-31

### Added

- Legal pages are collapsible on phones (collapsed by default)

### Changed

- "Cancel" button removed from the Search Bar
- Filters can be cleared with the associated "x"

### Fixed

- Click regions in iOS 26 were not always laid out correctly
- Filter buttons stay highlighted when there are applied filters

## [2.4.2] - 2025-09-30

### Added

- Page indicator can be disabled via the `FlyerStyle` theme

### Fixed

- Search bar no longer disappears on scroll bounce

## [2.4.1] - 2025-08-29

### Fixed

- Fixed the race condition on search text change
- Fixed potential double-parsing of Publications, causing some extra latency on large flyers

## [2.4.0] - 2025-07-29

### Added

- Added support for animated, transparent `webp` overlays

## [2.3.2] - 2025-06-27

### Fixed

- Banner ads are excluded from `mobileUrl` fallback 
- Disclaimers larger that 2 lines are collapsed and expanded on demand

## [2.3.1] - 2025-05-30

### Added

- Added support for `appear`/`fade` Carousel effects

### Fixed

- Fixed conditional top-padding when masthead is hidden

## [2.3.0] - 2025-04-02

### Added

- Added video playback occupying the full `FlyerView`, as well as full-screen
- Updated `Publication.Attributes` to include `jobName` and `category`

## [2.2.0] - 2025-01-31

### Fixed

- Swapping between regular and search modes is more consistent regardless of initial page state
- Mitigated the effect of race condition on search text change
- Searching static guidebooks renders similarly to the web flyer
- XCode version bumped to 16.0 to fix CI compilation problems

## [2.1.3] - 2024-10-03

### Fixed

- In-block play button occasionally moves within block on scroll
- Partial-block videos can be slightly offset from absolute location on scroll
- Video should auto-close after first play when not looping

## [2.1.2] - 2024-09-25

### Fixed

- Aligned search result ordering with Android
- Centralized `DispatchQueue` event handler usage
- Removed transitive references to `FlyerViewUI` (thus removing references to `FlyerView`)

## [2.1.1] - 2024-09-20

### Added

- Added a `hidden` parameter (default `false`) to the Masthead and Search themes to selectively disable functionality

### Fixed

- Fixed page indicator double-refresh
- Fixed search bar localization placeholder
- Fixed retain cycle in FlyerView delegate

## [2.1.0] - 2024-08-16

The v2.1.0 release introduces several new features and improvements to the SDK, bringing it closer to parity with the TC Web SDK:

- Configurable in-block and partial-block video playback (muted by default)
- Carousel views with manual or automatic ("slide-show") navigation
- Ability to perform internal (deep) or external (web) linking functionality as provisioned by the TC API

## [2.0.1] - 2024-04-29

### Fixed

- Fixed outbound date encoding when using `listPublication` API
- Fixed environment URLs being incorrectly decoded in certain release types
- Stopped emitting SKU events for URL/Video blocks

## [2.0.0] - 2023-09-29

The v2.0.0 update introduces substantial performance improvements for the SDK, driven by several optimizations and technical enhancements:

- Reduced time to first image when opening a flyer with the new rendering engine by re-writing manifest parsing
- Reduced total memory usage by 5-10x by eagerly caching to disk and evicting off-screen images from memory
- Improved scrolling responsiveness by moving most/all download/parsing/rendering-related actions to worker threads. This results in less "janky" scrolling when viewing very large flyers
- Reduced bandwidth requirements by enabling HTTP Range Requests to allow partial downloads, instead of re-downloads
- Reduced likelihood of temporarily blank screens by dynamically changing download/rendering priorities to prefer what should be currently on-screen, and deferring all off-screen images

These updates will facilitate some upcoming performance enhancements, namely pre-fetching. Pre-fetching will allow the SDK to optionally download and cache images before they are needed, resulting in a much smoother user experience and an even lower time to first image.

These improvements are the result of a significant re-write of the SDK's rendering engine, and as such, we are releasing this as a breaking version change.

## [1.1.4] - 2022-12-14

### Fixed

- Fixed a rare issue where a static flyer was mis-rendered on an iPhone XR

## [1.1.3] - 2022-07-21

### Fixed

- Fixed a rare issue where a flyer was mis-rendered

## [1.1.2] - 2022-06-02

### Added

- The `Flyer` object now contains a friendly `displayTitle` string

### Fixed

- Internal product SKUs should not be emitted by the SDK
- Adjusted full-height blocks in horizontal mode on small-screened devices

## [1.1.1] - 2022-05-31

### Changed

- Replaced rendering engine on the horizontal view for faster first-paint time and improved scrolling when flyer download has completed
  - This change has resulted in slightly janky scrolling while downloads are in-progress, this is actively being worked on
- Reduced memory pressure (overall, but with a focus on memory spikes) during network download and render use cases

## [1.1.0] - 2022-05-20

### Added

- Removed `print` statements from within the SDK and replaced them with an externally-facing Logger API

### Changed

- Replaced the rendering engine for faster first-paint time and improved scrolling when flyer download has completed
  - This change has resulted in slightly janky scrolling while downloads are in-progress, this is actively being worked on
- `setSdkParams` accepts "fr" in lieu of "bil" for French language stores/flyers

### Fixed

- If `language` and `storeId` are passed into the SDK, they do not also need to be passed to the Views

## [1.0.6] - 2022-04-22

### Changed

- Statically linking internal libraries, in lieu of dynamic linking
- Changed iOS deployment version to 12.0

## [1.0.5] - 2022-04-06

### Parameter Initialization

A method was added to the `DFManager` singleton to consolidate initialization:

#### Previously:

```swift
var storeId = "myStoreId"
var language = "en" // "en" or "bil"
var banner = "myBanner"
var environment = "staging" // "staging", "live"
var client = "myClient"
var date = "2021/12/31" // Optional
```

#### Now:

```swift
DFManager.shared.setSdkParams(
    "myBanner",
    "myClient",
    "staging", // "staging", "uat", "live"
    "myStoreId",
    "en", // "en" or "bil"
    Date(), // Optional - defaults to current date
    .black,
) { (response, error) in
    // ... other SDK calls ...
}
```

#### Store/Language Modification

The following methods have been made available on the `DFManager` singleton in order to modify the `store id` and/or `language`:

```swift
DFManager.shared.changeStoreId("myStoreId")
DFManager.shared.changeLang("en")
DFManager.shared.setStoreIdAndLanguage("myStoreId", "en")
```

### `ERR_NO_BASE_URL_FOUND` replaced by `ERR_PARAM_VALIDATION`

If any parameters required by the Web API are missing, a `NSError.domain = ERR_PARAM_VALIDATION` will be returned to the caller.

### `getFlyerListingData` API change

The API for `getFlyerListingData` has been changed to `onLandingResponse` and to use the `storeId` and `language` that are passed in previously, rather than require them again:

#### Previously:

```swift
DFManager.shared.getFlyerListingData(storeId:, language: ) { (response, error) in ... }
```

#### Now:

```swift
DFManager.shared.onLandingResponse() { (response, error) in ... }
```
