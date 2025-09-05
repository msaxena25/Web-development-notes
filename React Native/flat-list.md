# 🔷 What is `FlatList`?

`FlatList` is a **performance-optimized component** for displaying a list of data. It renders only the visible items (plus a small buffer), rather than all items at once — which helps with performance for long lists.

---

## 📦 Basic Usage

```jsx
import { FlatList, Text, View } from 'react-native';

const data = [{ id: '1', title: 'Item 1' }, { id: '2', title: 'Item 2' }];

<FlatList
  data={data}
  renderItem={({ item }) => <Text>{item.title}</Text>}
  keyExtractor={(item) => item.id}
/>
```

---

## 📚 All Key Props of `FlatList`

Here's a complete list of important and commonly used props, categorized:

---

### 🔹 **Required Props**

| Prop           | Type                               | Description                                          |
| -------------- | ---------------------------------- | ---------------------------------------------------- |
| `data`         | `Array<any>`                       | The data to render in the list                       |
| `renderItem`   | `({ item, index }) => JSX.Element` | How to render each item                              |
| `keyExtractor` | `(item, index) => string`          | Unique key for each item (optional if item has `id`) |

---

### 🔹 **Performance Props**

| Prop                        | Type      | Description                                               |
| --------------------------- | --------- | --------------------------------------------------------- |
| `initialNumToRender`        | `number`  | How many items to render initially (default is 10)        |
| `maxToRenderPerBatch`       | `number`  | Maximum items to render per batch                         |
| `windowSize`                | `number`  | Number of viewable items buffer on screen (default is 21) |
| `removeClippedSubviews`     | `boolean` | Unmount off-screen views to save memory                   |
| `updateCellsBatchingPeriod` | `number`  | Time (ms) between rendering batches                       |

---

### 🔹 **List Appearance Props**

| Prop                     | Type           | Description                         |
| ------------------------ | -------------- | ----------------------------------- |
| `ListHeaderComponent`    | `ReactElement` | Element shown at top of the list    |
| `ListFooterComponent`    | `ReactElement` | Element shown at bottom of the list |
| `ItemSeparatorComponent` | `ReactElement` | Component between items             |
| `ListEmptyComponent`     | `ReactElement` | Component when list is empty        |
| `contentContainerStyle`  | `Style`        | Style for the inner container       |

---

### 🔹 **Scroll Control Props**

| Prop                  | Type       | Description                     |
| --------------------- | ---------- | ------------------------------- |
| `horizontal`          | `boolean`  | Show list horizontally          |
| `inverted`            | `boolean`  | Flip the direction of the list  |
| `scrollEnabled`       | `boolean`  | Disable/enable scrolling        |
| `onScroll`            | `function` | Fires on list scroll            |
| `scrollEventThrottle` | `number`   | Throttle scroll event frequency |

---

### 🔹 **Pagination / Infinite Scroll Props**

| Prop                    | Type       | Description                                       |
| ----------------------- | ---------- | ------------------------------------------------- |
| `onEndReached`          | `function` | Called when end is near (for infinite scrolling)  |
| `onEndReachedThreshold` | `number`   | How far from the end (0–1) to call `onEndReached` |
| `refreshing`            | `boolean`  | Show refresh spinner                              |
| `onRefresh`             | `function` | Called on pull-to-refresh                         |

---

### 🔹 **Advanced Control**

| Prop                 | Type       | Description                                     |
| -------------------- | ---------- | ----------------------------------------------- |
| `extraData`          | `any`      | Re-render list when this changes (e.g., state)  |
| `getItemLayout`      | `function` | Improves performance if items have fixed height |
| `numColumns`         | `number`   | For grid-style layout                           |
| `columnWrapperStyle` | `style`    | Style for grid row container                    |

---

## 🧰 FlatList Methods (via Ref)

You can use a `ref` to call methods like:

```js
const flatListRef = useRef();

<FlatList ref={flatListRef} ... />
```

| Method                                 | Description                              |
| -------------------------------------- | ---------------------------------------- |
| `scrollToOffset({ offset, animated })` | Scroll to a specific offset              |
| `scrollToIndex({ index, animated })`   | Scroll to a specific item index          |
| `scrollToItem({ item, animated })`     | Scroll to a specific item (by reference) |
| `recordInteraction()`                  | Used internally to improve rendering     |
| `flashScrollIndicators()`              | Temporarily show scroll indicators       |

---

## 💡 Tips for Large Lists

* Use `getItemLayout` for faster scroll performance (if item height is known).
* Use `initialNumToRender` + `windowSize` wisely to balance speed and memory.
* Always provide a `keyExtractor` for better rendering behavior.
* Avoid inline functions inside `renderItem` if performance matters.

---

## 🧪 Example with All Features

```jsx
<FlatList
  data={data}
  renderItem={({ item }) => <Text>{item.title}</Text>}
  keyExtractor={(item) => item.id}
  ListHeaderComponent={<Text>Header</Text>}
  ListFooterComponent={<Text>End of List</Text>}
  ItemSeparatorComponent={() => <View style={{ height: 1, backgroundColor: '#ccc' }} />}
  ListEmptyComponent={<Text>No items found</Text>}
  onEndReached={() => loadMoreItems()}
  onEndReachedThreshold={0.5}
  refreshing={isRefreshing}
  onRefresh={refreshList}
  horizontal={false}
  numColumns={2}
  contentContainerStyle={{ padding: 10 }}
/>
```

---

# Dynamic change numColumns based on orientation

Yes, you **can absolutely make `numColumns` dynamic** based on the device’s **orientation** in React Native! You'll just need to detect orientation changes and then re-render the `FlatList` with the appropriate number of columns.

---

## ✅ Approach: Use `Dimensions` API

### 🔧 Step-by-step:

1. Use the `Dimensions` API to get screen width and height.
2. Detect orientation (portrait or landscape).
3. Update state and re-render `FlatList` with a different `numColumns`.

---

### 🧪 Example Code

```tsx
import React, { useEffect, useState } from 'react';
import { FlatList, Text, View, Dimensions, StyleSheet } from 'react-native';

const data = Array.from({ length: 20 }, (_, i) => ({ id: i.toString(), title: `Item ${i + 1}` }));

const ResponsiveFlatList = () => {
  const [numColumns, setNumColumns] = useState(getNumColumns());

  useEffect(() => {
    const subscription = Dimensions.addEventListener('change', () => {
      setNumColumns(getNumColumns());
    });

    return () => subscription?.remove?.(); // Cleanup on unmount
  }, []);

  function getNumColumns() {
    const { width, height } = Dimensions.get('window');
    return width > height ? 4 : 2; // landscape: 4 cols, portrait: 2 cols
  }

  return (
    <FlatList
      data={data}
      key={numColumns} // important: forces FlatList to rerender layout
      keyExtractor={(item) => item.id}
      numColumns={numColumns}
      renderItem={({ item }) => (
        <View style={styles.item}>
          <Text>{item.title}</Text>
        </View>
      )}
    />
  );
};
```

----

## List with all performance-related props


Absolutely! Here's a **complete `FlatList` example** in React Native that uses **all the key performance-related props**, including:

* `initialNumToRender`
* `maxToRenderPerBatch`
* `windowSize`
* `removeClippedSubviews`
* `updateCellsBatchingPeriod`
* `getItemLayout`
* `extraData`

---

## ✅ Full Performance-Optimized FlatList Example

```tsx
import React, { useState } from 'react';
import { FlatList, Text, View, StyleSheet } from 'react-native';

const data = Array.from({ length: 1000 }, (_, i) => ({
  id: i.toString(),
  title: `Item ${i + 1}`,
}));

const ITEM_HEIGHT = 60;

export default function PerformanceFlatList() {
  const [selectedId, setSelectedId] = useState(null);

  const renderItem = ({ item }) => {
    const isSelected = item.id === selectedId;
    return (
      <View style={[styles.item, isSelected && styles.selected]}>
        <Text>{item.title}</Text>
      </View>
    );
  };

  return (
    <FlatList
      data={data}
      renderItem={renderItem}
      keyExtractor={(item) => item.id}
      initialNumToRender={10}                // Render 10 items at first
      maxToRenderPerBatch={20}               // Render up to 20 items per batch
      windowSize={5}                         // Render 5 screenfuls worth of items
      removeClippedSubviews={true}           // Remove off-screen views
      updateCellsBatchingPeriod={50}         // Time (ms) between batch renders
      getItemLayout={(data, index) => ({
        length: ITEM_HEIGHT,
        offset: ITEM_HEIGHT * index,
        index,
      })}                                    // Helps with fast scroll performance
      extraData={selectedId}                 // Re-renders when selection changes
    />
  );
}

const styles = StyleSheet.create({
  item: {
    height: ITEM_HEIGHT,
    justifyContent: 'center',
    alignItems: 'center',
    borderBottomWidth: 1,
    borderColor: '#ddd',
    backgroundColor: '#f9f9f9',
  },
  selected: {
    backgroundColor: '#cdeffd',
  },
});
```

---

## 🧠 Why These Props Matter

| Prop                        | Benefit                                                       |
| --------------------------- | ------------------------------------------------------------- |
| `initialNumToRender`        | Controls initial load time vs. responsiveness                 |
| `maxToRenderPerBatch`       | Prevents overloading UI thread when rendering batches         |
| `windowSize`                | Balances memory use vs. scroll fluidity                       |
| `removeClippedSubviews`     | Saves memory by unmounting off-screen items (Android only)    |
| `updateCellsBatchingPeriod` | Controls render pacing to avoid frame drops                   |
| `getItemLayout`             | Speeds up scroll-to-item and layout calculations              |
| `extraData`                 | Ensures re-render when non-data props (like selection) change |

---

## 🔹 What does **"batch"** mean in React Native `FlatList`?

In the context of `FlatList`, a **batch** refers to a **group of list items rendered together in a single rendering cycle** — not all at once, but in **chunks** to improve performance.

---

### 🧠 Why use batching?

Rendering hundreds of items at once can freeze or lag the UI. So `FlatList` renders items **in small batches**, making scrolling and app startup smoother.

---

## 🔧 Example: Batching in Action

Let’s say your list has 1000 items.

You configure:

```js
initialNumToRender={10}
maxToRenderPerBatch={20}
```

Here’s how it behaves:

| Phase             | What Happens                                              |
| ----------------- | --------------------------------------------------------- |
| On mount          | Renders **first 10 items** (initialNumToRender)           |
| After scrolling   | Renders **20 more items per batch** (maxToRenderPerBatch) |
| Until all visible | Continues rendering in **20-item chunks** as you scroll   |

So, instead of dumping 1000 items into memory at once, it handles them **gradually**.

---
