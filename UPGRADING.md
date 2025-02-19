## Updating to 3.X

* Lines ending in [**] are candidates for backporting to 2.x if time/desired by users.
* Lines ending in [**!] have been backported.

**Breaking Changes**

* The built-in Avalonia UI now runs on version 11 of Avalonia (6a9a6426324e357daf0f9991d7a981b19a009b93, a0a7314317e9fc712270f75f31c6442a2d454da3, 46de3e9c9525cac4026a7959e44764752cdf36ee, `avalonia-preview` branch)
* Bumped minimum version of .NET Framework from 4.5.2 to 4.6.2 (57fa9ad2597ec6615894464b8f2ccef881dda52b) as 4.5.2 is EOL per https://learn.microsoft.com/en-us/lifecycle/products/microsoft-net-framework
* Removed `Newtonsoft.Json` dependency for .NET Framework build and use `System.Text.Json` (57fa9ad2597ec6615894464b8f2ccef881dda52b)
* .NET Framework `ReleaseNotesGrabber` uses an `HttpClient` instead of a `WebClient` for downloading (31523e95b50f00fab3fb3e043dea7d32227d5862)
* `WebClientFileDownloader` renamed to `WebFileDownloader` to better reflect functionality (e85235f512f4344c9a675e56e9a4d50434c33959)
* Changed `net6` to `net6.0` to be consistent with newer versions
* Removed `netcoreapp3.1` and `net5.0` compatibility as they are both EOL per https://dotnet.microsoft.com/en-us/platform/support/policy/dotnet-core (87381f2d3422e36fe5f614c85e43687eed497c42)
* Moved to official/non-preview builds of `System.Drawing.Common` for 8.x and bump up to `8.0.0` for older versions of .NET (87381f2d3422e36fe5f614c85e43687eed497c42) [**]
* Use `.axaml` in all Avalonia-related projects (46de3e9c9525cac4026a7959e44764752cdf36ee, e6d5ad20fec37e018a23ab46ef34d728b8104e96)
* `Exec(string cmd, bool waitForExit = true)` now returns a `bool`: `true` for the process starting, `false` otherwise
* The `IAppCastFilter` API has changed to: `IEnumerable<AppCastItem> GetFilteredAppCastItems(SemVerLike installed, IEnumerable<AppCastItem> items)`
* `IAppCastFilter` now expects you to filter out old versions and do sorting yourself as needed (previously filtering out old versions yourself could be avoided via a `bool` property on `FilterResult`; to do this easily yourself now, use the `AppCastReducers.RemoveOlderVersions` reducer like so: `AppCastReducers.RemoveOlderVersions(installed, itemsToFilter)`)
* The `FilterResult` class has been removed in its entirety (882ee260dd24245c53d351d4151b8b4bd4a53588)
* Used `SemVerLike` everywhere instead of `System.Version` for semver compatibility
* `WebFileDownloader` now deletes files on cancellation of a download like `LocalFileDownloader` did already (1cd2284c41bbe85d41566915965ad2acdb1a61f5)
* `WebFileDownloader` does not call `PrepareToDownloadFile()` in its constructor anymore. Now, it is called after the object is fully created. (420f961dfa9c9071332e2e0737b0f287d2cfa5dc)

**Changes/Fixes**

* Don't get download name if no need to (e63ebb054d62e26232b808189eb4619566952f3a) [**]
* Fixed some documentation in `LocalFileDownloader` (6ca714ca62577bc7412353338a148a03810ba81a) [**]
* Use net8.0 in Avalonia samples (46de3e9c9525cac4026a7959e44764752cdf36ee)
* Remove obsolete NetSparkleException override (3f54a53c7839fb99b291f8bc15b751aceb2d28de)
* Fixed `IsCriticalUpdate` not being written to the app cast (2142e8e0c277523ac33b2ba1d19d0e1fd9ca8483) [**!]
* Added more compatibility with Sparkle feeds (c9e2ddb4eea291c7f12e7747d9822aaa7fdb8c29) - see #275 [**]
* Added `InstallerProcess` public var for easier access of the installer process in case you are making an app that updates other apps (5e6c321846d08645e0fc891a33f1461780c4b405) [**]
* Allow user to avoid killing the parent process that started the update process -- `ShouldKillParentProcessWhenStartingInstaller`; defaults to `true` (19fae1ab1766eafea5a07881ac2598b4355f69f6) [**]
* Allow setting the process ID to kill when starting the installer -- `ProcessIDToKillBeforeInstallerRuns`; defaults to `null`(8cc81e0a561b82c220ecd3a86c5f424a236dc268) [**]
* Fixed Cancel button not working in Avalonia checking for updates window (f35e8968c1c7167664f90f91835ea3019acd2046) [**]
* Added `InstallerProcessAboutToStart` event. You can use this to modify/see the installer process before it actually begins and optionally make `NetSparkleUpdater` not run the process (you'll have to run the process yourself in that case). (a5cbbf1abce83de209f9d62a8fe2434f6b5cc85a)
* Added `SemVerLike` class that combines the normal .NET `System.Version` with semver properties (@kenjiuno)
* Added `VersionTrimmers` for trimming `SemVerLike` versions to `System.Version` (@kenjiuno)
* Added `AppCastReducers` helpers for common ways of filtering app cast items (@kenjiuno)
* Added `SparkleUpdater.InstallUpdateFailed` to see why `InstallUpdate` or its related installer methods fail. Also adds `Enums.InstallUpdateFailureReason`. (fe546de8667b1a5d6e0c4a72a7c128dc954f4aba)
* `WebFileDownloader.PrepareToDownloadFile` is now `virtual` (84df81122cfae9309de4f5b79489e46287bf3a62)

## Updating from 0.X or 1.X to 2.X

This section holds info on major changes when moving from versions 0.X or 1.Y. If we've forgotten something important, please [file an issue](https://github.com/NetSparkleUpdater/NetSparkle/issues).

* Minimum .NET Framework requirement, if running on .NET Framework, is now .NET Framework 4.5.2 instead of 4.5.1. Otherwise, the core library is .NET Standard 2.0 compatible, which means you can use it in your .NET Core and .NET 5+ projects.
* Change of base namespace from `NetSparkle` to `NetSparkleUpdater`
* `Sparkle` renamed to `SparkleUpdater` for clarity
* More logs are now written via `LogWriter` to help you debug and figure out any issues that are going on
* The default `NetSparkleUpdater` package (`NetSparkleUpdater.SparkleUpdater`) **has no built-in UI**. Please use one of the NetSparkleUpdater packages with a UI if you want a built-in UI that is provided for you.
  * Note that if you do _not_ use a `UIFactory`, you **must** use the `CloseApplication` or `CloseApplicationAsync` events to close your application; otherwise, your downloaded update file will never be executed/read! The only exception to this is if you want to handle all aspects of installing the update package yourself.
* XML docs are now properly shipped with the code for all public and protected methods rather than being here in this README file
  * Enabled build time warnings for functions that need documentation that don't have it
* `SparkleUpdater` constructors now require an `ISignatureVerifier` in order to "force" you to choose your signature verification method
* `SecurityMode` is a new enum that defines which signatures are required and which signatures are not required
  * Added `SecurityMode.OnlyVerifySoftwareDownloads` if you want to verify only software download signatures and don't care about verifying your app cast or release notes
* UIs are now in different namespaces. If you want to use a UI, you must pass in a `UIFactory` that implements `IUIFactory` and handles showing/handling all user interface elements
  * `NetSparkleUpdater` offers basic, built-in UIs for WinForms, WPF, and Avalonia. Copy & paste these files to your project and modify them to make them work better in your project!
  * `SparkleUpdater` no longer holds its own `Icon`. This is now handled by the `UIFactory` object.
  * `HideReleaseNotes`, `HideRemindMeLaterButton`, and `HideSkipButton` are all handled by the `UIFactory` objects
* Added built-in UIs for WPF and [Avalonia](https://github.com/AvaloniaUI/Avalonia) 0.10.X.
* Localization capabilities are now non-functional and are expected to come back in a later version. See [this issue](https://github.com/NetSparkleUpdater/NetSparkle/issues/92). (Contributions are welcome!)
* Most `SparkleUpdater` elements are now configurable. For example, you can implement `IAppCastHandler` to implement your own app cast parsing and checking.
  * `IAppCastDataDownloader` to implement downloading of your app cast file
  * `IUpdateDownloader` to implement downloading of your actual binary/installer as well as getting file names from the server (if `CheckServerFileName` is `true`)
  * `IAppCastHandler` to implement your own app cast parsing
  * `ISignatureVerifier` to implement your own download/app cast signature checking. NetSparkleUpdater has built-in DSA and Ed25519 signature verifiers.
  * `IUIFactory` to implement your own UI
    * `IUIFactory` implementors must now have `ReleaseNotesHTMLTemplate` and `AdditionalReleaseNotesHeaderHTML` -- it's ok if these are `string.Empty`/`""`/`null`.
    * `IUIFactory` methods all now take a reference to the `SparkleUpdater` instance that called the method
  * `ILogger` to implement your own logger class (rather than being forced to subclass `LogWriter` like in previous versions)
  * `Configuration` subclasses now take an `IAssemblyAccessor` in their constructor(s) in order to define where assembly information is loaded from
  * Many `SparkleUpdater` functions are now virtual and thus more easily overridden for your specific use case
  * Several `ReleaseNotesGrabber` functions are now virtual as well.
* Many delegates, events, and functions have been renamed, removed, and/or tweaked for clarity and better use
  * `DownloadEvent` now has the `AppCastItem` that is being downloaded rather than being just the download path
  * `AboutToExitForInstallerRun`/`AboutToExitForInstallerRunAsync` has been renamed to `PreparingToExit`/`PreparingToExitAsync`, respectively
  * The `UserSkippedVersion` event has been removed. Use `UserRespondedToUpdate` instead.
  * The `RemindMeLaterSelected` event has been removed. Use `UserRespondedToUpdate` instead.
  * The `FinishedDownloading`/`DownloadedFileReady` events have been removed. Use `DownloadFinished` instead.
  * The `StartedDownloading` event has been removed and replaced with `DownloadStarted`
  * The `DownloadError` event has been removed and replaced with `DownloadHadError`
  * `Sparkle.RunUpdate` no longer exists. Use `SparkleUpdater.InstallUpdate` instead.
  * `Sparkle.DownloadPathForAppCastItem` -> `SparkleUpdater.GetDownloadPathForAppCastItem`
  * `AppCastItem.DownloadDSASignature` -> `AppCastItem.DownloadSignature`
  * `SilentModeTypes` enum renamed to `UserInteractionMode`
  * `Sparkle.SilentMode` renamed to `Sparkle.UserInteractionMode`
  * `UseSyncronizedForms` renamed to `ShowsUIOnMainThread`
* Samples have been updated and improved
  * Sample apps for [Avalonia](https://github.com/AvaloniaUI/Avalonia), WinForms, and WPF UIs
  * Sample app to demonstrate how to handle events yourself with your own, custom UI
* By default, the app cast signature file now has a `.signature` extension. The app cast downloader will look for a file with the old `.dsa` signature if data is not available or found in a `appcast.xml.signature` on your server. You can change the extension using the `signature-file-extension` option in the app cast generator and via the `XMLAppCast.SignatureFileExtension` property.
* `sparkle:dsaSignature` is now `sparkle:signature` instead. If no `sparkle:signature` is found, `sparkle:dsaSignature` will be used (if available). If `sparkle:dsaSignature` is not found, `sparkle:edSignature` will be used (if available). This is to give us as much compatibility with old versions of `NetSparkle` as well as the macOS `Sparkle` library.
* An entirely new app cast generator tool is now available for use.
* By default, the app cast generator tool now uses Ed25519 signatures. If you don't want to use files on disk to store your keys, set the `SPARKLE_PRIVATE_KEY` and `SPARKLE_PUBLIC_KEY` environment variables before running the app cast generator tool. You can also store these signatures in a custom location with the `--key-path` flag.
  * You can still use DSA signatures via the DSAHelper tool and the `DSAChecker` class. This is not recommended.
  * `Ed25519Checker` is the class responsible for handling Ed25519 signatures. `DSAChecker` sticks around for verifying DSA signatures if they're still used.
* Removed `AssemblyAccessor` class in lieu of `IAssemblyAccessor` implementors
* The server file name for each app cast download is now checked before doing any downloads or showing available updates to the client. To disable this behavior and use the name in the app cast, set `SparkleUpdater.CheckServerFileName` to `false`.
* `bool ignoreSkippedVersions = false` has been added to `CheckForUpdatesAtUserRequest`, `CheckForUpdatesQuietly`, and `GetUpdateStatus` to make ignoring skipped versions easier.
* The file name/path used by `RelaunchAfterUpdate` are controlled by `RestartExecutableName` and `RestartExecutablePath`, respectively. `SparkleUpdater` makes a best effort to figure these out for you; however, you can override them if you need to. NOTE: The way these parameters are fetched has CHANGED in recent previews (as of 2021-04-18) -- YOU HAVE BEEN WARNED!!
* **Breaking change**: `CheckForUpdatesQuietly` now shows no UI ever. It could show a UI before, which didn't make a lot of sense based on the function name. Make sure that if you use this function that you handle showing a UI yourself if necessary. (See the HandleEventsYourself sample if you want help.) You can always trigger the built-in `SparkleUpdater` by calling `_sparkle.ShowUpdateNeededUI(updateInfo.Updates)`. 
* **Breaking change**: DLL assembly names for .NET Framework WinForms UI dlls changed from `NetSparkle.UI.WinForms` to `NetSparkleUpdater.UI.WinForms`.
* **Breaking change**: DLL assembly names for Avalonia UI dlls changed from `NetSparkle.UI.Avalonia` to `NetSparkleUpdater.UI.Avalonia`.
* **We now rely on Portable.BouncyCastle** (BouncyCastle.Crypto.dll) for the ed25519 implementation. This means there is another DLL to reference when you use NetSparkle!
* **We now rely on System.Text.Json (netstandard2.0) OR Newtonsoft.Json (.NET Framework 4.5.2)** for the JSON items. This means there is another DLL to reference when you use NetSparkle, and it will change depending on if the `System.Text.Json` or `Newtonsoft.Json` item is used!
