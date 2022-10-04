# REDUX

## Ciclo de vida
![Ciclo de Vida](https://static.platzi.com/media/user_upload/ciclo%20de%20vida-b7df006a-cbbe-47dc-9467-c2d7bfbf13bf.jpg)

![Cicle](https://i.ibb.co/PGh3GVS/redux-simple.gif)

## Teoria
### ¿Qué es Redux?
-  Nació en 2015.
-  Es un herramienta para mantener y modificar el estado de nuestra aplicación.
-   El estado es el conjunto de valores almacenados por nuestra app en propiedades o variables en cualquier momento de ejecución.
-   El estado es usado por la UI para mostrar elementos.
-   El estado puede incluir respuestas del servidor, información cacheada, datos generados de forma local, rutas activas, etc.

### ¿Qué necesitamos?

-   Donde almacenar  
    Almacenamiento = store
-   Como acceder  
    Acceso a los datos = selectores
-   Como actualizar  
    Actualización = actions y reducer

### Principio de Redux

1.  Única fuente de la verdad (Store)
    -   El estado es un objeto, normalmente tiene varias capas de profundidad.
    -   Suele ser clave valor (recomendado), aunque también puede ser arreglo.
    -   El store contiene al estado. También contiene a los selectores, al dispatcher, etc.
2.  El estado es de solo lectura (Actions)
    -   La única manera de modificar el estado es a través de actions.
    -   Los actions son los responsables del paso de un estado a otro.
    -   Son objetos que describen que fue lo que paso, el cambio.
3.  Los cambios se realizan con funciones puras (Reducers)
    -   Los reducers son funciones puras que toman el estado anterior y una acción, y devuelven un nuevo estado

Una Función Pura, debe cumplir 3 características:

-   Valor retornado cambia si la entrada cambia.
-   Misma entrada, misma salida ( el resultado depende de los parámetros).
-   Sin efectos colaterales ( no tendrá en cuenta variables globales).

### Reducers:

-   Calcular el nuevo estado basado solo en los parámetros (state, action).
-   No modificar el estado directamente.
-   No tener lógica asíncrona.
## Practica
### Envolver la app
Envolvemos la app con un Provider, creamos el store, aplicamos el compose, el reducer y los middlewares

```jsx
// index.js
import { Provider } from 'react-redux';
import {
  applyMiddleware,
  compose,
  legacy_createStore as createStore,
} from 'redux';
import thunk from 'redux-thunk';
import { logger } from './middlewares';
import rootReducer from './reducers/rootReducer';

// Compose alternativo
const composeAlt = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;
// Middlewares
const composedEnhancers = composeAlt(applyMiddleware(thunk, logger));
// Store
const store = createStore(rootReducer, composedEnhancers);

root.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
);
```
### Consumo de data
Usamos useSelector para acceder al estado y el dispatch para usar los actions
#### Con Redux Toolkit
```jsx
// App.jsx
import { shallowEqual, useDispatch, useSelector } from 'react-redux';
import { fetchPokemonsWithDetails } from './slices/dataSlice';

function App() {
  const pokemons = useSelector((state) => state.data.pokemons, shallowEqual);

  const loading = useSelector((state) => state.ui.loading);

  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchPokemonsWithDetails());
  }, []);

  return (
    <div className='App'>
        <PokemonList pokemons={pokemons} />
    </div>
  );
}

export default App;
```
```jsx
// PokemonCard.jsx
import { setFavorite } from '../slices/dataSlice';
import './PokemonList.css';

const PokemonCard = ({ name, image, types, id, favorite }) => {
  const dispatch = useDispatch();
  const typesString = types.map((elem) => elem.type.name).join(', ');

  const handleOnFavorite = () => {
    dispatch(setFavorite({ pokemonId: id }));
  };

  return (
    <Card
      title={name}
      cover={<img src={image} alt={name} />}
      extra={<StarButton isFavorite={favorite} onClick={handleOnFavorite} />}
    >
      <Meta description={typesString} />
    </Card>
  );
};

export default PokemonCard;
```
#### Tradicional
```jsx
// App.jsx
import { shallowEqual, useDispatch, useSelector } from 'react-redux';
import { getPokemon } from './api';
import { getPokemonsWithDetails, setLoading } from './actions';

function App() {
// Accedemos al estado con useSelector y los metodos de immutable.js, y evitamos errores de comparacion estricta con shallowEqual, pues estamos accediendo a un objeto. 
  const pokemons = useSelector((state) =>
    state.getIn(['data', 'pokemons'], shallowEqual)
  ).toJS();

  const loading = useSelector((state) => state.getIn(['ui', 'loading']));
  const dispatch = useDispatch();
  
  useEffect(() => {
    const fetchPokemons = async () => {
      dispatch(setLoading(true));
      const pokemonsRes = await getPokemon();
      dispatch(getPokemonsWithDetails(pokemonsRes));
      dispatch(setLoading(false));
    };
	fetchPokemons();
  }, []);

  return (
	<div>
		<PokemonList pokemons={pokemons} />
	</div>
	  )
  }
```
```jsx
// PokemonCard.jsx
import { useDispatch } from 'react-redux';
import { setFavorite } from '../actions';

const PokemonCard = ({ name, image, types, id, favorite }) => {

  const dispatch = useDispatch();
  
  const typesString = types.map((elem) => elem.type.name).join(', ');
  
  const handleOnFavorite = () => {
    dispatch(setFavorite({ pokemonId: id }));
  };

  return (
    <Card
      title={name}
      cover={<img src={image} alt={name} />}
      extra={<StarButton isFavorite={favorite} onClick={handleOnFavorite} />}
    >
      <Meta description={typesString} />
    </Card>
  );
};
export default PokemonCard;
```
### Middlewares
```js
// middlewares/index.js
export const logger = (store) => (next) => (action) => {
  console.log(action);
  next(action);
};

export const featuring = (store) => (next) => (actionInfo) => {
  const featured = [{ name: 'eddie' }, ...actionInfo.action.payload];
  const updatedActionInfo = {
    ...actionInfo,
    action: { ...actionInfo.action, payload: featured },
  };
  next(updatedActionInfo);
};
```

### Reducers
Reducer combinado para mejorar la experiencia de desarrollo dividiendo el codigo en diferentes archivos segun el tipo de tarea que hagamos.
#### Con Redux Toolkit
##### Reducer combinador
```js
// rootReducer.js
import { combineReducers } from 'redux';
import dataReducer from '../slices/dataSlice';
import uiReducer from '../slices/uiSlice';

const rootReducer = combineReducers({
	data: dataReducer, ui: uiReducer, 
});

export default rootReducer;
```
##### Slices
###### Slice para la UI
```js
// uiSlice.js
import { createSlice } from '@reduxjs/toolkit';

const initialState = {
  loading: false,
};

export const uiSlice = createSlice({
  name: 'ui',
  initialState,
  reducers: {
    setLoading: (state, action) => {
      state.loading = action.payload;
    },
  },
});

export const { setLoading } = uiSlice.actions;

export default uiSlice.reducer;
```
###### Slice para la data
```js
// dataSlice.js
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import { getPokemon, getPokemonDetails } from '../api';
import { setLoading } from './uiSlice';

const initialState = {
  pokemons: [],
};

export const fetchPokemonsWithDetails = createAsyncThunk(
  'data/fetchPokemonsWithDetails',
  async (_, { dispatch }) => {
    dispatch(setLoading(true));
    const pokemonsRes = await getPokemon();
    const pokemonsDetailed = await Promise.all(
      pokemonsRes.map((pokemon) => getPokemonDetails(pokemon))
    );
    dispatch(setPokemons(pokemonsDetailed));
    dispatch(setLoading(false));
  }
);

export const dataSlice = createSlice({
  name: 'data',
  initialState,
  reducers: {
    setPokemons: (state, action) => {
      state.pokemons = action.payload;
    },
    setFavorite: (state, action) => {
      const currentPokemonIndex = state.pokemons.findIndex((pokemon) => {
        return pokemon.id === action.payload.pokemonId;
      });

      if (currentPokemonIndex >= 0) {
        const isFavorite = state.pokemons[currentPokemonIndex].favorite;

        state.pokemons[currentPokemonIndex].favorite = !isFavorite;
      }
    },
  },
});

export const { setFavorite, setPokemons } = dataSlice.actions;

export default dataSlice.reducer;
```
#### Manera tradicional
##### Reducer combinador
```js
// rootReducer.js
import { combineReducers } from 'redux-immutable';
import { pokemonsReducer } from './pokemons';
import { uiReducer } from './ui';
// Combinamos los reducers en un solo objeto.
const rootReducer = combineReducers({
  data: pokemonsReducer,
  ui: uiReducer,
});

export default rootReducer;
```
##### Reducer para la data
```js
// pokemons.js
import { fromJS } from 'immutable';
import { SET_FAVORITE, SET_POKEMONS } from '../actions/types';

const initialState = fromJS({
  pokemons: [],
});

export const pokemonsReducer = (state = initialState, action) => {
  switch (action.type) {
    case SET_POKEMONS:
      return state.setIn(['pokemons'], fromJS(action.payload));
    case SET_FAVORITE:
      const currentPokemonIndex = state.get('pokemons').findIndex((pokemon) => {
        return pokemon.get('id') === action.payload.pokemonId;
      });

      if (currentPokemonIndex < 0) {
        return state;
      }
      const isFavorite = state.getIn([
        'pokemons',
        currentPokemonIndex,
        'favorite',
      ]);
      return state.setIn(
        ['pokemons', currentPokemonIndex, 'favorite'],
        !isFavorite
      );
    default:
      return state;
  }
};
```
##### Reducer para la UI
```js
import { fromJS } from 'immutable';
import { SET_LOADING } from '../actions/types';

const initialState = fromJS({
  loading: false,
});  

export const uiReducer = (state = initialState, action) => {
  switch (action.type) {
    case SET_LOADING:
      return state.setIn(['loading'], action.payload);
    default:
      return state;
  }
};
```
### Actions (Tradicional)
```js
// actions/index.js
import { getPokemonDetails } from '../api';
import { SET_FAVORITE, SET_LOADING, SET_POKEMONS } from './types';

export const setPokemons = (payload) => ({
  type: SET_POKEMONS,
  payload,
});

export const setLoading = (payload) => ({
  type: SET_LOADING,
  payload,
});

export const setFavorite = (payload) => ({
  type: SET_FAVORITE,
  payload,
});

export const getPokemonsWithDetails =
  (pokemons = []) =>
  async (dispatch) => {
    const pokemonsDetailed = await Promise.all(
      pokemons.map((pokemon) => getPokemonDetails(pokemon))
    );
    dispatch(setPokemons(pokemonsDetailed));
  };
```
```js
// types.js
export const SET_POKEMONS = 'SET_POKEMONS';
export const SET_LOADING = 'SET_LOADING';
export const SET_FAVORITE = 'SET_FAVORITE';
```
### API
```js
// api/index.js
import axios from 'axios';

export const getPokemon = () => {
  return axios
    .get('https://pokeapi.co/api/v2/pokemon?limit=151')
    .then((res) => res.data.results)
    .catch((err) => console.log(err));
};

export const getPokemonDetails = (pokemon) => {
  return axios.get(pokemon.url)
  .then(res => res.data)
  .catch((err) => console.log(err));
}
```