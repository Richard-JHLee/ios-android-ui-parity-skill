# Example: Porting a Screen

## Cursor prompt

```text
Use the iOS to Android UI Parity Skill.

Reference:
ios/StockDetailView.swift

Target:
android/app/src/main/java/.../StockDetailScreen.kt

Also inspect related ViewModel, models, network service, design tokens, and the iOS screenshot.
Preserve the existing API contract and information hierarchy.
Use Android-native system inset handling.
Do not introduce Material defaults that visibly redesign the screen.
Capture an Android screenshot and compare it with the iOS reference.
```
