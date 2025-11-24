# plant-store-redux
Bizni kompaniya uy o'simliklarini sotadi
Файлы и код, связанные с Redux (4 балла): → store.js, reducers/, actions/ papkalarini yarat. → Redux Toolkit (configureStore, createSlice) ishlat.
import { configureStore } from '@reduxjs/toolkit';
import cartReducer from './cartSlice';

export const store = configureStore({
  reducer: {
    cart: cartReducer,
  },
});
import { createSlice } from '@reduxjs/toolkit';

const initialState = {
  items: [], // {id, name, price, thumbnail, quantity}
  totalQuantity: 0,
  totalPrice: 0,
};

const cartSlice = createSlice({
  name: 'cart',
  initialState,
  reducers: {
    addToCart: (state, action) => {
      const product = action.payload;
      const existing = state.items.find(item => item.id === product.id);
      if (!existing) {
        state.items.push({ ...product, quantity: 1 });
        state.totalQuantity += 1;
        state.totalPrice += product.price;
      }
    },
    increaseQuantity: (state, action) => {
      const item = state.items.find(i => i.id === action.payload);
      if (item) {
        item.quantity += 1;
        state.totalQuantity += 1;
        state.totalPrice += item.price;
      }
    },
    decreaseQuantity: (state, action) => {
      const item = state.items.find(i => i.id === action.payload);
      if (item && item.quantity > 1) {
        item.quantity -= 1;
        state.totalQuantity -= 1;
        state.totalPrice -= item.price;
      }
    },
    removeFromCart: (state, action) => {
      const item = state.items.find(i => i.id === action.payload);
      if (item) {
        state.totalQuantity -= item.quantity;
        state.totalPrice -= item.price * item.quantity;
        state.items = state.items.filter(i => i.id !== action.payload);
      }
    },
  },
});

export const { addToCart, increaseQuantity, decreaseQuantity, removeFromCart } = cartSlice.actions;
export default cartSlice.reducer;
import { useDispatch } from 'react-redux';
import { addToCart } from './cartSlice';

const dispatch = useDispatch();
<button onClick={() => dispatch(addToCart(product))}>Добавить в корзину</button>
import { useSelector, useDispatch } from 'react-redux';
import { increaseQuantity, decreaseQuantity, removeFromCart } from './cartSlice';

const { items, totalQuantity, totalPrice } = useSelector(state => state.cart);
