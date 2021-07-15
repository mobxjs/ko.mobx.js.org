---
title: 리액트 컴포넌트 렌더링 최적화
sidebar_label: React 최적화 {🚀}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# 리액트 컴포넌트 렌더링 최적화 {🚀}

MobX는 매우 빠르며 [종종 Redux보다 빠릅니다.](https://twitter.com/mweststrate/status/718444275239882753) 하지만 React와 MobX를 최대한 활용하기 위한 몇 가지 팁이 있습니다. 대부분은 일반적으로 React에 적용되며 MobX에만 해당하지 않습니다.
이러한 패턴을 알고 있는 것이 좋지만 일반적인 애플리케이션은 전혀 걱정하지 않아도 될 만큼 빠를 것입니다.

실제로 문제가 발생한 경우에만 성능을 우선시하세요!

## 컴포넌트를 최소화하여 사용하세요.

`observer` 컴포넌트들은 모든 값을 추적하고 변경사항이 있으면 다시 렌더링합니다.
따라서 컴포넌트가 작을수록 다시 렌더링해야 하는 변경사항이 적습니다. 컴포넌트를 작게 만드는 것은 사용자 인터페이스의 더 많은 부분이 서로 독립적으로 렌더링할 가능성이 있음을 의미합니다.

## 전용 컴포넌트들을 사용하여 리스트를 렌더링하세요.

위의 내용은 큰 컬렉션을 렌더링할 때 특히 그렇습니다.
리액트는 조정자가 컬렉션이 변경될 때마다 컬렉션에 의해 생성된 컴포넌트를 평가해야 하므로 규모가 큰 컬렉션을 렌더링하는 데 좋지 않습니다.
따라서 컬렉션에 매핑하고 렌더링하고 다른 것은 렌더링하지 않는 컴포넌트를 사용하는 것이 좋습니다.

나쁜 예시

```javascript
const MyComponent = observer(({ todos, user }) => (
    <div>
        {user.name}
        <ul>
            {todos.map(todo => (
                <TodoView todo={todo} key={todo.id} />
            ))}
        </ul>
    </div>
))
```
위의 목록에서 React는 user.name이 변경될 때 불필요하게 모든 `TodoView` 구성 요소를 조정해야 합니다. 다시 렌더링 되지는 않지만, 조정 프로세스 자체가 비용이 많이 듭니다.

좋은 예시

```javascript
const MyComponent = observer(({ todos, user }) => (
    <div>
        {user.name}
        <TodosView todos={todos} />
    </div>
))

const TodosView = observer(({ todos }) => (
    <ul>
        {todos.map(todo => (
            <TodoView todo={todo} key={todo.id} />
        ))}
    </ul>
))
```

## 배열 인덱스를 키로 사용하지 마세요.

배열 인덱스 또는 미래에 변경될 수 있는 값을 키로 사용하지 마세요. 필요한 경우 객체에 대한 ID를 생성하여 사용합니다.
자세한 내용은 [여기](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)를 참고해주세요.

## 역참조는 최대한 늦게 하세요.

`mobx-react`를 사용할 때는 가능한 한 늦게 값을 역참조 하는 것이 좋습니다.
이는 MobX가 관찰 가능한 값을 자동으로 역참조하는 컴포넌트를 다시 렌더링하기 때문입니다. 
컴포넌트 트리 깊숙한 부분에서 이러한 일이 발생하면 다시 렌더링해야 하는 컴포넌트가 줄어듭니다.

Slower:

```javascript
<DisplayName name={person.name} />
```

Faster:

```javascript
<DisplayName person={person} />
```

더 빠른 예시에서 name 속성의 변경은 DisplayName만 다시 렌더링하도록 트리거 하는 반면 더 느린 예시에서는 컴포넌트의 소유자도 다시 렌더링해야 합니다. 만약 소유 컴포넌트 렌더링이 충분히 빠르다면(보통은 그렇습니다.) 느린 예시처럼 사용해도 문제가 없습니다.

### Function props {🚀}

값을 늦게 역참조하기 위해선 데이터의 다른 부분을 각각 렌더링하도록 맞춤화된 observer 컴포넌트를 최소화하여 많이 만들어야 한다는 것을 알 수 있습니다. 예를 들면 다음과 같습니다.

```javascript
const PersonNameDisplayer = observer(({ person }) => <DisplayName name={person.name} />)

const CarNameDisplayer = observer(({ car }) => <DisplayName name={car.model} />)

const ManufacturerNameDisplayer = observer(({ car }) => 
    <DisplayName name={car.manufacturer.name} />
)
```

다른 모양의 데이터가 많은 경우 이 작업은 빠르게 지루해집니다. 대안은 `*Displayer`가 렌더링할 데이터를 반환하는 함수를 사용하는 것입니다.

```javascript
const GenericNameDisplayer = observer(({ getName }) => <DisplayName name={getName()} />)
```

예를 들어 아래와 같이 컴포넌트를 사용할 수 있습니다.

```javascript
const MyComponent = ({ person, car }) => (
    <>
        <GenericNameDisplayer getName={() => person.name} />
        <GenericNameDisplayer getName={() => car.model} />
        <GenericNameDisplayer getName={() => car.manufacturer.name} />
    </>
)
```

위와 같은 접근 방식을 사용하면 응용 프로그램 전체에서 GenericNameDisplayer를 재사용하여 이름을 렌더링할 수 있으며, 여전히 컴포넌트 재 렌더링을 최소로 유지할 수 있습니다.
