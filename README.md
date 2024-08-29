# redux-autosetters

redux-autosetters provides automatic setters and getters for all properties in the Redux store.

Here's an example temperature converter:

## Without redux-autosetters

**store.jsx**

```javascript
import { configureStore, createSlice } from '@reduxjs/toolkit';

const temperatureSlice = createSlice({
  name: 'temperature',
  initialState: { celsius: 0, fahrenheit: 32 },
  reducers: {
    toCelsius: (state, action) => {
      state.celsius = ((action.payload - 32) * 5) / 9;
      state.fahrenheit = action.payload;
    },
    toFahrenheit: (state, action) => {
      state.celsius = action.payload;
      state.fahrenheit = (action.payload * 9) / 5 + 32;
    },
  },
});

export const { toCelsius, toFahrenheit } = temperatureSlice.actions;

export const store = configureStore({
  reducer: temperatureSlice.reducer,
});
```

**app.jsx**
```javascript
import { Provider, useDispatch, useSelector } from 'react-redux';
import { store, toCelsius, toFahrenheit } from './store';

const TemperatureConverter = () => {
  const { celsius, fahrenheit } = useSelector((state) => state);
  const dispatch = useDispatch();

  return (
    <div>
      Celsius:
      <input
        type="number"
        value={celsius}
        onChange={(e) => dispatch(toFahrenheit(+e.target.value))}
      />
      <br />
      Fahrenheit:
      <input
        type="number"
        value={fahrenheit}
        onChange={(e) => dispatch(toCelsius(+e.target.value))}
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
import { createStore } from 'redux-autosetters';

const initialState = { celsius: 0, fahrenheit: 32 };

export const store = createStore(initialState, {});
export { set, get } from 'redux-autosetters';
```

**app.jsx**

```javascript
import { Provider, useDispatch, useSelector } from 'react-redux';
import { store, set } from './store';

const TemperatureConverter = () => {
  const { celsius, fahrenheit } = useSelector((state) => state);
  const dispatch = useDispatch();

  return (
    <div>
      Celsius:
      <input
        type="number"
        value={celsius}
        onChange={(e) => {
          dispatch(set.celsius(+e.target.value));
          dispatch(set.fahrenheit((+e.target.value * 9) / 5 + 32));
        }}
      />
      <br />
      Fahrenheit:
      <input
        type="number"
        value={fahrenheit}
        onChange={(e) => {
          dispatch(set.fahrenheit(+e.target.value));
          dispatch(set.celsius(((+e.target.value - 32) * 5) / 9));
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

  </tr>
</table>

**Notice how the calculations have shifted from the store to the component.**

However, this isn't strictly necessary. With redux-autosetters, you can keep these calculations in the store by using functional properties—something typically not allowed in standard Redux:

```javascript
const initialState = {
  celsius: (state) => ((state.fahrenheit - 32) * 5) / 9 || 0,
  fahrenheit: (state) => (state.celsius * 9) / 5 + 32 || 32,
};
```

Note the use of `||` to specify default values.

Whenever `celsius` changes, `fahrenheit` will automatically update, and vice versa.

To prevent infinite loops, redux-autosetters ensures that a property won't be recalculated if it’s already being used in the current calculation.

For instance, changing `celsius` recalculates `fahrenheit`, but this update to `fahrenheit` won't trigger another `celsius` recalculation.
However, if `fahrenheit` were referenced in a third functional property, that property _would_ be recalculated.

Using functional properties, the component can now be written like this:

```javascript
<div>
  Celsius:
  <input
    type="number"
    value={celsius}
    onChange={(e) => dispatch(set.celsius(+e.target.value));}
  />
  <br />
  Fahrenheit:
  <input
    type="number"
    value={fahrenheit}
    onChange={(e) => dispatch(set.fahrenheit(+e.target.value));}
  />
</div>
```
