 # Jest
## Teoria
### ¿Qué es un test?
Como developers tenemos que garantizar que el código escrito cumpla con ciertos 
requisitos/expectativas. Esto lo hacemos por medio de una prueba (test).

 Esto nos asegura:
	1. Nuestro código cumple con el estándar.
	2. Enviamos a producción sin errores.
### Tipos de pruebas
1. Funcionales.
	Pruebas Unitarias.- Se prueban pequeñas partes de nuestro código asegurándonos así que cumplen con lo que se desea. (En una pagina web las pruebas se traducen a probar cada sección de la pagina y todas las interacciones en ellas).
2. No funcionales.
	Con estas testeamos todo lo que no influye a la funcionalidad del producto, como por ejemplo, la accesibilidad, configuración, de rendimiento, entre otras.
## Practica
### Test Básicos
#### Strings
```js
const text = "Hola Mundo"

test("Debe contener la palabra Mundo", () => {
  expect(text).toMatch(/Mundo/)
})
```
[[test.png]]
#### Arrays
```js
const fruits = ["melon", "manzana", "banana", "mango"]

test("¿Tenemos un mango?", () => {
  expect(fruits).toContain('mango')
})
```
#### Números
```js
test("Mayor que", () => {
  expect(10).toBeGreaterThan(9)
})
```
#### Booleanos
```js
test("Verdadero?", () => {
  expect(true).toBeTruthy()
})
```
#### Callback
```js
const reverseString = (str, callback) => {
  callback(str.split('').reverse().join(''));
}

test("Probar un callback", () => {
  reverseString('Hola', (str) => {
    expect(str).toBe('aloH')
  })
})
```
#### Promesas
```js
const reverseString2 = str => {
  return new Promise((resolve, reject) => {
    if (!str) {
      reject(Error('Error'))
    }
    resolve(str.split('').reverse().join(''))
  })
}

test('Probar una promesa', () => {
  return reverseString2('Hola').then(string => {
    expect(string).toBe('aloH')
  })
})
```
##### async/await
```js
// async/await
test('Probar async/await', async () => {
  const string = await reverseString2('Hola')
  expect(string).toBe('aloH')
})
```
#### Before
```js
// Antes de cada test
beforeEach(() => console.log('Ejecutar prueba'));
// Despues de todos los tests
beforeAll(() => console.log('Ejecutar pruebas'));
```
#### After
```js
// Despues de cada test
afterEach(() => console.log('Prueba terminada'));
// Despues de todos los tests
afterAll(() => console.log('Todas las pruebas terminadas'));
```
### Suits
```js
const randomString = require('../index')

describe("Probar funcionalidades de randomString", () => {
  test('Probar función', () => {
    expect(typeof (randomString())).toBe('string')
  })
  test('Probar que no existe Medellin', () => {
    expect(randomString()).not.toMatch(/Medellin/)
  })
})
```
### Configuración
```js
// setupTest.js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });
```
```json
// package.json
"jest": {
	"verbose": true,
    "setupFilesAfterEnv": [
      "<rootDir>/src/__test__/setupTest.js"
    ]
  }
```
### Test a renders
Los [[Jest#Mocks]] son necesarios para que estos test funcionen
#### Render de un componente Footer
~~~dependencias
npm i -D jest enzyme enzyme-adapter-react-16
~~~
```js
import React from 'react';
import { mount } from 'enzyme';

import Footer from '../../components/Footer';

describe('<Footer />', () => {
// Montar el componente
  const footer = mount(<Footer />);

  test('Render del Footer', () => {
    expect(footer.length).toEqual(1);
  });
  test('Render del titulo', () => {
    expect(footer.find('.Footer-title').text()).toEqual('Platzi Store');
  });
});
```
#### Render de un componente que usa Redux
Necesita un Mock -> [[Jest#Provider Mock]]
```js
// Header.test.js
import React from 'react';
import { mount, shallow } from 'enzyme';
import ProviderMock from '../../__mocks__/ProviderMock';
import Header from '../../components/Header';

describe('<Header />', () => {
  test('Render del componente Header', () => {
    const header = shallow(
      <ProviderMock>
        <Header />
      </ProviderMock>,
    );
    expect(header.length).toEqual(1);
  });
  test('Render del Titulo', () => {
    const header = mount(
      <ProviderMock>
        <Header />
      </ProviderMock>,
    );
    expect(header.find('.Header-title').text()).toEqual('Platzi Store');
  });
});
```
#### Render de un componente y test de un click.
Tambien necesita el Provider Mock -> [[Jest#Provider Mock]]
```js
// Product.test.js
import React from 'react';
import { mount, shallow } from 'enzyme';
import ProviderMock from '../../__mocks__/ProviderMock';
import ProductMock from '../../__mocks__/ProductMock';
import Product from '../../components/Product';

describe('<Product/>', () => {
  test('Render del componente Product', () => {
    const product = shallow(
      <ProviderMock>
        <Product />
      </ProviderMock>,
    );
    expect(product.length).toEqual(1);
  });
  test('Comprobar el boton de comprar', () => {
    const handleAddToCart = jest.fn();
    const wrapper = mount(
      <Product product={ProductMock} handleAddToCart={handleAddToCart} />,
    );
    wrapper.find('button').simulate('click');
    expect(handleAddToCart).toHaveBeenCalledTimes(1);
  });
});
```
#### Mount vs shallow
Shallow permite traer elementos y probarlos como una unidad. Es útil cuando solo necesito algo particular de ese componente y no necesito toda su estructura y elementos del DOM
**mount** -> Cuando necesitas el DOM  
**shallow** -> Solo necesitas algo particular del componente. No ocupas todo el DOM

### Test a actions
Necesitaremos el Provider Mock -> [[Jest#Provider Mock|Provider Mock]]
```js
import ProductMock from '../../__mocks__/ProductMock';
import actions from '../../actions/index';

describe('Actions', () => {
  test('addToCart', () => {
    const payload = ProductMock;
    const expected = {
      type: 'ADD_TO_CART',
      payload,
    };
    expect(actions.addToCart(payload)).toEqual(expected);
  });
  test('removeFromCart', () => {
    const payload = ProductMock;
    const expected = {
      type: 'REMOVE_FROM_CART',
      payload,
    };
    expect(actions.removeFromCart(payload)).toEqual(expected);
  });
});
```
### Test a reducers
Necesitaremos el mock -> [[Jest#Mock para la data de un producto|Product Mock]]
```js
import ProductMock from '../../__mocks__/ProductMock';
import reducer from '../../reducers';

describe('Reducers', () => {
  test('Retornar initial State', () => {
    expect(reducer({}, '')).toEqual({});
  });
  test('ADD_TO_CART', () => {
    const initialState = {
      cart: [],
    };
    const payload = ProductMock;
    const action = {
      type: 'ADD_TO_CART',
      payload,
    };
    const expected = {
      cart: [ProductMock],
    };
    expect(reducer(initialState, action)).toEqual(expected);
  });
});

```
### Test a peticiones fetch
~~~
npm i -D jest-fetch-mock
~~~
```js
// setupTest.js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });
// Configuracion para el mock de fetch
global.fetch = require('jest-fetch-mock');
```
```js
// getData.test.js
import getData from '../../utils/getData';

describe('Fetch API', () => {

  beforeEach(() => {
    fetch.resetMocks();
  });

  test('Llamar una API y retornar datos', () => {
    fetch.mockResponse(JSON.stringify({ data: 'foo' }));

    getData('https://google.com').then(res => {
      expect(res.data).toEqual('foo');
    });

    expect(fetch.mock.calls[0][0]).toEqual('https://google.com');
  });
});
```

### Mocks
Los mocks son funciones que simulan acciones que debería hacer nuestra app.
#### Mock para los estilos
```js
// styleMock.js
module.exports = {};
```
```json
// package.json
"jest": {
    "setupFilesAfterEnv": [
      "<rootDir>/src/__test__/setupTest.js"
    ],
    "moduleNameMapper": {
      "\\.(styl|css)$": "<rootDir>/src/__mocks__/styleMock.js"
    },
    "testEnvironment": "jsdom"
  }
```
Es necesario instalar la siguiente dependencia:
~~~terminal
npm i -D jest-environment-jsdom
~~~
#### Provider Mock
```js
// ProviderMock.js
import React from 'react';
import { createStore } from 'redux';
import { Router } from 'react-router-dom';
import { Provider } from 'react-redux';
import { createBrowserHistory } from 'history';
import initialState from '../initialState';
import reducer from '../reducers';

const store = createStore(reducer, initialState);
const history = createBrowserHistory();

const ProviderMock = props => (
  <Provider store={store}>
    <Router history={history}>
      {props.children}
    </Router>
  </Provider>
);

export default ProviderMock;
```
#### Mock para la data de un producto
```js
//ProductMock.js
const ProductMock = {
  id: '1',
  image: 'https://arepa.s3.amazonaws.com/camiseta.png',
  title: 'Camiseta',
  price: 25,
  description: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit',
};

export default ProductMock;
```
### Snapshots
Normalmente estos test son necesarios cuando nuestra Ul no cambia y que en dado caso por algún detalle ha cambiado, nos vamos a dar cuenta que hay ese detalle en el cambio.
~~~dependencies
npm i -D react-test-renderer
~~~
```js
// Footer.test.js
describe('Footer Snapshot', () => {
  test('Comprobar la UI del componente Footer', () => {
    const footer = create(<Footer />);
    expect(footer.toJSON()).toMatchSnapshot();
  });
});
```
```js
// Header.test.js
describe('Header SnapShot', () => {
  test('Comprobar el Snapshot de Header', () => {
    const header = create(
      <ProviderMock>
        <Header />
      </ProviderMock>,
    );
    expect(header.toJSON()).toMatchSnapshot();
  });
});
```
**Actualizar snapshot**
~~~comando
jest --updateSnapshot
~~~
