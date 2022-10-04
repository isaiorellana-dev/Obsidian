# React Router
## Teoria
### Tipos de Routers
1. Browser Router
	dominio/ruta
2. Hash Router
	dominio/#/ruta
3. Memory Router
## Hooks
- [[React Router#Usando useRoutes|useRoutes]]
- [[React Router#useParams|useParams]]
- [[React Router#useNavigate|useNavigate]]
- [[React Router#useLocation|useLocation]]
## Docs v6
### Configuración básica
En este ejemplo se usa HashRouter en lugar de BrowserRouter para poder hacer deploy en GitHub Pages. Debido a eso nuestras rutas tendrán un # antes del nombre de la ruta.
```jsx
import { HashRouter, Routes, Route } from 'react-router-dom';
import { Menu } from './Menu';
import { HomePage } from './HomePage';
import { BlogPage } from './BlogPage';
import { ProfilePage } from './ProfilePage';

// /#/ -> Home
// /#/blog
// /#/profile
// /#/lalalala -> Not Found

function App() {
  return (
    <>
      <HashRouter>
// El componente Menu va dentro del Provider para poder usar la navegacion, como Links o poner informacion de la atenticacion
        <Menu />
        
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/blog" element={<BlogPage />} />
          <Route path="/profile" element={<ProfilePage />} />
          <Route path="*" element={<p>Not found</p>} />
        </Routes>
      </HashRouter>
    </>
  );
}

export default App;
```
#### Usando useRoutes
```jsx
import { HashRouter, useRoutes } from'react-router-dom'
import { BlogPage } from'./BlogPage';
import { HomePage } from'./HomePage';
import { Menu } from'./Menu';
import { ProfilePage } from'./ProfilePage';

function Router() {
  const routes = [
    {
      path: '/',
      element: <HomePage />,
    },
    {
      path: '/blog',
      element: <BlogPage />,
    },
    {
      path: '/profile',
      element: <ProfilePage />,
    },
  ]
  const elements = useRoutes(routes)
  return elements
}

functionApp() {
  return (
    <HashRouter>
      <Menu />
      <Router />
    </HashRouter>
  );
}

export default App;
```
### Link y NavLink
#### Link
```jsx
<Link to="/">Home</Link>
```
#### NavLink
```jsx
<NavLink
    style={({ isActive }) => ({
        color: isActive ? 'red' : 'blue',
    })}
    to="/blog"
    >Blog</NavLink>
```
```jsx
const routes = [];
routes.push({
  to: '/',
  text: 'Home',
});
routes.push({
  to: '/blog',
  text: 'Blog',
});
routes.push({
  to: '/profile',
  text: 'Profile',
});
return (
	{routes.map(route => (
          <li>
            <NavLink
              style={({ isActive }) => ({
                color: isActive ? 'red' : 'blue',
              })}
              to={route.to}
            >
              {route.text}
            </NavLink>
          </li>
        ))}
)
```
### useParams
**Sirve para crear rutas dinamicas en la aplicacion, como el link a un post en especifico atraves de un Link.**
En el archivo donde configuramos las rutas debemos crear la ruta con el slug dinámico de la siguiente forma:
```jsx
<Routes>
    <Route path="/blog" element={<BlogPage />} />
    <Route path="/blog/:slug" element={<BlogPost />} />
</Routes>
```
Luego en el elemento debemos utilizar el hook useParams para recibir ese slug:
```jsx
import { useParams } from 'react-router-dom';

function BlogPost() {
  const { slug } = useParams();

  return (
    <>
      <h2>Estamos en la ruta: {slug}</h2>
    </>
  );
}

export { BlogPost };
```
### useNavigate
Al igual que los "Link" este Hook nos permite navegar dentro de la aplicación pero con funciones que hayamos creado.
Tambien le podemos enviar un segundo argumento para transmitirle datos al location.
```jsx
const navigate = useNavigate()

const returnToBlog = () => {
	navigate('/blog');
	// o tambien podemos pasarle un -1 como parametro para que nos lleve a la ruta anterior
	navigate(-1)
	// Puedes pasar un segundo argumento para utilizar en useLocation
	navigate('/home', { state })
}

return(
	<button onClick={returnToBlog} >< /button>
)
```
### useLocation
Contendra la data sobre la ruta en la que estamos y tambien la data que le hayamos pasado con useNavigate()
```jsx
const location = useLocation()
```
### Outlet
Para las Rutas anidadas tenemos 'Outlet'.
Tenemos un componente que queremos renderizar dentro de una pagina pero que solo vamos a mostrar a traves de una ruta anidada.
En nuestra configuración de rutas:
```jsx
<Routes>
  <Route path="/blog" element={<BlogPage />}>
	<Route path=":slug" element={<BlogPost />} />
  </Route>
</Routes>
```
En en componente padre debe ir el Outlet
```jsx
function BlogPage() {
  return (
    <>
      <h1>Blog</h1>
      // Aqui
      <Outlet />
      <ul>
        <li>Hola </>
      </ul>
    </>
  );
}
```
### Navigate
```jsx
<Navigate to="/login" />
```
### Redirects
Redirects a rutas protegidas
```jsx
function AuthRoute(props) {
  const auth = useAuth();

  if (!auth.user) {
    return <Navigate to="/login" />;
  }

  return props.children;
}
```
En nuestra configuración de rutas:
```jsx
<Route
  path="/logout"
  element={
	<AuthRoute>
	  <LogoutPage />
	</AuthRoute>
  }
/>
```