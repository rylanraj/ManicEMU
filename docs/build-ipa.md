# Build .ipa for iOS

This repo includes a GitHub Actions workflow to build an unsigned .ipa suitable for sideloading (AltStore/Sideloadly).

## CI (GitHub Actions)

- Workflow: `.github/workflows/build-ipa.yml`
- Triggers: push to main or manual run
- Output: `ManicEmu-unsigned.ipa` artifact

Steps performed:
1. Select Xcode 16 on macOS runner
2. `pod install` in `ManicEmu/`
3. Resolve SPM packages
4. Archive `ManicEmu` scheme with `SideloadRelease` configuration
5. Package `.app` into an unsigned `.ipa`

You can sideload this IPA using AltStore or Sideloadly; it will be resigned for your device at install time.

## Local build (Xcode)

1. Prereqs
   - Xcode 16+
   - VulkanSDK, Boost (see root README)
   - `cd ManicEmu && pod install`
2. Open `ManicEmu/ManicEmu.xcworkspace`
3. Target: `ManicEmu` (iOS), Scheme: `ManicEmu`
4. Product → Archive
   - Configuration: `SideloadRelease`
5. In Organizer, Export:
   - For Development (ad‑hoc) or for iOS App Store (if you have profiles)

If you don’t have Apple signing set up, export the `.app` from the archive and zip it as `Payload/ManicEmu.app` → `ManicEmu-unsigned.ipa`. Then sideload.

## Optional: Signed CI

If you want CI to produce a signed IPA automatically, add these secrets and steps:
- `APPLE_ID`, `APP_SPECIFIC_PASSWORD`, `TEAM_ID`
- Certificates/profiles in a repo secret or use `apple-actions/import-codesign-certs`
- Replace the archive step with a proper `xcodebuild -exportArchive -exportOptionsPlist` using your provisioning profile

We can wire this up when you’re ready to provide signing assets.
