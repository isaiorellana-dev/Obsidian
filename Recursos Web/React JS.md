# React JS
## Docs
### index
1. [[React JS#Hooks|Hooks]]
2. [[React JS#Patrones de Render|Patrones de Render]]
3. [[React JS#Lazy y Suspense|Lazy y Suspense]]
### Hooks
#### index
- [[React JS#useState|useState]]
- [[React JS#useEffect|useEffect]]
- [[React JS#useContext|useContext]]
- [[React JS#useReducer|useReducer]]
- [[React JS#useMemo|useMemo]]
- [[React JS#useCallback|useCallback]]
- [[React JS#useRef|useRef]]
- [[React JS#useRef forwardRef y useImperativeHandle|useImperativeHandler]]
#### useState
Hook que te permite añadir el estado de React a un componente funcional.
Se compone de dos valores el primero es el estado actual y el segundo es la función que actualiza el estado. Tambien debemos darle un estado inicial.
```jsx
const [state, setState] = useState('initial state')
```
#### useEffect
Este hook al igual que todos son funciones, y en este caso recibe dos parámetros, el primero corresponde a un callback o función y el segundo un arreglo de dependencias.

```jsx
useEffect(() => {
	// code here
	return () => {
		// code to clean the effect here
	}
}, [ // Dependencies array ])
```

El callback no recibe ningún parámetro solamente nos sirve para ejecutar código en el momento de que se produzca el efecto deseado.

Por otro lado, el segundo parámetro es opcional y dependiendo de su valor el efecto se ejecutará.

##### Momentos de ejecución según el valor del segundo parámetro del useEffect
-   **Sin valor**: cuando omitimos este parámetro, el efecto se producirá en el primer renderizado y en cada uno de los subsecuentes (cuando se produce un cambio de estado o las props cambian). Podemos decir que en este caso están combinados los métodos `componentDidMount` y `componentDidUpdate`.
-   **Arreglo vacío [ ]**: cuando le pasamos este valor, el efecto se producirá **únicamente** en el primer renderizado y es equivalente al método `componentDidMount`. Y esto es debido a que el efecto se produce siempre y cuando el valor dentro del arreglo [ ] cambie, y como en este caso al no recibir nada, React ejecuta el efecto una única vez.

-   **Arreglo con dependencias**: los posibles valores de este arreglo pueden ser variables asociadas a un **estado** o **props** del componente, según sea el caso el hook se ejecutará cada vez que sus dependencias cambien.

##### Limpiar el Efecto
Es muy importante limpiar el efecto para evitar problemas en nuestra aplicación, para eso debemos retornar una función dentro del useEffect.
```jsx
  useEffect(() => {
    const interval = setInterval( () => console.log("Algo estoy haciendo"), 1000); // Inicializamos el interval al montarse el componente
    return () => clearInterval(interval); //Eliminamos el interval justo antes de desmontarse el componente
  }, []);
```
#### useContext
Hook que nos ayuda a conectar nuestros componentes de manera facil.

1. Debemos crear el contexto
```js
const ThemeContext = React.createContext(null);
```
2. Usar el provider
```jsx
<ThemeContext.Provider value="blue">
      /*Mi Aplicación*/
</ThemeContext.Provider>
```
3. Consumir el context
```jsx
import ThemeContext from './context/ThemeContext'

const color = useContext(ThemeContext)
```
#### useReducer
- Como useState, pero más escalable
- Implementa una forma más amigables y llena de caracteristicas para trabajar con el estado
- useReducer a menudo es preferible a useState cuando:
    - se tiene una lógica compleja que involucra múltiples subvalores
    - el próximo estado depende del anterior.
```jsx
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  const increase = () => {
	  dispatch({ type: 'increment' })
  }
  const decrease = () => {
	  dispatch({ type: 'increment' })
  }

  return (
    <>
      Count: {state.count}
      <button onClick={increase}>-</button>
      <button onClick={decrease}>+</button>
    </>
  );
}
```
#### useMemo
**[¿Que es Memoization?](https://platzi.com/clases/2118-react-hooks/33472-que-es-memoization-tecnicas-de-optimizacion-en-pro/)**
```jsx
const filteredUsers = useMemo(
    () =>
      characters.filter((user) => {
        return user.name.toLowerCase().includes(search.toLocaleLowerCase());
      }),
    [characters, search]
  );
```
#### useCallback
useCallback guarda una función en memoria, y useMemo también puede guardar una función pero la diferencia esta en que esta retorna también su resultado. Es por eso que useMemo se recomienda para guardar valores y useCallback para guardar funciones.
```js
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```
#### useRef
Sirve para guardar en un objeto una referencia que no va a cambiar entre renderizados.
```jsx
const elementRef = useRef()

return (
	<h1 ref={elementRef} >Hola Mundo</h1>
)
```
#### useRef,  forwardRef y useImperativeHandle
Vamos a usar una referencia en un componente creado con React para utilizar una funcion interna de el componente a referenciar desde un componente padre.
```jsx
// FatherComponent.jsx
const Component = (){
	const elementRef = useRef()

	const handleButton = elementRef.current.toggleVisibility()

	return (
		<>
			<Togglable ref={elementRef} />
			<button onCLick={handleButton} >Cambiar visibilidad del hijo</button>
		</>
	)
}
```
Debemos envolver en forwardRef el componente y con useImperativeHandle enviar la función que queremos utilizar desde el componente padre.
```jsx
// ChildComponent.jsx
import { forwardRef, useImperativeHandle }

const Togglable = forwardRef(({ aqui, las, props }, ref) => {

	const [visibility, setVisibility] = useState(false)
	const toggleVisibility = () => setVisibility(!visibility)

	useImperativeHandle(ref, () => {
		return {
			toggleVisibility
		}
	})
	
	return(
	 // bla bla bla
	)
})
```
### Patrones de Render
#### Render props
Podemos decir que tenemos 2 tipos de props: las props normales que reciben un valor o variable y por otro lado tenemos las props que contienen una función. Estas que contienen una función son las que nos interesan.

Esta función devuelve un componente o un elemento que pudiera tener anidados más elementos y componentes.

```jsx
<TodoList
	onError={(error) => <TodosError error={error} />}
	onLoading={() => <TodosLoading />}
	onEmpty={() => <TodosEmpty />}
	render={() => <TodoItem />}
/>
```

#### Render functions
Es el patrón de entregar la información de nuestro componente en una función.

A continuación tenemos un componente que dentro recibe la prop children:
```jsx
function MyHeader( props ){
	return (
	<header className="header__styles">
		{ props.children }
	</header>
	)
} 
```
Cuando mandemos a llamar ese componente le pasaremos los componentes que debe renderizar a traves de una función.
```jsx
<MyHeader>
	{ () => <myLogo type={ type } /> }

	{
		(text) => <Title title={text} />
	}

</MyHeader>
```

#### High Order Components
##### ¿Que son las High Order Functions?
Son funciones que retornan otras funciones aplicando el concepto funcional `currying`.

Si llamamos a la high order function y le enviamos un parámetro no tendremos todavía un resultado, como está devolviendo otra función tenemos que llamar a esa función que obtenemos luego de llamar a la de orden superior, enviarle los nuevos parámetros que necesita la función de retorno y entonces si, obtendremos nuestro resultado.

Ejemplo:
```js
function highOrderFunction(num1) {
	return function returnFunction(num2){
		return num1 + num2
	}
}

const withSum1 = highOrderFunction(1)

const sumTotal = withSum1(2)

// 3
```

##### HOC
Tenemos una función que espera como parámetro un componente, antes de retornar este componente hacemos el llamado a una API, y al retornar el componente esperado le podemos pasar los datos de la API.
```jsx
function withApi(WrappedComponent) {
	const apiData = fetchApi('https://api.com');
	
	return function WrappedComponentWithApi(props) {
		if (apidData.loading) return <p>Loading</p>;
		return(
			<WrappedComponent data={apiData.json} />
		);
	}
}
```
Con esto si tenemos otro componente que necesita los datos de la API para renderizar información lo único que debemos hacer es utilizar el HOC de la siguiente manera:
```jsx
function TodoBox(props) {
	return (
		<p>
			Tu nombre es {props.data.name}
		</p>
	);
}

const TodoBoxWithApi = withApi(TodoBox);
```
También podemos agregar más “capas” para tener más personalizaciones como por ejemplo:
```jsx
function withApi(apiUrl){
	return function withApiUrl(WrappedComponent) {
		const apiData = fetchApi(apiUrl);
		
		return function WrappedComponentWithApi(props) {
			if (apidData.loading) return <p>Loading</p>;
			return(
				<WrapperdComponent data={apiData.json} />
			);
		}
	}
}
```
Uso del HOC
```jsx
function TodoBox(props) {
	return (
		<p>
			Tu nombre es {props.data.name}
		</p>
	);
}

const TodoBoxWithApi = withApi('https://api.com')(TodoBox);
```
### Lazy y Suspense
```jsx
// This component is loaded dynamically
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // Displays <Spinner> until OtherComponent loads
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```