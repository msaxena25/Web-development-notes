# React Native Registration Form

✅ Create a **Txt, Checkbox, Radio group fields**
✅ Pick a **Profile Picture** using **Camera or Gallery**
✅ Get the **Current Location** using GPS
✅ Show success message after saving

---

### 🔧 Dependencies (use Expo for easier setup):

If you're using **Expo**, run:

```bash
npm install react-native-paper react-native-toast-message
npx expo install expo-image-picker expo-location
```

react-native-paper → UI components (Radio, Checkbox)
react-native-toast-message → For showing toast alerts

---

### 📦 Full Working Code (With Camera & Location)

```jsx
import React, { useState, useEffect } from 'react';
import {
  View,
  Text,
  TextInput,
  Button,
  StyleSheet,
  TouchableOpacity,
  Alert,
  Image,
  ScrollView,
} from 'react-native';
import * as ImagePicker from 'expo-image-picker';
import * as Location from 'expo-location';

export default function RegistrationForm() {
  const [form, setForm] = useState({
    name: '',
    email: '',
    password: '',
    gender: '',
    hobbies: [],
    image: null,
    location: null,
  });

  const hobbiesList = ['Reading', 'Music', 'Traveling', 'Gaming'];

  useEffect(() => {
    (async () => {
      const { status: camStatus } = await ImagePicker.requestCameraPermissionsAsync();
      const { status: locStatus } = await Location.requestForegroundPermissionsAsync();
      if (camStatus !== 'granted' || locStatus !== 'granted') {
        Alert.alert('Permissions required', 'Camera and location permissions are needed');
      }
    })();
  }, []);

  const handleImagePick = async () => {
    let result = await ImagePicker.launchCameraAsync({
      allowsEditing: true,
      aspect: [1, 1],
      quality: 0.7,
    });

    if (!result.canceled) {
      setForm({ ...form, image: result.assets[0].uri });
    }
  };

  const handleLocation = async () => {
    let location = await Location.getCurrentPositionAsync({});
    setForm({
      ...form,
      location: {
        latitude: location.coords.latitude,
        longitude: location.coords.longitude,
      },
    });
  };

  const handleHobbyChange = (hobby) => {
    const updated = form.hobbies.includes(hobby)
      ? form.hobbies.filter((h) => h !== hobby)
      : [...form.hobbies, hobby];
    setForm({ ...form, hobbies: updated });
  };

  const handleSubmit = () => {
    if (!form.name || !form.email || !form.password || !form.gender || !form.image || !form.location) {
      Alert.alert('Error', 'All fields, image, and location are required.');
      return;
    }

    console.log('Form Data:', form);
    Alert.alert('Success', 'Registration Successful!');
  };

  return (
    <ScrollView contentContainerStyle={styles.container}>
      <Text style={styles.title}>Registration Form</Text>

      <TextInput
        style={styles.input}
        placeholder="Name"
        value={form.name}
        onChangeText={(text) => setForm({ ...form, name: text })}
      />
      <TextInput
        style={styles.input}
        placeholder="Email"
        keyboardType="email-address"
        value={form.email}
        onChangeText={(text) => setForm({ ...form, email: text })}
      />
      <TextInput
        style={styles.input}
        placeholder="Password"
        secureTextEntry
        value={form.password}
        onChangeText={(text) => setForm({ ...form, password: text })}
      />

      <Text style={styles.label}>Gender</Text>
      <View style={styles.row}>
        {['Male', 'Female'].map((g) => (
          <TouchableOpacity
            key={g}
            style={styles.radio}
            onPress={() => setForm({ ...form, gender: g })}
          >
            <View style={styles.circle}>
              {form.gender === g && <View style={styles.innerCircle} />}
            </View>
            <Text>{g}</Text>
          </TouchableOpacity>
        ))}
      </View>

      <Text style={styles.label}>Hobbies</Text>
      {hobbiesList.map((hobby) => (
        <TouchableOpacity
          key={hobby}
          style={styles.checkbox}
          onPress={() => handleHobbyChange(hobby)}
        >
          <View style={styles.box}>
            {form.hobbies.includes(hobby) && <View style={styles.checked} />}
          </View>
          <Text>{hobby}</Text>
        </TouchableOpacity>
      ))}

      <Text style={styles.label}>Profile Picture</Text>
      <Button title="Capture Image" onPress={handleImagePick} />
      {form.image && <Image source={{ uri: form.image }} style={styles.image} />}

      <Text style={styles.label}>Location</Text>
      <Button title="Get Location" onPress={handleLocation} />
      {form.location && (
        <Text style={styles.locationText}>
          Lat: {form.location.latitude}, Lng: {form.location.longitude}
        </Text>
      )}

      <View style={{ marginTop: 20 }}>
        <Button title="Submit" onPress={handleSubmit} />
      </View>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: { padding: 20, backgroundColor: '#fff', flexGrow: 1 },
  title: { fontSize: 22, fontWeight: 'bold', marginBottom: 20 },
  input: {
    borderWidth: 1, borderColor: '#ccc', padding: 10, marginBottom: 10, borderRadius: 5,
  },
  label: { marginTop: 10, fontWeight: 'bold' },
  row: { flexDirection: 'row', gap: 15, marginVertical: 10 },
  radio: { flexDirection: 'row', alignItems: 'center' },
  circle: {
    height: 20, width: 20, borderRadius: 10, borderWidth: 1, marginRight: 6, justifyContent: 'center', alignItems: 'center',
  },
  innerCircle: {
    height: 12, width: 12, borderRadius: 6, backgroundColor: '#000',
  },
  checkbox: { flexDirection: 'row', alignItems: 'center', marginVertical: 5 },
  box: {
    height: 20, width: 20, borderWidth: 1, marginRight: 8, justifyContent: 'center', alignItems: 'center',
  },
  checked: {
    width: 12, height: 12, backgroundColor: '#000',
  },
  image: { height: 100, width: 100, marginVertical: 10, borderRadius: 10 },
  locationText: { marginTop: 5, fontStyle: 'italic', color: 'green' },
});
```

--- 
## TouchableOpacity

In **React Native**, `TouchableOpacity` is a core component used to **create touchable UI elements** like buttons, list items, or anything that should respond to a tap.

### ✅ Purpose:

It wraps any view or element and makes it **respond to a touch**. It also gives **visual feedback** by reducing the opacity when pressed.

---

### 🔍 Syntax Example:

```jsx
import { TouchableOpacity, Text } from 'react-native';

<TouchableOpacity onPress={() => alert('Pressed!')}>
  <Text>Click Me</Text>
</TouchableOpacity>
```

---

### 🎯 Why use it?

* It gives **fade (opacity) feedback** when pressed.
* Useful for **custom buttons** with styles.
* More flexible than `<Button>` for styling/layout.

---

### ✨ Visual Feedback:

* When the user taps the element, the **opacity decreases**, showing that it's been touched.
* You can customize how it looks before and after pressing.

---

### 🔧 Props:

| Prop            | Description                                    |
| --------------- | ---------------------------------------------- |
| `onPress`       | Function to run when tapped                    |
| `activeOpacity` | Controls how much opacity to apply when tapped |
| `style`         | Custom styles                                  |
| `disabled`      | Disable the touch behavior                     |

---

### 🆚 TouchableOpacity vs TouchableHighlight vs Pressable:

| Component            | Feedback         | Use When                                     |
| -------------------- | ---------------- | -------------------------------------------- |
| `TouchableOpacity`   | Fades (opacity)  | Want simple touch feedback with custom views |
| `TouchableHighlight` | Background color | You want a highlighted background on press   |
| `Pressable`          | Full control     | You need gestures, long press, hover, etc.   |

---

### ✅ Example with Style:

```jsx
<TouchableOpacity
  onPress={() => alert('Submitted!')}
  style={{ backgroundColor: 'blue', padding: 10, borderRadius: 5 }}
  activeOpacity={0.6}
>
  <Text style={{ color: '#fff' }}>Submit</Text>
</TouchableOpacity>
```

-----------------------------

# **React Native product list app** that includes:

✅ Product cards with toggle to switch between **Card** and **List view**
✅ **Pull-to-refresh** to simulate loading new data
✅ Lazy loading on scroll (infinite scroll)
✅ **Modal popup** with product details
✅ Responsive and centered modal

---

### 📦 Dependencies

No external dependencies needed (uses React Native core).

---

### 🧠 Key Features Covered:

* `FlatList` for rendering
* `refreshing`, `onRefresh` for pull-to-refresh
* `onEndReached` for lazy loading
* `Modal` for detail view
* `TouchableOpacity` for interactions

---

### ✅ Full Code Example:

```jsx
import React, { useState, useEffect } from 'react';
import {
  View,
  Text,
  FlatList,
  StyleSheet,
  TouchableOpacity,
  Modal,
  Button,
  RefreshControl,
  ActivityIndicator,
  SafeAreaView,
} from 'react-native';

const generateProducts = (start, count = 10) =>
  Array.from({ length: count }, (_, i) => ({
    id: start + i,
    name: `Product ${start + i}`,
    description: `Description for product ${start + i}`,
    price: `${(start + i) * 10}₹`,
  }));

export default function ProductListScreen() {
  const [products, setProducts] = useState(generateProducts(1, 10));
  const [viewMode, setViewMode] = useState('card'); // or 'list'
  const [refreshing, setRefreshing] = useState(false);
  const [loadingMore, setLoadingMore] = useState(false);
  const [modalVisible, setModalVisible] = useState(false);
  const [selectedProduct, setSelectedProduct] = useState(null);

  const onRefresh = () => {
    setRefreshing(true);
    setTimeout(() => {
      setProducts(generateProducts(1, 10));
      setRefreshing(false);
    }, 1500);
  };

  const loadMore = () => {
    if (loadingMore) return;
    setLoadingMore(true);
    setTimeout(() => {
      const newItems = generateProducts(products.length + 1, 10);
      setProducts([...products, ...newItems]);
      setLoadingMore(false);
    }, 2000);
  };

  const renderItem = ({ item }) => (
    <TouchableOpacity
      style={viewMode === 'card' ? styles.card : styles.listItem}
      onPress={() => {
        setSelectedProduct(item);
        setModalVisible(true);
      }}
    >
      <Text style={styles.productName}>{item.name}</Text>
      <Text>{item.price}</Text>
    </TouchableOpacity>
  );

  return (
    <SafeAreaView style={{ flex: 1, padding: 10 }}>
      <View style={styles.header}>
        <Text style={styles.title}>Product List</Text>
        <TouchableOpacity
          onPress={() => setViewMode(viewMode === 'card' ? 'list' : 'card')}
          style={styles.toggleBtn}
        >
          <Text style={{ color: '#fff' }}>
            View: {viewMode === 'card' ? 'List' : 'Card'}
          </Text>
        </TouchableOpacity>
      </View>

      <FlatList
        data={products}
        keyExtractor={(item) => item.id.toString()}
        renderItem={renderItem}
        key={viewMode === 'card' ? 'card' : 'list'} // force re-render on toggle
        numColumns={viewMode === 'card' ? 2 : 1}
        contentContainerStyle={{ paddingBottom: 20 }}
        refreshControl={
          <RefreshControl refreshing={refreshing} onRefresh={onRefresh} />
        }
        onEndReached={loadMore}
        onEndReachedThreshold={0.2}
        ListFooterComponent={
          loadingMore ? <ActivityIndicator size="small" color="gray" /> : null
        }
      />

      <Modal
        visible={modalVisible}
        transparent
        animationType="fade"
        onRequestClose={() => setModalVisible(false)}
      >
        <View style={styles.modalContainer}>
          <View style={styles.modalContent}>
            <Text style={styles.modalTitle}>{selectedProduct?.name}</Text>
            <Text>{selectedProduct?.description}</Text>
            <Text style={{ marginTop: 10 }}>{selectedProduct?.price}</Text>
            <Button title="OK" onPress={() => setModalVisible(false)} />
          </View>
        </View>
      </Modal>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  header: {
    flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center', marginBottom: 10,
  },
  title: { fontSize: 22, fontWeight: 'bold' },
  toggleBtn: {
    backgroundColor: '#007bff', paddingHorizontal: 12, paddingVertical: 6, borderRadius: 6,
  },
  card: {
    backgroundColor: '#f1f1f1', padding: 10, margin: 5, borderRadius: 8,
    width: '48%',
  },
  listItem: {
    backgroundColor: '#e0f7fa', padding: 15, marginVertical: 6, borderRadius: 8,
    width: '100%',
  },
  productName: { fontWeight: 'bold', fontSize: 16 },
  modalContainer: {
    flex: 1, justifyContent: 'center', alignItems: 'center', backgroundColor: 'rgba(0,0,0,0.5)',
  },
  modalContent: {
    width: 300, padding: 20, backgroundColor: 'white', borderRadius: 10, alignItems: 'center',
  },
  modalTitle: { fontSize: 20, fontWeight: 'bold', marginBottom: 10 },
});
```

---

### 💡 You Can Extend This With:

* Load images for products using `<Image>`
* Add delete/edit options in modal
* Animate toggle with `LayoutAnimation`
* Add toast/snackbar on scroll end

Let me know if you'd like to connect it to an API or add pagination/server-side integration!
