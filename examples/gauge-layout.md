# Example: Gauge Layout

## Avoid

```kotlin
Row {
    Column {
        Text("상승 우위")
        Text("지수 흐름은 상승 쪽입니다.")
    }

    Canvas(Modifier.fillMaxSize()) {
        // gauge
    }
}
```

## Prefer

```kotlin
Row(
    modifier = Modifier.fillMaxWidth(),
    verticalAlignment = Alignment.CenterVertically
) {
    Column(Modifier.weight(1f)) {
        Text("상승 우위")
        Text("지수 흐름은 상승 쪽입니다.")
    }

    Box(
        modifier = Modifier.width(120.dp).height(110.dp),
        contentAlignment = Alignment.Center
    ) {
        MarketGauge(score = 61, modifier = Modifier.size(104.dp))
    }
}
```

Prefer Canvas for graphics and Compose Text for score and labels. Validate no overlap, no clipping, compact width, larger font scale, and screenshot position against the iOS reference.
