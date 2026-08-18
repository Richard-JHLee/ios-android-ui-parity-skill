iOS to Android UI Parity Skill

Purpose

Convert existing SwiftUI iOS screens into Android Jetpack Compose while preserving the original product design, layout, behavior, information hierarchy, and user experience as closely as practical.

The goal is product and UI parity, not a Material Design reinterpretation.

Use the iOS implementation as the reference implementation and the iOS screenshot as the primary visual source of truth.

Exact pixel matching is not more important than Android usability, accessibility, system behavior, localization, or platform requirements.

When to Use

Use this skill when:

Porting an existing SwiftUI screen to Android Jetpack Compose

Rebuilding an iPhone app screen on Android

Matching Android UI to an existing iOS screenshot

Fixing visual differences between iOS and Android

Converting SwiftUI layouts, components, states, and navigation to Compose

Creating shared Android design tokens based on an existing iOS design system

Migrating an existing native iOS application to a native Android application

Validating whether the Android version feels like the same product as the iOS version

Do not use this skill when the goal is to redesign the app specifically for Material Design.

Core Principle

Preserve the product design, not the framework defaults.

Visual parity includes information density, not only colors, shapes, and section order.

If Android shows materially less of the same content above the fold than the iOS reference, parity has failed even when individual components look similar.

Compactness and Information-Density Parity

Before coding, measure the iOS reference and Android screenshot at comparable content widths:

- content horizontal margins
- visible font size, weight, and line height
- text block height and wrapping
- vertical gaps between rows and sections
- card padding, corner radius, and visible height
- grid row height
- visible button and icon dimensions
- amount of shared content visible above the fold
- total height between shared visual anchors

Create a compactness budget before implementation:

| Metric | iOS reference | Android target |
|---|---:|---:|
| Horizontal content margin | measured | within ±2dp equivalent |
| Hero block height | measured | within ±8% |
| Action card height | measured | within ±8% |
| Repeated tile height | measured | within ±8% |
| Shared content above fold | recorded sections | same sections visible |
| Total shared-content height | measured | within ±8% |

Use the budget as a validation constraint, not optional documentation.

Do not declare completion while Android is visibly taller, looser, or shows less shared content than iOS without a documented accessibility or platform requirement.

Do not blindly translate:

SwiftUI Button to Material Button

SwiftUI TextField to Material TextField

SwiftUI card-like views to Material Card

SwiftUI navigation to default Material navigation visuals

SF Symbols directly to Material Icons without visual review

iOS safe-area assumptions directly to Android system insets

Framework-equivalent components often have different:

padding

typography

corner radius

elevation

minimum size

ripple behavior

safe-area behavior

icon geometry

vertical alignment

default colors

interaction behavior

When framework defaults change the appearance or behavior, build or reuse custom Compose components.

Reference Priority

When multiple sources disagree, use this priority:

Product requirements and platform safety requirements

iOS reference screenshot

Current SwiftUI implementation

Existing product design tokens or design specifications

Existing Android project conventions

Material defaults

The screenshot is the primary visual reference, but it must not override accessibility, system behavior, localization, or mandatory Android platform requirements.

Conflict Resolution

When exact iOS visual parity conflicts with Android usability, accessibility, system behavior, localization, or platform requirements:

Preserve product identity.

Preserve information hierarchy.

Preserve layout intent and visual proportions where practical.

Preserve core interaction intent.

Follow Android accessibility requirements.

Follow Android system behavior and platform requirements.

Support localization and RTL correctly.

Do not force pixel-perfect parity when it harms usability.

Document intentional platform differences.

Never silently redesign the product.

The target is the same product experience, not identical rendering at any cost.

Required Workflow

1. Analyze Before Coding

Before creating or changing Android UI, inspect the corresponding SwiftUI screen and identify:

screen hierarchy

sections

component boundaries

horizontal and vertical spacing

padding

alignment

fixed dimensions

flexible dimensions

typography

colors

corner radius

borders

shadows

image scaling

scroll behavior

safe-area behavior

keyboard behavior

loading state

empty state

error state

disabled state

navigation behavior

sheet or dialog behavior

accessibility behavior

localization behavior

RTL behavior if applicable

dynamic type or font-scale implications

Do not begin by guessing an Android layout.

2. Extract Design Tokens

Prefer reusable tokens instead of scattered hard-coded values.

Create or reuse structures such as:

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

Also prefer reusable definitions for:

AppColors

AppTypography

AppSpacing

AppRadius

AppDimensions

AppIcons

AppElevation

AppInsets

If the iOS application already defines explicit values, preserve them unless Android platform requirements require an intentional difference.

Layout Rules

Spacing and Padding

Translate SwiftUI spacing intentionally.

Example:

VStack(spacing: 16) {
    ...
}
.padding(.horizontal, 20)

Preferred Compose equivalent:

Column(
    modifier = Modifier.padding(horizontal = 20.dp),
    verticalArrangement = Arrangement.spacedBy(16.dp)
) {
    ...
}

Do not replace explicit values with Material defaults.

Dimensions

Preserve:

component heights

image dimensions

icon sizes

minimum touch areas where possible

aspect ratios

section spacing

Avoid unnecessary fixed widths if SwiftUI uses flexible layout.

Prefer constraints that reproduce the same layout intent across common Android device widths.

Do not tune a screen only for one test device.

Density and Device Size Rules

Android devices vary more widely in screen size, density, aspect ratio, and font scaling.

Do not assume a single iPhone screenshot maps directly to one Android device.

Validate the screen across at least these layout categories when practical:

compact phone width

standard phone width

large phone width

tall aspect-ratio device

device with gesture navigation

device with three-button navigation if supported by the test environment

Use:

dp for layout dimensions

sp for text dimensions

scalable constraints where the iOS layout is flexible

aspect ratios for media where appropriate

Avoid:

pixel-based layout assumptions

arbitrary screen-width multipliers without design intent

fixed heights for text containers that can break under font scaling

positioning based on one device screenshot only

The iOS screenshot is a visual reference, not a fixed coordinate map.

Android Edge-to-Edge and System Insets

Modern Android applications may render edge-to-edge.

Review all screens for:

status bar

display cutout

navigation bar

gesture area

keyboard

bottom navigation

modal sheets

full-screen media

Use Compose inset APIs intentionally when needed:

Modifier.statusBarsPadding()
Modifier.navigationBarsPadding()
Modifier.imePadding()

Or use appropriate WindowInsets APIs when the project architecture requires them.

Rules:

Do not apply system insets twice.

Do not hard-code status bar heights.

Do not hard-code navigation bar heights.

Verify edge-to-edge behavior on devices with gesture navigation.

Ensure bottom fixed actions remain usable above system gesture areas.

Ensure keyboard appearance does not cover focused input or important actions.

Preserve the visual intent of the iOS safe area while using Android-native inset handling.

Typography Rules

Typography is a major source of cross-platform visual mismatch.

For each text element, preserve:

font family

font size

font weight

line height

letter spacing

text alignment

max lines

truncation behavior

Example:

Text(
    text = title,
    fontSize = 16.sp,
    fontWeight = FontWeight.SemiBold,
    lineHeight = 22.sp
)

Do not assume:

SwiftUI .title2 == MaterialTheme.typography.titleLarge

They are conceptually similar but not visually identical.

If the product uses a custom font such as Pretendard, use the same font family on Android whenever licensing and project configuration permit.

Line Height and Text Vertical Rhythm

Matching font size alone is not sufficient for visual parity.

SwiftUI and Jetpack Compose may produce different text block heights because of differences in font metrics, default line spacing, font padding, and platform text rendering.

For every important text style, validate:

font size

font weight

line height

letter spacing

baseline alignment

paragraph spacing

number of lines

total text block height

Do not rely only on the default Compose line height when visual parity matters.

Prefer defining line height in reusable typography tokens.

Example:


val BodyMedium = TextStyle(

    fontSize = 15.sp,

    lineHeight = 21.sp,

    fontWeight = FontWeight.Normal

)

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
2. Do not reduce font size automatically before checking parent constraints, line height, and font padding. If Material defaults make Android text visibly larger than the reference, an explicit screenshot-derived smaller text token is a valid parity correction.
3. Allow important text to wrap when practical.
4. Keep interactive hit regions large enough for Android accessibility expectations without inflating their visible chrome.
5. Add meaningful contentDescription values where required.
6. Decorative images should not create unnecessary screen reader noise.
7. Preserve semantic grouping and heading intent.
8. Accessibility requirements take priority over exact pixel parity.

If the design cannot support larger font scales without layout changes, document the intentional responsive behavior.

Touch Target Without Visual Inflation

A 48dp Android touch target does not require 48dp visible chrome.

For compact controls:

- preserve the reference icon, label, border, and visible row dimensions
- provide the Android hit target with a transparent parent container
- center the compact visible control inside the larger hit region
- do not increase card padding, section spacing, or visible button height solely to reach 48dp
- do not apply minimum touch height to non-interactive cards, labels, metric tiles, dividers, or text rows
- keep disabled controls visually compact while preserving clear disabled semantics

Example:

```kotlin
Box(
    modifier = Modifier
        .size(48.dp)
        .clickable { onClick() },
    contentAlignment = Alignment.Center
) {
    Icon(
        imageVector = icon,
        contentDescription = label,
        modifier = Modifier.size(14.dp)
    )
}
```

Screenshot-Derived Typography

Do not map SwiftUI text styles directly to Material typography styles.

For each visible text role, record:

- apparent font size
- font weight
- line height
- number of lines
- text block height
- truncation behavior

Explicitly compare Korean glyph placement and numeric baselines.

Material typography is not a minimum size requirement. When the screenshot is more compact, use explicit screen tokens rather than retaining an oversized Material default.

Initial compact token candidates may use:

- caption: 11sp with 14sp line height
- compact body: 13sp with 18sp line height
- section title: 15sp with 20sp line height
- screen title: 20sp with 26sp line height

These are starting points only. Confirm them against screenshot measurements and the product font.

No Material Density Expansion

Do not use Material Card, Button, OutlinedButton, ListItem, or default typography when their intrinsic padding or minimum dimensions make Android larger than the iOS reference.

For screenshot-parity screens:

- build compact visual containers with Box, Row, and Column when needed
- specify visible padding, radius, line height, and icon size intentionally
- use a transparent hit container around compact interactive chrome
- avoid nested surface backgrounds unless they exist in the reference
- avoid 16dp padding as an automatic default
- avoid 24dp icons when the reference uses caption-sized 12–16dp icons
- avoid adding vertical space for Android visual conventions absent from the reference

Android-native interaction behavior is required; Android-default visual density is not.

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

Support dark mode only according to the existing product design.

Check accessible contrast in both light and dark modes.

Component Rules

Buttons

Avoid default Material Button when its internal padding, minimum height, radius, colors, or elevation differs from iOS.

Prefer a custom component when necessary:

@Composable
fun AppPrimaryButton(
    title: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier
)

Preserve:

height

radius

text style

horizontal padding

background

disabled appearance

pressed behavior

Do not reduce touch targets below reasonable Android accessibility expectations merely to match iOS geometry.

Text Fields

Material TextField and OutlinedTextField frequently differ substantially from iOS.

When visual parity is required, use a custom container with BasicTextField if necessary.

Preserve:

height

internal padding

placeholder style

border

focus state

background

cursor

error state

Also verify:

keyboard type

IME action

focus traversal

autofill behavior

password behavior

accessibility label

Cards and Panels

Do not introduce Material Card automatically.

If the iOS view is simply:

background
+
border
+
radius

implement exactly that using Compose modifiers.

Example:

Modifier
    .clip(RoundedCornerShape(12.dp))
    .background(AppColors.Surface)
    .border(
        width = 1.dp,
        color = AppColors.Border,
        shape = RoundedCornerShape(12.dp)
    )

Icons

SF Symbols and Material Icons are not geometrically identical.

Priority:

Existing custom product asset

Equivalent provided Android vector asset

Closest Material icon only if exact parity is not important

Check:

icon size

optical weight

baseline

padding

filled vs outlined style

RTL behavior

accessibility meaning

Complex Visual Components

Charts, gauges, progress arcs, donut charts, score rings, custom indicators, and other Canvas-based visual components require explicit layout validation.

Functional parity alone is not enough for these components.

The Android result must preserve:

component position

component size

visual proportion

surrounding whitespace

relationship to nearby text

information hierarchy

label placement

score placement

clipping behavior

responsive behavior

Analyze the Layout Box Before Coding

Before implementing a complex visual component, identify its dedicated layout region.

Example:

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

Do not place a gauge, chart, or Canvas into a Row or Box without first defining the region that owns it.

The visual component must not overlap adjacent text or expand into another information region.

Dedicated Gauge Components

Create reusable gauge components instead of drawing gauge logic directly inside a screen.

Examples:

MarketGauge
RiskGauge
AiScoreGauge
PortfolioDonut
MomentumGauge

The screen owns layout and positioning.

The gauge component owns rendering.

Do not combine screen positioning logic and gauge drawing logic in the same Composable unless the component is truly screen-specific.

Gauge Size Constraints

A gauge must receive an explicit size or a bounded parent constraint.

Preferred:

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

Avoid an unconstrained visual component.

Do not use this pattern inside a general Row or Card:

Canvas(
    modifier = Modifier.fillMaxSize()
)

unless the parent itself is the intentionally bounded gauge container.

Do not allow the Canvas to determine the size of the surrounding card.

Recommended Gauge Layout

For a text summary and gauge displayed side by side:

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

The text region and gauge region must have independent constraints.

Do not solve overlap problems using absolute offsets.

Do not move the gauge with arbitrary x/y translation values to make one screenshot look correct.

Canvas Responsibility

Prefer using Canvas only for graphics.

Use Compose Text for score values, labels, and descriptions whenever practical.

Preferred structure:

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

This makes typography, accessibility, localization, and alignment more reliable.

If text must be drawn on Canvas for a specific visual reason, document why.

Gauge Information Hierarchy

Do not place every value inside the gauge.

Keep only the primary score and primary state label in the central area when appropriate.

Example:

61
상승 우위

Secondary information should remain outside the gauge.

Example:

8/14 대비 +1

This prevents crowding and improves localization behavior.

Gauge and Chart Overflow Validation

After implementation, verify:

no overlap with adjacent text

no clipping at Card edges

no clipping at Canvas bounds

no text collision inside the gauge

no label collision under large font scale

no visual expansion caused by fillMaxSize

no unexpected stretching on large devices

no compression on compact devices

no bottom navigation overlap

no system inset overlap

Any overlap or clipping means the screen is not complete.

Responsive Visual Component Rules

Do not design a gauge for only one Android device.

Prefer:

bounded dp dimensions

weight for surrounding flexible text

BoxWithConstraints when the design genuinely needs width-aware behavior

aspectRatio for graphics that must remain proportional

explicit min and max sizes when needed

Avoid:

raw pixel positions

arbitrary screenWidth multipliers

hard-coded absolute offsets

negative offsets used to repair layout

Canvas sizes derived from the entire screen when the component belongs to a Card

Visual Component Screenshot Validation

For screens containing gauges, charts, progress arcs, score rings, donut charts, or custom Canvas graphics, screenshot comparison is mandatory.

Compare:

component center position

component width

component height

arc radius

stroke width

start and end angles

score baseline

state label baseline

spacing between graphics and text

surrounding Card padding

relation to neighboring sections

Do not mark the screen complete because the score value is correct.

Visual placement is part of the feature.

Visual Regression Failure Conditions

The UI task is incomplete if any of the following occurs:

component overlap

gauge overlaps text

chart overlaps labels

text appears inside the wrong region

Canvas exceeds its parent

Card height changes unexpectedly because of the visual component

important information becomes hidden below the fold because a gauge is oversized

fixed bottom navigation is covered

component alignment materially differs from the reference

visual hierarchy differs from the reference

compact width breaks the layout

large font scale breaks the layout

Visual Component Review Checklist

Before completing a screen containing a custom visual component, verify:

Dedicated layout region exists

Parent constraints are explicit

Gauge or chart size is bounded

Canvas does not control unrelated parent size

Text region and graphic region do not overlap

Score and state labels are readable

Secondary information is not unnecessarily placed inside the gauge

Compact phone width is validated

Standard phone width is validated

Large font scale is validated

Screenshot has been compared to the iOS reference

Position is visually consistent

Size is visually consistent

Spacing is visually consistent

No clipping is present

No absolute offset workaround was used without documented reason



Android Layout Height and Vertical Alignment Rules

Android UI parity issues frequently come from parent constraints, text metrics, font padding, line height, and alignment behavior rather than from the visible component itself.

When the Android result differs from the iOS reference in card height, row height, gauge placement, progress-bar label position, or vertical centering, do not immediately compensate with arbitrary padding or offsets.

Use the following analysis order:

Parent constraint
    ↓
Child height policy
    ↓
Row / Column / Box alignment
    ↓
Text font metrics
    ↓
lineHeight
    ↓
font padding
    ↓
padding / spacing
    ↓
offset only as a last resort

Fixed Height for Repeated Metric Cards

When multiple cards represent the same type of information, such as market indicators, their visible height should remain consistent unless the reference design clearly requires otherwise.

For repeated cards, prefer one shared dimension token.

Example:

object AppDimensions {
    // 동일 종류의 시장 지표 카드가 항상 같은 높이를 유지하도록 한다.
    val MarketMetricCardHeight = 132.dp
    val EvidenceBarHeight = 28.dp
}

Preferred implementation:

@Composable
fun MarketMetricCard(
    title: String,
    value: String,
    changeText: String,
    modifier: Modifier = Modifier
) {
    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(AppDimensions.MarketMetricCardHeight)
            .clip(RoundedCornerShape(AppRadius.medium))
            .background(AppColors.Surface)
    ) {
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(AppSpacing.md),
            verticalArrangement = Arrangement.SpaceBetween
        ) {
            Text(
                text = title,
                style = AppTypography.MetricLabel
            )

            Text(
                text = value,
                style = AppTypography.MetricValue
            )

            Text(
                text = changeText,
                style = AppTypography.MetricChange
            )
        }
    }
}

For equal-width two-column layouts, use weight for width distribution only and define height independently.

Row(
    modifier = Modifier.fillMaxWidth(),
    horizontalArrangement = Arrangement.spacedBy(AppSpacing.sm)
) {
    MarketMetricCard(
        modifier = Modifier
            .weight(1f)
            .height(AppDimensions.MarketMetricCardHeight),
        title = leftTitle,
        value = leftValue,
        changeText = leftChange
    )

    MarketMetricCard(
        modifier = Modifier
            .weight(1f)
            .height(AppDimensions.MarketMetricCardHeight),
        title = rightTitle,
        value = rightValue,
        changeText = rightChange
    )
}

Do not allow one card to become taller because:

its title wraps differently

its value is missing

its change text is missing

a child uses wrapContentHeight

a child unexpectedly expands through fillMaxHeight

a Spacer differs between card states

a nested Column has different verticalArrangement behavior

If the reference design intentionally supports multiline titles, use a shared minimum height and validate every card state rather than assigning different per-card heights.

Avoid Accidental Height Expansion

When a component appears too tall, inspect the complete Modifier chain from parent to child.

Check for:

fillMaxHeight
fillMaxSize
weight
heightIn
requiredHeight
wrapContentHeight
padding
Spacer
WindowInsets
navigationBarsPadding
statusBarsPadding
imePadding

Do not assume the visible Card owns the extra height.

A child using fillMaxHeight inside an insufficiently bounded parent may produce a different result than expected.

A weight modifier in a Column may consume remaining vertical space.

System inset padding may also be applied twice.

Height Debugging Procedure

When fixing a height mismatch:

Identify the exact component whose top or bottom edge differs from the reference.

Identify its direct parent.

Trace every vertical constraint from the nearest bounded ancestor.

List every fixed height, min height, max height, weight, Spacer, and vertical padding involved.

Determine which element actually owns the final measured height.

Modify the owning constraint instead of compensating in a child.

Re-check compact, standard, and large phone widths.

Re-check fontScale values of at least 1.0 and 1.3 where practical.

Before changing code, the Agent should report the suspected height owner.

Example:

The card itself is not creating the extra height.
The parent Column uses weight(1f), so the card is expanding to fill remaining vertical space.
The fix should be applied to the parent layout constraint rather than by reducing internal padding.

Progress Bar Label Centering

Text rendered inside a horizontal evidence bar, segmented bar, progress bar, score bar, or status bar must be centered using the parent geometry.

Do not visually center text using top padding, bottom padding, baseline offsets, or arbitrary translation values.

Preferred structure:

@Composable
fun EvidenceBar(
    uncertainRatio: Float,
    label: String,
    modifier: Modifier = Modifier
) {
    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(AppDimensions.EvidenceBarHeight)
            .clip(RoundedCornerShape(6.dp)),
        contentAlignment = Alignment.Center
    ) {
        // 막대 배경과 구간은 그래픽 영역만 담당한다.
        Row(
            modifier = Modifier.fillMaxSize()
        ) {
            Box(
                modifier = Modifier
                    .weight(0.10f)
                    .fillMaxHeight()
                    .background(AppColors.Negative)
            )

            Box(
                modifier = Modifier
                    .weight(0.65f)
                    .fillMaxHeight()
                    .background(AppColors.Uncertain)
            )

            Box(
                modifier = Modifier
                    .weight(0.25f)
                    .fillMaxHeight()
                    .background(AppColors.Positive)
            )
        }

        // 텍스트는 같은 Box의 기하학적 중심에 배치한다.
        Text(
            text = label,
            modifier = Modifier.align(Alignment.Center),
            style = AppTypography.EvidenceBarLabel,
            maxLines = 1
        )
    }
}

If separate labels must appear inside individual segments, each segment should own its own centered Box.

Box(
    modifier = Modifier
        .weight(0.65f)
        .fillMaxHeight(),
    contentAlignment = Alignment.Center
) {
    Text(
        text = "불확실 65%",
        style = AppTypography.EvidenceBarLabel,
        maxLines = 1
    )
}

Do not use this pattern to center bar text:

Text(
    text = "불확실 65%",
    modifier = Modifier
        .padding(top = 2.dp)
        .offset(y = 1.dp)
)

That may appear correct on one device while becoming vertically misaligned on another device or font scale.

Row Vertical Alignment

When labels, icons, values, or indicators share one horizontal row, define vertical alignment explicitly.

Row(
    modifier = Modifier
        .fillMaxWidth()
        .height(32.dp),
    verticalAlignment = Alignment.CenterVertically
) {
    // Row 내부 요소는 시각적 중앙을 기준으로 배치한다.
    Text(
        text = label,
        style = AppTypography.Body
    )
}

Do not rely on incidental baseline behavior when the design requires geometric centering.

Use baseline alignment only when the reference specifically depends on text baselines.

Android Font Padding and Text Metrics

Text that appears visually lower than the center may be caused by font metrics even when Alignment.Center is used.

For important compact labels, inspect:

font family

font size

font weight

line height

includeFontPadding

maxLines

baseline

parent height

When font padding is the confirmed cause, use a project-wide typography decision or a narrowly documented style rather than arbitrary per-screen offsets.

Example:

val EvidenceBarLabel = TextStyle(
    fontSize = 13.sp,
    lineHeight = 16.sp,
    fontWeight = FontWeight.SemiBold,
    platformStyle = PlatformTextStyle(
        // Android 기본 폰트 여백이 수직 중앙 정렬에 영향을 주는 경우 사용한다.
        includeFontPadding = false
    )
)

Do not disable font padding globally without validating Korean, English, numbers, and all supported scripts used by the application.

If the application supports multiple languages, verify at minimum representative scripts that can have different font metrics.

Compact Bar Typography Rules

For text placed inside short-height components such as progress bars:

keep lineHeight smaller than or reasonably proportional to the parent height

use maxLines = 1

avoid multiline labels

avoid large top or bottom padding

avoid automatic font-size reduction as a first fix

validate Korean glyph vertical placement

validate numeric glyph vertical placement

validate the selected product font

The parent Box should control geometric centering.

Typography should control text metrics.

Padding should not be used to repair typography.

Screenshot-Based Height and Alignment Validation

When reference and Android screenshots are available, compare using visible edges and center lines.

For repeated cards:

Reference card top
Reference card bottom
Reference card center

Android card top
Android card bottom
Android card center

For a horizontal evidence bar:

Bar top
Bar bottom
Bar vertical center
Text bounding-box center
Text baseline

The Agent must distinguish between:

container center mismatch

text bounding-box mismatch

baseline-only mismatch

font-metric mismatch

Do not modify the entire screen when only one component has a local alignment issue.

Required Visual Checks for Repeated Cards

Before completion verify:

All same-type cards share the same visible height

Left and right cards align at top

Left and right cards align at bottom

Missing-data cards preserve the same container height

Wrapped titles do not unexpectedly change grid rhythm

Card internal padding is consistent

Vertical spacing between rows is consistent

No card uses an undocumented one-off height

Required Visual Checks for Horizontal Bars

Before completion verify:

Bar height matches the reference intent

Text is horizontally centered in its intended segment or container

Text is vertically centered

Text is not corrected using arbitrary top padding

Text is not corrected using arbitrary y offset

Font padding has been reviewed if the label still appears low

lineHeight has been reviewed

Korean text is visually centered

Numeric text is visually centered

Compact width does not clip the label

Larger font scale does not create overlap

UI Correction Priority

When repairing an Android UI parity problem, use this priority:

1. Parent constraints
2. Component dimensions
3. Alignment
4. Typography and lineHeight
5. Font padding
6. Internal padding
7. Offset only when the design truly requires an intentional optical correction

Any use of offset for parity correction must include a code comment explaining why geometric centering is insufficient.

Android UI Anti-Patterns

Do not use these as default fixes:

adding top padding until the screenshot looks correct

adding negative padding or negative offsets

using different heights for visually identical cards

shrinking font size to solve container-height bugs

using fillMaxSize for a child whose parent should determine a bounded region

allowing a Canvas or visual child to determine unrelated parent height

applying navigationBarsPadding or statusBarsPadding in multiple nested layers

assigning one-device-specific dp values without checking another screen size

Agent Instructions for Height and Alignment Bugs

When the user reports that Android height or alignment still differs from the reference:

Locate the exact target Composable.

Locate the immediate parent and nearest bounded ancestor.

Inspect the complete Modifier chain.

Inspect Row, Column, and Box alignments.

Inspect Typography, lineHeight, and font padding.

Identify the actual owner of the measured height.

Explain the root cause before editing.

Apply the smallest structural correction.

Do not modify business logic.

Do not modify unrelated screens.

Build or preview if the environment permits.

Compare the result against the reference screenshot.

Validate a second Android width where practical.

Report the exact file and modifier changed.

For a progress-bar label that appears too low, check in this order:

Box contentAlignment
    ↓
Text modifier alignment
    ↓
parent height
    ↓
lineHeight
    ↓
includeFontPadding
    ↓
internal padding
    ↓
offset as a last resort

For repeated cards with inconsistent or incorrect height, check in this order:

shared dimension token
    ↓
parent Row or Grid constraint
    ↓
card height
    ↓
child fillMaxHeight / weight
    ↓
content padding
    ↓
text wrapping and lineHeight

The Agent must not declare completion until both structural layout and visual output have been validated.

Architecture Mapping

Use idiomatic Android architecture rather than line-by-line Swift translation.

Recommended mapping:

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

API Rules

Existing backend API contracts should remain unchanged unless there is a documented incompatibility.

For a shared Laravel backend:

preserve endpoint paths

preserve request field names

preserve response JSON structures

preserve authentication behavior

preserve error contract

preserve pagination behavior

preserve date formats

Do not change backend APIs merely to make Android implementation easier.

State Management

Prefer:

Repository
    ↓
ViewModel
    ↓
StateFlow
    ↓
Compose UI

Example:

data class StockDetailUiState(
    val isLoading: Boolean = false,
    val data: StockDetail? = null,
    val error: String? = null
)

Expose immutable state from the ViewModel.

Do not put networking or business logic directly inside Composables.

Navigation

Preserve the user flow from iOS while using Android-native implementation mechanics.

Match:

destination order

back behavior

modal presentation intent

tab selection

deep-link destination

state restoration where required

The implementation may differ internally, but the user experience should remain equivalent.

Do not imitate iOS navigation behavior when it conflicts with expected Android back behavior.

Platform-Specific Features

The following must be reviewed explicitly and must not be mechanically translated:

Sign in with Apple

Google Sign-In

StoreKit subscriptions

Google Play Billing

APNs

Firebase Cloud Messaging

Keychain

Android Keystore

Photos

Camera

Microphone

Background tasks

Deep links

Universal Links or App Links

Widgets

Permissions

App lifecycle

Biometric authentication

Share sheets

File pickers

notification permission flows

system settings links

For these features, preserve the product behavior while using the correct platform API.

Screenshot Comparison

After implementing a screen:

Run the Android screen at a device size reasonably comparable to the iOS reference.

Capture an Android screenshot.

Compare it against the iOS reference.

Inspect visual differences in:

top offset

horizontal margins

section spacing

typography

text baselines

button heights

card dimensions

icon positions

corner radii

image crop

gauge position and size

chart position and size

Canvas clipping or overlap

Density Validation — Mandatory

Place the screenshots side by side or overlay them at the same content width.

Record these shared vertical anchors where present:

1. hero top
2. metadata top
3. decomposition or evidence bar top
4. action card top and bottom
5. first section title
6. first repeated grid row bottom

For each anchor, record cumulative vertical drift:

- more than 4dp drift introduced by one component: inspect that component locally
- more than 12dp cumulative drift: parity failure
- more than 8% shared-content height difference: parity failure
- fewer shared sections visible above the fold: parity failure

Fix density mismatches in this order:

1. screenshot-derived font size and line height
2. unnecessary text wrapping
3. visible control dimensions
4. card padding
5. section spacing
6. outer margins

Do not compensate for cumulative drift with offsets.

score and label alignment

bottom insets

Adjust the implementation.

Repeat until major differences are removed.

Re-test with larger font scale.

Re-test on at least one additional Android screen size when practical.

Re-test RTL when the app supports RTL languages.

Do not consider the task complete merely because the code compiles.

Visual Parity Checklist

Before marking a screen complete, verify:

Screen structure matches the iOS reference

Horizontal margins match

Vertical section spacing matches

Font family is correct

Font sizes match

Font weights match

Line heights are visually consistent

Colors match

Corner radii match

Borders match

Component heights match

Icon sizes and positions match

Images use the correct aspect ratio and crop

Safe-area and system inset spacing is correct

Bottom navigation spacing is correct

Loading state matches

Empty state matches

Error state matches

Disabled state matches

Keyboard does not break the layout

Scrolling behavior is appropriate

Dark mode is consistent if supported

Large font scale does not break essential layout

Interactive targets remain accessible

Screen reader semantics are appropriate

Localized text does not break the layout

RTL behavior is correct if supported

Compact Android width remains usable

Large Android width remains visually balanced

Edge-to-edge behavior is correct

Gesture navigation does not overlap controls

Intentional platform differences are documented

Typography

Font family matches design intent

Font size is visually consistent

Font weight is visually consistent

Line height is visually consistent

Letter spacing is visually consistent

Multi-line text height matches the reference intent

Korean text has sufficient vertical breathing room

Text wrapping occurs at an appropriate position

Baselines align correctly with nearby icons and numbers

Larger font scale does not cause clipping or overlap

Code Quality Rules

Always:

use small reusable Composables

separate UI from business logic

avoid duplicated design constants

preserve existing Android project conventions when they do not conflict with UI parity

keep state immutable where practical

use previews for reusable components when useful

remove unused imports and dead code

keep names descriptive

avoid premature abstraction

use string resources for user-facing text

preserve accessibility semantics

use start and end rather than left and right where localization requires it

Do not rewrite unrelated parts of the project.

Migration Order

When porting a complete feature, prefer this order:

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

Avoid porting the whole application in one uncontrolled pass.

Work screen-by-screen or feature-by-feature.

Agent Instructions

When asked to port a screen:

Locate the requested SwiftUI source.

Locate related models, ViewModels, services, and assets.

Inspect the iOS screenshot if available.

Identify reusable Android components already present.

Identify existing design tokens.

Determine what must be preserved before changing code.

Check localization, RTL, accessibility, and inset requirements.

Implement the minimum required files.

Build or compile if the environment permits.

Fix compile errors caused by the implementation.

Compare against the visual reference if screenshot feedback is available.

Validate at another Android screen size when practical.

Validate larger font scale when practical.

Validate RTL when supported.

Report:

files created

files changed

platform differences intentionally retained

accessibility decisions

localization or RTL decisions

anything requiring manual verification

Do not silently redesign the screen.

Recommended Invocation

Example:

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

For iterative correction:

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

Definition of Done

A migrated screen is complete only when:

It builds successfully.

It uses the existing backend contract correctly.

Core interactions work.

Loading, success, empty, and error states behave correctly.

Android visual output closely matches the iOS reference.

Android shows the same shared sections above the fold as the comparable iOS reference, unless a documented accessibility or platform constraint prevents it.

Shared-content height is within the compactness budget, normally ±8% of the iOS reference.

No component introduces more than 4dp unexplained local vertical drift, and cumulative anchor drift stays within 12dp.

Interactive hit targets meet Android accessibility expectations without unnecessarily enlarging visible controls, cards, or section spacing.

Typography and line height were derived from the screenshot rather than accepted from Material defaults.

No unnecessary Material styling has been introduced.

No unrelated code has been changed.

Platform-specific behavior has been explicitly reviewed.

Accessibility behavior has been reviewed.

Larger font scale does not break essential interactions.

Localization has been reviewed.

RTL behavior has been reviewed when supported.

Edge-to-edge and system insets behave correctly.

The layout has been checked on more than one Android screen size when practical.

Intentional iOS and Android differences are documented.

Custom gauges, charts, progress arcs, donut charts, and Canvas-based components have been visually validated.

No visual component overlaps text, escapes its parent bounds, or relies on arbitrary absolute-position fixes.

Final Goal

The final Android application should feel like the same product as the iOS application.

The implementation does not need to use identical platform components.

The correct target is:

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

Do not optimize for mechanical source-code conversion.

Optimize for consistent product experience across iOS and Android.