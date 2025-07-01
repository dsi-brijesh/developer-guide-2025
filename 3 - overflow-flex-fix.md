# ✅ Flutter Layout: Search Accordion + Scrollable List (Working Pattern)

---

## 🧩 Problem Summary

- Layout: `Column` with:
  - `ExpansionPanelList` as a search/filter section.
  - Scrollable results list (`ListView`).
- Desired behavior:
  - Top widget should:
    - Collapse to header (~10% of screen).
    - Expand to natural height based on child content.
    - Be scrollable when keyboard is visible.
  - Bottom widget should:
    - Take remaining space.
    - Scroll independently.
  - Keyboard must not break layout.

---

## ✅ Final Working Approach

1. Use `LayoutBuilder` to get total body height.
2. Wrap top widget in `AnimatedContainer` to animate height changes.
3. Wrap `ExpansionPanelList` in `SingleChildScrollView`:
   - Supports keyboard interaction.
   - Prevents overflow.
4. Constrain top widget's max height when expanded (e.g., 50% of screen).
5. Wrap bottom widget (`ListView`) in `Expanded` so it fills leftover space.
6. Add `padding: MediaQuery.of(context).viewInsets.bottom` to support keyboard-safe input.

---

## 📐 UI Sizing Reference

- `AppBar` = `kToolbarHeight = 56 px` ≈ 7% of screen
- `BottomNavigationBar` = 56 px ≈ 7% of screen
- Combined non-body space = **~14% of screen height**

---

## 🧠 Key Flutter Takeaways

- ❌ Don’t use `Expanded` on widgets that need dynamic or natural height.
- ✅ Use `BoxConstraints(maxHeight: …)` to limit expanding widgets inside scroll views.
- ✅ Use `AnimatedContainer` for smooth height transitions.
- ✅ Wrap scrollable widgets properly inside `SingleChildScrollView`.
- ✅ Always calculate space dynamically using `LayoutBuilder`.
- ✅ Add `viewInsets.bottom` padding when handling forms with keyboard.

---

## 💡 Extra Tips

- Use `SafeArea` if needed for notch/device padding.
- For responsive behavior on tablets, use `MediaQuery` to adjust max heights.
- Consider `FocusScope.of(context).unfocus()` to dismiss keyboard on outside tap.
