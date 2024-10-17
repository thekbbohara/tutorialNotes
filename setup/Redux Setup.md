---
id: Redux Setup
aliases: []
tags: []
---

-- You can setup your dir structure however you want.
```
src
├ app
│ ├ layout.js
│ ├ provider
│ │ ├ redux
│ │ │ ├ provider.js
│ │ │ └ store
│ │ │   ├ index.js
│ │ │   └slices
│ │ │      └ userSlice.js
... other dirs
```
- slices/userSlice.js

```js
import { createSlice } from '@reduxjs/toolkit'

const initialState = { user: {}, isLoggedIn: false }

const userSlice = createSlice({
  name: 'user',
  initialState,
  reducers: {
    setUser: (state, action) => {
      state.user = action.payload
    },
    setIsLoggedIn: (state, action) => {
      state.isLoggedIn = action.payload
    }
  },
})

export const { setUser, setIsLoggedIn } = userSlice.actions
const userReducer = userSlice.reducer
export default userReducer
```

- store/index.js -> store.js
```jsx
'use client'
import { configureStore } from "@reduxjs/toolkit"
import userReducer from "./slices/userSlice"
export const store = configureStore({
  reducer: {
    user: userReducer
  }
})
```

- redux/provider.jsx
```js
'use client'

const { Provider } = require("react-redux")
const { store } = require("./store")

const ReduxProvider = ({ children }) =>
  <Provider store={store}>
    {children}
  </Provider>

export default ReduxProvider
```

- app/layout.js
```jsx
//other imports
import ReduxProvider from "./provider/redux/provider";
// ... other code
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <bodyclassName='...'>
        <ReduxProvider>
            {children}
        </ReduxProvider>
      </body>
    </html>
  );

```
