# iOS to Android UI Parity Skill

## Purpose

Convert existing SwiftUI iOS screens into Android Jetpack Compose while preserving the original product design, layout, behavior, information hierarchy, and user experience as closely as practical.

The goal is product and UI parity, not a Material Design reinterpretation.

Use the iOS implementation as the reference implementation and the iOS screenshot as the primary visual source of truth.

Exact pixel matching is not more important than Android usability, accessibility, system behavior, localization, or platform requirements.

## When to Use

Use this skill when:

- Porting an existing SwiftUI screen to Android Jetpack Compose
- Rebuilding an iPhone app screen on Android
- Matching Android UI to an existing iOS screenshot
- Fixing visual differences between iOS and Android
- Converting SwiftUI layouts, components, states, and navigation to Compose
- Creating shared Android design tokens based on an existing iOS design system
- Migrating an existing native iOS application to a native Android application
- Validating whether the Android version feels like the same product as the iOS version

Do not use this skill when the goal is to redesign the app specifically for Material Design.

## Core Principle

Preserve the product design, not the framework defaults.

Do not blindly translate:

- SwiftUI Button to Material Button
- SwiftUI TextField to Material TextField
- SwiftUI card-like views to Material Card
- SwiftUI navigation to default Material navigation visuals
- SF Symbols directly to Material Icons without visual review
- iOS safe-area assumptions directly to Android system insets

Framework-equivalent components often have different:

- padding
- typography
- corner radius
- elevation
- minimum size
- ripple behavior
- safe-area behavior
- icon geometry
- vertical alignment
- default colors
- interaction behavior

When framework defaults change the appearance or behavior, build or reuse custom Compose components.

## Reference Priority

When multiple sources disagree, use this priority:

1. Product requirements and platform safety requirements
2. iOS reference screenshot
3. Current SwiftUI implementation
4. Existing product design tokens or design specifications
5. Existing Android project conventions
6. Material defaults

The screenshot is the primary visual reference, but it must not override accessibility, system behavior, localization, or mandatory Android platform requirements.

## Conflict Resolution

When exact iOS visual parity conflicts with Android usability, accessibility, system behavior, localization, or platform requirements:

1. Preserve product identity.
2. Preserve information hierarchy.
3. Preserve layout intent and visual proportions where practical.
4. Preserve core interaction intent.
5. Follow Android accessibility requirements.
6. Follow Android system behavior and platform requirements.
7. Support localization and RTL correctly.
8. Do not force pixel-perfect parity when it harms usability.
9. Document intentional platform differences.
10. Never silently redesign the product.

The target is the same product experience, not identical rendering at any cost.

## Required Workflow

### 1. Analyze Before Coding

Before creating or changing Android UI, inspect the corresponding SwiftUI screen and identify:

- screen hierarchy
- sections
- component boundaries
- horizontal and vertical spacing
- padding
- alignment
- fixed dimensions
- flexible dimensions
- typography
- colors
- corner radius
- borders
- shadows
- image scaling
- scroll behavior
- safe-area behavior
- keyboard behavior
- loading state
- empty state
- error state
- disabled state
- navigation behavior
- sheet or dialog behavior
- accessibility behavior
- localization behavior
- RTL behavior if applicable
- dynamic type or font-scale implications

Do not begin by guessing an Android layout.

### 2. Extract Design Tokens

Prefer reusable tokens instead of scattered hard-coded values.

Create or reuse structures such as:

```kotlin
object AppSpacing {
    val xxs = 4.dp
    val xs = 8.dp
    val sm = 12.dp
    val md = 16.dp
    val lg = 20.dp
    val xl = 24.dp
    val xxl = 32.dp
}

object AppRadius {
    val small = 8.dp
    val medium = 12.dp
    val large = 16.dp
    val extraLarge = 20.dp
}
```

Also prefer reusable definitions for:

- AppColors
- AppTypography
- AppSpacing
- AppRadius
- AppDimensions
- AppIcons
- AppElevation
- AppInsets

If the iOS application already defines explicit values, preserve them unless Android platform requirements require an intentional difference.

## Layout Rules

### Spacing and Padding

Translate SwiftUI spacing intentionally.

Example:

```swift
VStack(spacing: 16) {
    ...
}
.padding(.horizontal, 20)
```

Preferred Compose equivalent:

```kotlin
Column(
    modifier = Modifier.padding(horizontal = 20.dp),
    verticalArrangement = Arrangement.spacedBy(16.dp)
) {
    ...
}
```

Do not replace explicit values with Material defaults.

### Dimensions

Preserve:

- component heights
- image dimensions
- icon sizes
- minimum touch areas where possible
- aspect ratios
- section spacing

Avoid unnecessary fixed widths if SwiftUI uses flexible layout.

Prefer constraints that reproduce the same layout intent across common Android device widths.

Do not tune a screen only for one test device.

## Density and Device Size Rules

Android devices vary more widely in screen size, density, aspect ratio, and font scaling.

Do not assume a single iPhone screenshot maps directly to one Android device.

Validate the screen across at least these layout categories when practical:

- compact phone width
- standard phone width
- large phone width
- tall aspect-ratio device
- device with gesture navigation
- device with three-button navigation if supported by the test environment

Use:

- dp for layout dimensions
- sp for text dimensions
- scalable constraints where the iOS layout is flexible
- aspect ratios for media where appropriate

Avoid:

- pixel-based layout assumptions
- arbitrary screen-width multipliers without design intent
- fixed heights for text containers that can break under font scaling
- positioning based on one device screenshot only

The iOS screenshot is a visual reference, not a fixed coordinate map.

## Android Edge-to-Edge and System Insets

Modern Android applications may render edge-to-edge.

Review all screens for:

- status bar
- display cutout
- navigation bar
- gesture area
- keyboard
- bottom navigation
- modal sheets
- full-screen media

Use Compose inset APIs intentionally when needed:

```kotlin
Modifier.statusBarsPadding()
Modifier.navigationBarsPadding()
Modifier.imePadding()
```

Or use appropriate WindowInsets APIs when the project architecture requires them.

Rules:

1. Do not apply system insets twice.
2. Do not hard-code status bar heights.
3. Do not hard-code navigation bar heights.
4. Verify edge-to-edge behavior on devices with gesture navigation.
5. Ensure bottom fixed actions remain usable above system gesture areas.
6. Ensure keyboard appearance does not cover focused input or important actions.
7. Preserve the visual intent of the iOS safe area while using Android-native inset handling.

## Typography Rules

Typography is a major source of cross-platform visual mismatch.

For each text element, preserve:

- font family
- font size
- font weight
- line height
- letter spacing
- text alignment
- max lines
- truncation behavior

Example:

```kotlin
Text(
    text = title,
    fontSize = 16.sp,
    fontWeight = FontWeight.SemiBold,
    lineHeight = 22.sp
)
```

Do not assume:

```text
SwiftUI .title2 == MaterialTheme.typography.titleLarge
```

They are conceptually similar but not visually identical.

If the product uses a custom font such as Pretendard, use the same font family on Android whenever licensing and project configuration permit.

## Accessibility and Font Scale

Visual parity must not break accessibility.

Support Android font scaling and accessibility behavior.

Check:

- larger system font sizes
- text truncation
- wrapped text
- touch target size
- content descriptions
- screen reader semantics
- disabled state clarity
- color contrast
- focus order
- keyboard navigation where relevant

Rules:

1. Do not force text into fixed-height containers when text may scale.
2. Do not reduce font size automatically just to preserve screenshot geometry.
3. Allow important text to wrap when practical.
4. Keep interactive targets large enough for Android accessibility expectations.
5. Add meaningful contentDescription values where required.
6. Decorative images should not create unnecessary screen reader noise.
7. Preserve semantic grouping and heading intent.
8. Accessibility requirements take priority over exact pixel parity.

If the design cannot support larger font scales without layout changes, document the intentional responsive behavior.

## Localization and RTL

The Android implementation must support translated text without assuming English or Korean text lengths.

Check:

- short labels
- long labels
- multiline text
- long German or French strings
- Arabic and Urdu RTL layout
- Chinese and Japanese line breaking
- numeric formatting
- currency formatting
- date formatting

Prefer:

- start instead of left
- end instead of right
- layout-direction-aware alignment
- localized string resources
- locale-aware number and date formatting

Avoid hard-coded UI text inside Composables unless the project explicitly requires it.

For RTL languages:

1. Verify horizontal layout direction.
2. Verify navigation icons and directional arrows.
3. Verify text alignment.
4. Verify padding and margins use start and end where appropriate.
5. Verify charts or financial indicators that must remain logically directional.
6. Do not mirror content that should remain semantically fixed.
7. Verify mixed RTL and LTR text such as stock symbols, URLs, numbers, and codes.

Do not sacrifice localization correctness to match an LTR reference screenshot.

## Color Rules

Preserve exact product colors when available.

Do not replace product colors with Material semantic defaults unless the original design intentionally uses them.

Prefer:

```kotlin
AppColors.Background
AppColors.Surface
AppColors.PrimaryText
AppColors.SecondaryText
AppColors.Border
AppColors.Positive
AppColors.Negative
```

Support dark mode only according to the existing product design.

Check accessible contrast in both light and dark modes.

## Component Rules

### Buttons

Avoid default Material Button when its internal padding, minimum height, radius, colors, or elevation differs from iOS.

Prefer a custom component when necessary:

```kotlin
@Composable
fun AppPrimaryButton(
    title: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier
)
```

Preserve:

- height
- radius
- text style
- horizontal padding
- background
- disabled appearance
- pressed behavior

Do not reduce touch targets below reasonable Android accessibility expectations merely to match iOS geometry.

### Text Fields

Material TextField and OutlinedTextField frequently differ substantially from iOS.

When visual parity is required, use a custom container with BasicTextField if necessary.

Preserve:

- height
- internal padding
- placeholder style
- border
- focus state
- background
- cursor
- error state

Also verify:

- keyboard type
- IME action
- focus traversal
- autofill behavior
- password behavior
- accessibility label

### Cards and Panels

Do not introduce Material Card automatically.

If the iOS view is simply:

```text
background
+
border
+
radius
```

implement exactly that using Compose modifiers.

Example:

```kotlin
Modifier
    .clip(RoundedCornerShape(12.dp))
    .background(AppColors.Surface)
    .border(
        width = 1.dp,
        color = AppColors.Border,
        shape = RoundedCornerShape(12.dp)
    )
```

### Icons

SF Symbols and Material Icons are not geometrically identical.

Priority:

1. Existing custom product asset
2. Equivalent provided Android vector asset
3. Closest Material icon only if exact parity is not important

Check:

- icon size
- optical weight
- baseline
- padding
- filled vs outlined style
- RTL behavior
- accessibility meaning


## Complex Visual Components

Charts, gauges, progress arcs, donut charts, score rings, custom indicators, and other Canvas-based visual components require explicit layout validation.

Functional parity alone is not enough for these components.

The Android result must preserve:

- component position
- component size
- visual proportion
- surrounding whitespace
- relationship to nearby text
- information hierarchy
- label placement
- score placement
- clipping behavior
- responsive behavior

### Analyze the Layout Box Before Coding

Before implementing a complex visual component, identify its dedicated layout region.

Example:

```text
Market Summary Card

┌─────────────────────────────────┐
│ Today Market             info ↻ │
│                                 │
│ ┌────────────────┐ ┌──────────┐ │
│ │ Text Summary   │ │ Gauge    │ │
│ │ Result         │ │ Score    │ │
│ │ Description    │ │ Label    │ │
│ └────────────────┘ └──────────┘ │
│                                 │
│ Date | Confidence | Basis | Lag │
│                                 │
│ Evidence Bar                    │
└─────────────────────────────────┘
```

Do not place a gauge, chart, or Canvas into a Row or Box without first defining the region that owns it.

The visual component must not overlap adjacent text or expand into another information region.

### Dedicated Gauge Components

Create reusable gauge components instead of drawing gauge logic directly inside a screen.

Examples:

```text
MarketGauge
RiskGauge
AiScoreGauge
PortfolioDonut
MomentumGauge
```

The screen owns layout and positioning.

The gauge component owns rendering.

Do not combine screen positioning logic and gauge drawing logic in the same Composable unless the component is truly screen-specific.

### Gauge Size Constraints

A gauge must receive an explicit size or a bounded parent constraint.

Preferred:

```kotlin
Box(
    modifier = Modifier
        .width(120.dp)
        .height(110.dp),
    contentAlignment = Alignment.Center
) {
    MarketGauge(
        score = score,
        modifier = Modifier.size(104.dp)
    )
}
```

Avoid an unconstrained visual component.

Do not use this pattern inside a general Row or Card:

```kotlin
Canvas(
    modifier = Modifier.fillMaxSize()
)
```

unless the parent itself is the intentionally bounded gauge container.

Do not allow the Canvas to determine the size of the surrounding card.

### Recommended Gauge Layout

For a text summary and gauge displayed side by side:

```kotlin
Row(
    modifier = Modifier.fillMaxWidth(),
    verticalAlignment = Alignment.CenterVertically
) {
    Column(
        modifier = Modifier.weight(1f)
    ) {
        // Result
        // Summary
        // Description
    }

    Box(
        modifier = Modifier
            .width(120.dp)
            .height(110.dp),
        contentAlignment = Alignment.Center
    ) {
        MarketGauge(
            score = score,
            modifier = Modifier.size(104.dp)
        )
    }
}
```

The text region and gauge region must have independent constraints.

Do not solve overlap problems using absolute offsets.

Do not move the gauge with arbitrary x/y translation values to make one screenshot look correct.

### Canvas Responsibility

Prefer using Canvas only for graphics.

Use Compose Text for score values, labels, and descriptions whenever practical.

Preferred structure:

```kotlin
Box(
    modifier = modifier,
    contentAlignment = Alignment.Center
) {
    Canvas(
        modifier = Modifier.fillMaxSize()
    ) {
        // Draw arcs, ticks, background tracks, or markers only.
    }

    Column(
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text(text = score.toString())
        Text(text = label)
    }
}
```

This makes typography, accessibility, localization, and alignment more reliable.

If text must be drawn on Canvas for a specific visual reason, document why.

### Gauge Information Hierarchy

Do not place every value inside the gauge.

Keep only the primary score and primary state label in the central area when appropriate.

Example:

```text
61
상승 우위
```

Secondary information should remain outside the gauge.

Example:

```text
8/14 대비 +1
```

This prevents crowding and improves localization behavior.

### Gauge and Chart Overflow Validation

After implementation, verify:

- no overlap with adjacent text
- no clipping at Card edges
- no clipping at Canvas bounds
- no text collision inside the gauge
- no label collision under large font scale
- no visual expansion caused by fillMaxSize
- no unexpected stretching on large devices
- no compression on compact devices
- no bottom navigation overlap
- no system inset overlap

Any overlap or clipping means the screen is not complete.

### Responsive Visual Component Rules

Do not design a gauge for only one Android device.

Prefer:

- bounded dp dimensions
- weight for surrounding flexible text
- BoxWithConstraints when the design genuinely needs width-aware behavior
- aspectRatio for graphics that must remain proportional
- explicit min and max sizes when needed

Avoid:

- raw pixel positions
- arbitrary screenWidth multipliers
- hard-coded absolute offsets
- negative offsets used to repair layout
- Canvas sizes derived from the entire screen when the component belongs to a Card

### Visual Component Screenshot Validation

For screens containing gauges, charts, progress arcs, score rings, donut charts, or custom Canvas graphics, screenshot comparison is mandatory.

Compare:

- component center position
- component width
- component height
- arc radius
- stroke width
- start and end angles
- score baseline
- state label baseline
- spacing between graphics and text
- surrounding Card padding
- relation to neighboring sections

Do not mark the screen complete because the score value is correct.

Visual placement is part of the feature.

### Visual Regression Failure Conditions

The UI task is incomplete if any of the following occurs:

- component overlap
- gauge overlaps text
- chart overlaps labels
- text appears inside the wrong region
- Canvas exceeds its parent
- Card height changes unexpectedly because of the visual component
- important information becomes hidden below the fold because a gauge is oversized
- fixed bottom navigation is covered
- component alignment materially differs from the reference
- visual hierarchy differs from the reference
- compact width breaks the layout
- large font scale breaks the layout

### Visual Component Review Checklist

Before completing a screen containing a custom visual component, verify:

- [ ] Dedicated layout region exists
- [ ] Parent constraints are explicit
- [ ] Gauge or chart size is bounded
- [ ] Canvas does not control unrelated parent size
- [ ] Text region and graphic region do not overlap
- [ ] Score and state labels are readable
- [ ] Secondary information is not unnecessarily placed inside the gauge
- [ ] Compact phone width is validated
- [ ] Standard phone width is validated
- [ ] Large font scale is validated
- [ ] Screenshot has been compared to the iOS reference
- [ ] Position is visually consistent
- [ ] Size is visually consistent
- [ ] Spacing is visually consistent
- [ ] No clipping is present
- [ ] No absolute offset workaround was used without documented reason


## Architecture Mapping

Use idiomatic Android architecture rather than line-by-line Swift translation.

Recommended mapping:

```text
SwiftUI                    Android
------------------------------------------------
Swift                      Kotlin
SwiftUI                    Jetpack Compose
ObservableObject           ViewModel
@Published                 StateFlow
@State                     remember / state
@StateObject               ViewModel
@EnvironmentObject         DI / shared ViewModel
async/await                Kotlin Coroutines
Task                       coroutine launch
Codable                    Kotlin Serialization
URLSession                 Retrofit / OkHttp
UserDefaults               DataStore
Keychain                   Android Keystore
CoreData / SwiftData       Room
StoreKit 2                 Google Play Billing
APNs                       Firebase Cloud Messaging
NavigationStack            Navigation Compose
```

## API Rules

Existing backend API contracts should remain unchanged unless there is a documented incompatibility.

For a shared Laravel backend:

- preserve endpoint paths
- preserve request field names
- preserve response JSON structures
- preserve authentication behavior
- preserve error contract
- preserve pagination behavior
- preserve date formats

Do not change backend APIs merely to make Android implementation easier.

## State Management

Prefer:

```text
Repository
    ↓
ViewModel
    ↓
StateFlow
    ↓
Compose UI
```

Example:

```kotlin
data class StockDetailUiState(
    val isLoading: Boolean = false,
    val data: StockDetail? = null,
    val error: String? = null
)
```

Expose immutable state from the ViewModel.

Do not put networking or business logic directly inside Composables.

## Navigation

Preserve the user flow from iOS while using Android-native implementation mechanics.

Match:

- destination order
- back behavior
- modal presentation intent
- tab selection
- deep-link destination
- state restoration where required

The implementation may differ internally, but the user experience should remain equivalent.

Do not imitate iOS navigation behavior when it conflicts with expected Android back behavior.

## Platform-Specific Features

The following must be reviewed explicitly and must not be mechanically translated:

- Sign in with Apple
- Google Sign-In
- StoreKit subscriptions
- Google Play Billing
- APNs
- Firebase Cloud Messaging
- Keychain
- Android Keystore
- Photos
- Camera
- Microphone
- Background tasks
- Deep links
- Universal Links or App Links
- Widgets
- Permissions
- App lifecycle
- Biometric authentication
- Share sheets
- File pickers
- notification permission flows
- system settings links

For these features, preserve the product behavior while using the correct platform API.

## Screenshot Comparison

After implementing a screen:

1. Run the Android screen at a device size reasonably comparable to the iOS reference.
2. Capture an Android screenshot.
3. Compare it against the iOS reference.
4. Inspect visual differences in:
   - top offset
   - horizontal margins
   - section spacing
   - typography
   - text baselines
   - button heights
   - card dimensions
   - icon positions
   - corner radii
   - image crop
   - gauge position and size
   - chart position and size
   - Canvas clipping or overlap
   - score and label alignment
   - bottom insets
5. Adjust the implementation.
6. Repeat until major differences are removed.
7. Re-test with larger font scale.
8. Re-test on at least one additional Android screen size when practical.
9. Re-test RTL when the app supports RTL languages.

Do not consider the task complete merely because the code compiles.

## Visual Parity Checklist

Before marking a screen complete, verify:

- [ ] Screen structure matches the iOS reference
- [ ] Horizontal margins match
- [ ] Vertical section spacing matches
- [ ] Font family is correct
- [ ] Font sizes match
- [ ] Font weights match
- [ ] Line heights are visually consistent
- [ ] Colors match
- [ ] Corner radii match
- [ ] Borders match
- [ ] Component heights match
- [ ] Icon sizes and positions match
- [ ] Images use the correct aspect ratio and crop
- [ ] Safe-area and system inset spacing is correct
- [ ] Bottom navigation spacing is correct
- [ ] Loading state matches
- [ ] Empty state matches
- [ ] Error state matches
- [ ] Disabled state matches
- [ ] Keyboard does not break the layout
- [ ] Scrolling behavior is appropriate
- [ ] Dark mode is consistent if supported
- [ ] Large font scale does not break essential layout
- [ ] Interactive targets remain accessible
- [ ] Screen reader semantics are appropriate
- [ ] Localized text does not break the layout
- [ ] RTL behavior is correct if supported
- [ ] Compact Android width remains usable
- [ ] Large Android width remains visually balanced
- [ ] Edge-to-edge behavior is correct
- [ ] Gesture navigation does not overlap controls
- [ ] Intentional platform differences are documented

## Code Quality Rules

Always:

- use small reusable Composables
- separate UI from business logic
- avoid duplicated design constants
- preserve existing Android project conventions when they do not conflict with UI parity
- keep state immutable where practical
- use previews for reusable components when useful
- remove unused imports and dead code
- keep names descriptive
- avoid premature abstraction
- use string resources for user-facing text
- preserve accessibility semantics
- use start and end rather than left and right where localization requires it

Do not rewrite unrelated parts of the project.

## Migration Order

When porting a complete feature, prefer this order:

```text
1. Models
2. API DTOs
3. API Service
4. Repository
5. ViewModel / UiState
6. Shared UI Components
7. Design Tokens
8. Screen UI
9. Navigation
10. Platform-specific integration
11. Accessibility review
12. Localization and RTL review
13. Edge-to-edge and inset review
14. Screenshot comparison
15. Multi-device validation
16. Testing
```

Avoid porting the whole application in one uncontrolled pass.

Work screen-by-screen or feature-by-feature.

## Agent Instructions

When asked to port a screen:

1. Locate the requested SwiftUI source.
2. Locate related models, ViewModels, services, and assets.
3. Inspect the iOS screenshot if available.
4. Identify reusable Android components already present.
5. Identify existing design tokens.
6. Determine what must be preserved before changing code.
7. Check localization, RTL, accessibility, and inset requirements.
8. Implement the minimum required files.
9. Build or compile if the environment permits.
10. Fix compile errors caused by the implementation.
11. Compare against the visual reference if screenshot feedback is available.
12. Validate at another Android screen size when practical.
13. Validate larger font scale when practical.
14. Validate RTL when supported.
15. Report:
   - files created
   - files changed
   - platform differences intentionally retained
   - accessibility decisions
   - localization or RTL decisions
   - anything requiring manual verification

Do not silently redesign the screen.

## Recommended Invocation

Example:

```text
Use the iOS to Android UI Parity Skill.

Reference:
ios/StockDetailView.swift

Target:
android/.../StockDetailScreen.kt

Use the existing iOS screenshot as the primary visual source of truth.

Preserve the existing Laravel API contract.

Implement only the files required for this screen and its dependencies.

Do not reinterpret the UI using Material defaults.

Preserve accessibility, localization, RTL support, and Android system behavior.
```

For iterative correction:

```text
Use the iOS to Android UI Parity Skill.

Compare the attached iOS and Android screenshots.

Fix only the visual and platform-parity differences.

Prioritize:
1. layout
2. spacing
3. typography
4. component dimensions
5. colors
6. icon alignment
7. system insets
8. accessibility
9. localization and RTL behavior

Do not modify business logic unless required.
```

## Definition of Done

A migrated screen is complete only when:

1. It builds successfully.
2. It uses the existing backend contract correctly.
3. Core interactions work.
4. Loading, success, empty, and error states behave correctly.
5. Android visual output closely matches the iOS reference.
6. No unnecessary Material styling has been introduced.
7. No unrelated code has been changed.
8. Platform-specific behavior has been explicitly reviewed.
9. Accessibility behavior has been reviewed.
10. Larger font scale does not break essential interactions.
11. Localization has been reviewed.
12. RTL behavior has been reviewed when supported.
13. Edge-to-edge and system insets behave correctly.
14. The layout has been checked on more than one Android screen size when practical.
15. Intentional iOS and Android differences are documented.
16. Custom gauges, charts, progress arcs, donut charts, and Canvas-based components have been visually validated.
17. No visual component overlaps text, escapes its parent bounds, or relies on arbitrary absolute-position fixes.

## Final Goal

The final Android application should feel like the same product as the iOS application.

The implementation does not need to use identical platform components.

The correct target is:

```text
iOS Reference
    ↓
Design Intent
    ↓
Shared Product Rules
    ↓
Platform-Native Implementation
    ↓
Visual and Behavioral Validation
    ↓
Same Product Experience
```

Do not optimize for mechanical source-code conversion.

Optimize for consistent product experience across iOS and Android.
