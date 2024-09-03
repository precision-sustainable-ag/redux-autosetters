# redux-autosetters

**redux-autosetters** provides automatic setters and getters for all properties in the Redux store.

Example:

<table>
  <thead>
    <tr><th>Without redux-autosetters<th>With redux-autosetters
  </thead>
  <tbody>
    <tr>
      <td style="vertical-align: top">

**store.jsx**

```javascript
import {
  configureStore,
  createSlice,
} from '@reduxjs/toolkit';

const temperatureSlice = createSlice({
  name: 'temperature',
  initialState: { temperature: 0 },
  reducers: {
    setTemperature: (state, action) => {
      state.temperature = +action.payload;
    }
  }
});

export const { setTemperature } = temperatureSlice.actions;

export const store = configureStore({
  reducer: temperatureSlice.reducer,
});
```
</td>
<td zstyle="vertical-align: top">

**store.jsx**

```javascript
import { createStore } from './redux-autosetters';

const initialState = { temperature };

export const store = createStore(initialState, {});
export { set, get } from './redux-autosetters';
&#8203;
&#8203;
&#8203;
&#8203;
&#8203;
&#8203;
&#8203;
&#8203;
&#8203;
&#8203;
&#8203;
&#8203;
&#8203;
&#8203;
```

<tr>
  <td style="vertical-align: top">

**app.jsx**

```javascript
import {
  Provider, useDispatch, useSelector,
} from 'react-redux';

import { store, setTemperature } from './store';

const Temperature = () => {
  const { temperature } = useSelector((state) => state);
  const dispatch = useDispatch();

  return (
    <div>
      Temperature:
      <input
        type="number"
        value={temperature}
        onChange={(e) => {
          dispatch(setTemperature(+e.target.value))
        }}
      />
    </div>
  );
};

const App = () => (
  <Provider store={store}>
    <Temperature />
  </Provider>
);

export default App;
```
</td>
<td style="vertical-align: top">

**app.jsx**

```javascript
import {
  Provider, useDispatch, useSelector
} from 'react-redux';

import { store, set } from './store';

const Temperature = () => {
  const { temperature } = useSelector((state) => state);
  const dispatch = useDispatch();

  return (
    <div>
      Temperature:
      <input
        type="number"
        value={celsius}
        onChange={(e) => {
          dispatch(set.temperature(+e.target.value))
        }}
      />
    </div>
  );
};

const App = () => (
  <Provider store={store}>
    <Temperature />
  </Provider>
);

export default App;
```
  </tbody>
</table>

Here's an example temperature converter:

## Without redux-autosetters

**store.jsx**

```javascript
import { configureStore, createSlice } from '@reduxjs/toolkit';

const temperatureSlice = createSlice({
  name: 'temperature',
  initialState: { celsius: 0, fahrenheit: 32, kelvin: 273.15 },
  reducers: {
    setCelsius: (state, action) => {
      const celsius = +action.payload;
      state.celsius = celsius;
      state.fahrenheit = (celsius * 9/5) + 32;
      state.kelvin = celsius + 273.15;
    },
    setFahrenheit: (state, action) => {
      const fahrenheit = +action.payload;
      const celsius = (fahrenheit - 32) * 5/9;
      state.celsius = celsius;
      state.fahrenheit = fahrenheit;
      state.kelvin = celsius + 273.15;
    },
    setKelvin: (state, action) => {
      const kelvin = +action.payload;
      const celsius = kelvin - 273.15;
      state.celsius = celsius;
      state.fahrenheit = (celsius * 9/5) + 32;
      state.kelvin = kelvin;
    }
  }
});

export const { setCelsius, setFahrenheit, setKelvin } = temperatureSlice.actions;

export const store = configureStore({
  reducer: temperatureSlice.reducer,
});
```

**app.jsx**
```javascript
import { Provider, useDispatch, useSelector } from 'react-redux';
import { store, setCelsius, setFahrenheit, setKelvin } from './store';

const TemperatureConverter = () => {
  const { celsius, fahrenheit, kelvin } = useSelector((state) => state);
  const dispatch = useDispatch();

  return (
    <div>
      Celsius:
      <input
        type="number"
        value={celsius}
        onChange={(e) => dispatch(setCelsius(+e.target.value))}
      />
      <br />
      Fahrenheit:
      <input
        type="number"
        value={fahrenheit}
        onChange={(e) => dispatch(setFahrenheit(+e.target.value))}
      />
      <br />
      Kelvin:
      <input
        type="number"
        value={kelvin}
        onChange={(e) => dispatch(setKelvin(+e.target.value))}
      />
    </div>
  );
};

const App = () => (
  <Provider store={store}>
    <TemperatureConverter />
  </Provider>
);

export default App;
```

## With redux-autosetters

**store.jsx**

```javascript
import { createStore } from './redux-autosetters';

const initialState = { celsius: 0, fahrenheit: 32, kelvin: 273.15 };

export const store = createStore(initialState, {});
export { set, get } from './redux-autosetters';
```

**app.jsx**

```javascript
import { Provider, useDispatch, useSelector } from 'react-redux';
import { store, set } from './store';

const TemperatureConverter = () => {
  const { celsius, fahrenheit, kelvin } = useSelector((state) => state);
  const dispatch = useDispatch();

  return (
    <div>
      Celsius:
      <input
        type="number"
        value={celsius}
        onChange={(e) => {
          const celsius = +e.target.value;
          dispatch(set.celsius(celsius));
          dispatch(set.fahrenheit((celsius * 9/5) + 32));
          dispatch(set.kelvin(celsius + 273.15));
        }}
      />
      <br />
      Fahrenheit:
      <input
        type="number"
        value={fahrenheit}
        onChange={(e) => {
          const fahrenheit = +e.target.value;
          const celsius = (fahrenheit - 32) * 5/9;
          dispatch(set.celsius(celsius));
          dispatch(set.fahrenheit(fahrenheit));
          dispatch(set.kelvin(celsius + 273.15));
        }}
      />
      <br />
      Kelvin:
      <input
        type="number"
        value={kelvin}
        onChange={(e) => {
          const kelvin = +e.target.value;
          const celsius = kelvin - 273.15;
          dispatch(set.celsius(celsius));
          dispatch(set.fahrenheit((celsius * 9/5) + 32));
          dispatch(set.kelvin(kelvin));
        }}
      />
    </div>
  );
};

const App = () => (
  <Provider store={store}>
    <TemperatureConverter />
  </Provider>
);

export default App;
```

**Notice how the calculations have shifted from the store to the component.**

However, this isn't strictly necessary. With **redux-autosetters**, you can keep these calculations in the store by using functional properties — something typically not allowed in standard Redux:

```javascript
const initialState = {
  celsius: (state) => ((state.fahrenheit - 32) * 5) / 9 || 0,
  kelvin: (state) => state.celsius + 273.15,
  fahrenheit: (state) => ((state.kelvin - 273.15) * 9) / 5 + 32,
};
```

Notice the use of `||` to set the default value for `celsius`. Defaults weren't provided for `kelvin` or `fahrenheit`, because **redux-autosetters** will automatically initialize them based on the `celsius` value.

* When `celsius` is updated, `kelvin` is calculated since it depends on `celsius`.  Then, `fahrenheit` is calculated based on `kelvin`.
* When `kelvin` is updated, `fahrenheit` is calculated since it depends on `kelvin`.  Then, `celsius` is calculated based on `fahrenheit`.
* When `fahrenheit` is updated, `celsius` is calculated since it depends on `fahrenheit`.  Then, `kelvin` is calculated based on `celsius`.

To avoid infinite loops, **redux-autosetters** ensures that a property won't be recalculated if it’s already being used in the current calculation.

For example, when `celsius` is changed, it calculates `kelvin`, which calculates `fahrenheit`. However, this change to `fahrenheit` will not cause another recalculation of `celsius`.

Using functional properties, the component can now be written like this:

```javascript
const TemperatureConverter = () => {
  const { celsius, fahrenheit, kelvin } = useSelector((state) => state);
  const dispatch = useDispatch();

  return (
    <div>
      Celsius:
      <input
        type="number"
        value={celsius}
        onChange={(e) => dispatch(set.celsius(+e.target.value))}
      />
      <br />
      Fahrenheit:
      <input
        type="number"
        value={fahrenheit}
        onChange={(e) => dispatch(set.fahrenheit(+e.target.value))}
      />
      <br />
      Kelvin:
      <input
        type="number"
        value={kelvin}
        onChange={(e) => dispatch(set.kelvin(+e.target.value))}
      />
    </div>
  );
};
```

### Side-by-side comparison (best viewed in VS Code):

<table>
  <thead>
    <tr><th>Without redux-autosetters<th>With redux-autosetters
  </thead>
  <tbody>
    <tr>
      <td style="vertical-align: top">

**store.jsx**

```javascript
import { configureStore, createSlice } from '@reduxjs/toolkit';

const temperatureSlice = createSlice({
  name: 'temperature',
  initialState: { celsius: 0, fahrenheit: 32, kelvin: 273.15 },
  reducers: {
    setCelsius: (state, action) => {
      const celsius = +action.payload;
      state.celsius = celsius;
      state.fahrenheit = (celsius * 9/5) + 32;
      state.kelvin = celsius + 273.15;
    },
    setFahrenheit: (state, action) => {
      const fahrenheit = +action.payload;
      const celsius = (fahrenheit - 32) * 5/9;
      state.celsius = celsius;
      state.fahrenheit = fahrenheit;
      state.kelvin = celsius + 273.15;
    },
    setKelvin: (state, action) => {
      const kelvin = +action.payload;
      const celsius = kelvin - 273.15;
      state.celsius = celsius;
      state.fahrenheit = (celsius * 9/5) + 32;
      state.kelvin = kelvin;
    }
  }
});

export const { setCelsius, setFahrenheit, setKelvin } = temperatureSlice.actions;

export const store = configureStore({
  reducer: temperatureSlice.reducer,
});
```
</td>
<td style="vertical-align: top">

**store.jsx**

```javascript
import { createStore } from './redux-autosetters';

const initialState = {
  celsius: (state) => ((state.fahrenheit - 32) * 5) / 9 || 0,
  kelvin: (state) => state.celsius + 273.15,
  fahrenheit: (state) => ((state.kelvin - 273.15) * 9) / 5 + 32 || 32,
};

export const store = createStore(initialState, {});
export { set, get } from './redux-autosetters';
```

<tr>
  <td style="vertical-align: top">

**app.jsx**

```javascript
import { Provider, useDispatch, useSelector } from 'react-redux';
import { store, setCelsius, setFahrenheit, setKelvin } from './store';

const TemperatureConverter = () => {
  const { celsius, fahrenheit, kelvin } = useSelector((state) => state);
  const dispatch = useDispatch();

  return (
    <div>
      Celsius:
      <input
        type="number"
        value={celsius}
        onChange={(e) => dispatch(setCelsius(+e.target.value))}
      />
      <br />
      Fahrenheit:
      <input
        type="number"
        value={fahrenheit}
        onChange={(e) => dispatch(setFahrenheit(+e.target.value))}
      />
      <br />
      Kelvin:
      <input
        type="number"
        value={kelvin}
        onChange={(e) => dispatch(setKelvin(+e.target.value))}
      />
    </div>
  );
};

const App = () => (
  <Provider store={store}>
    <TemperatureConverter />
  </Provider>
);

export default App;
```
</td>
<td style="vertical-align: top">

**app.jsx**

```javascript
import { Provider, useDispatch, useSelector } from 'react-redux';
import { store, set } from './store';

const TemperatureConverter = () => {
  const { celsius, fahrenheit, kelvin } = useSelector((state) => state);
  const dispatch = useDispatch();

  return (
    <div>
      Celsius:
      <input
        type="number"
        value={celsius}
        onChange={(e) => dispatch(set.celsius(+e.target.value))}
      />
      <br />
      Fahrenheit:
      <input
        type="number"
        value={fahrenheit}
        onChange={(e) => dispatch(set.fahrenheit(+e.target.value))}
      />
      <br />
      Kelvin:
      <input
        type="number"
        value={kelvin}
        onChange={(e) => dispatch(set.kelvin(+e.target.value))}
      />
    </div>
  );
};

const App = () => (
  <Provider store={store}>
    <TemperatureConverter />
  </Provider>
);

export default App;
```
  </tbody>
</table>