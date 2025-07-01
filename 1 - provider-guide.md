
# 🧠 Flutter Provider: `read` vs `watch` vs `select`

---

## 🔍 `context.watch<Type>()`
- ✅ Should be called inside the `build` method.
- 👂 Listens to changes in the provider (`ChangeNotifier`).
- 🔄 When `notifyListeners()` is called, widgets using `context.watch` rebuild.

## 🚀 `context.read<Type>()`
- ✅ Can be called anywhere (initState, callbacks, etc).
- ❌ Does **not** listen to changes or rebuild the widget.
- ☑️ Useful for triggering actions (like `clearHistory()`), not for reacting to state.

---

## 🔁 What happens:

| In This Widget                       | What Happens?                          |
|--------------------------------------|----------------------------------------|
| You call `read().clearHistory()`     | 🔄 Calls `notifyListeners()`            |
| This widget uses `read()`            | ❌ Doesn’t rebuild                      |
| Another widget uses `watch()`/Consumer | ✅ Rebuilds automatically             |

---

## ✅ Summary

| Use Case                              | Use `read()` | Rebuilds this widget? | Rebuilds others? |
|--------------------------------------|--------------|------------------------|------------------|
| Call method that updates state       | ✅ Yes        | ❌ No                  | ✅ Yes           |
| Read a changing value to display     | ❌ Use `watch()` | ✅ Yes              | —                |

---

## 🧠 Provider vs Get_It

| Use Case                              | Use `context.watch()` | Use `serviceLocator` |
|--------------------------------------|------------------------|----------------------|
| Rebuild UI on theme/font change      | ✅ Yes                 | ❌ No                |
| Read once (e.g. in `initState`)      | ✅ Yes (or `read()`)   | ✅ Yes               |
| Background service / logic           | ❌ No                  | ✅ Yes               |
| Avoid rebuild for performance        | ❌ No                  | ✅ Yes               |

---

## 🧠 Tip: `read`, `watch`, and `select`

| Method                  | Rebuilds on `notifyListeners()`?         | Best For                            |
|-------------------------|------------------------------------------|-------------------------------------|
| `context.watch<T>()`    | ✅ Yes                                   | Reading reactive values             |
| `context.read<T>()`     | ❌ No                                    | Calling methods / setting values    |
| `context.select<T, R>()`| ✅ Yes (only for selected field)         | Optimized listening to a single field |

---
