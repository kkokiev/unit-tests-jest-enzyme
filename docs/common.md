# Общие сведения

- Jest создан Facebook и представляет собой фреймворк для тестирования кода javaScript и react приложений.
- Enzyme — утилита, которая облегчает тестирование приложения на React.
- Самая большая сложность тестирования заключается в том, что необходимо писать легко тестируемый код.
- Для создания тетста добавить it() или test() блок с именем теста и кодом.
- Возможно опциональное обёртывание в describe() для логической группировки.


## Javascript

Основная идея заключается в том, что тест описывает `что` система делает, а не `как`. Так что рефакторинг не поломает тесты.

`sum.js`
```js
export default function sum(a, b) {
  return a + b;
}
```

`sum.test.js`
```js
import sum from './sum';

it('sums numbers', () => {
  expect(sum(1, 2)).toEqual(3);
  expect(sum(2, 2)).toEqual(4);
});
```
Все expect() поддерживаемые Jest описаны [здесь](https://facebook.github.io/jest/docs/en/expect.html#content)

## React

### Если стратегия тестирования еще не определена, `create-react-app` рекомендует начать с простых тестов для компонентов:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
});
```

Такой тест mounts a component и проверяет что он не выдал ошибку во время рендера.
Когда встретятся баги, вызванные изменением компонентов,
появится понимание какие части компонента нуждаются в тестировании.

### Для тестирования компонетов ИЗОЛИРОВАННО от child которые они ренедерят, рекомендуют использовать shallow() rendering API из Enzyme

```js
import React from 'react';
import { shallow } from 'enzyme';
import App from './App';

it('renders without crashing', () => {
  shallow(<App />);
});
```

В отличии от предыдущего теста, такой тест рендерит только <App> и будет passed даже если внутри 
<App> есть например компонент <Button> который выдает ошибку.

Если нужны рендер тесты которые проверяют интеграцию компонетов, Enzyme поддерживает
полный рендер с [mount()](http://airbnb.io/enzyme/docs/api/mount.html)

### Снимки ака snapshots

В первый раз, когда запускается тест, создаётся файл снимка. После этого можно посмотреть созданный файл, чтобы проверить, соответствует ли отрендеренный компонент ожидаемому результату. Snapshot, при необходимости можно перезаписать.

`link.js`

```jsx
import React from 'react';
import { string } from 'prop-types';
const Link = ({ title, url }) => <a href={url}>{title}</a>;
Link.propTypes = {
  title: string.isRequired,
  url: string.isRequired
};
export default Link;
```

`link.test.js`
```js
import React from 'react';
import { shallow } from 'enzyme';
import { shallowToJson } from 'enzyme-to-json';

import Link from './link';

describe('Link', () => {
  it('should render correctly', () => {
    const output = shallow(
      <Link title="mockTitle" url="mockUrl" />
    );
    expect(shallowToJson(output)).toMatchSnapshot();
  });
});
```

### Что еще тестировать?

Тесты - это то что нужно писать и поддерживать, написание хороших тестов - такое же ремесло как и написание самого кода.
Чем больше проект и команда, тем более детальные тесты необходимы.
Представь что ты возвращаешься в будущем к компоненту или рефакторишь его, что ты ожидаешь от тестов, какими им быть?

- Изолированными - все взаимодействия с наружными сервисами are mocked.
- Специфичными - если поменяется небольшая часть функционала, хорошо бы получить fail по отдельному тесту.