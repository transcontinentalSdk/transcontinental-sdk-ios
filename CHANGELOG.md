# Changelog

## [Unreleased]

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
