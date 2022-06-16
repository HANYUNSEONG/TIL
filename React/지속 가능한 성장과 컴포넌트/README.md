# 지속 가능한 성장과 컴포넌트

> 이 글은 토스 개발자 컨퍼런스 SLASH 22 - [지속 가능한 성장과 컴포넌트](https://youtu.be/fR8tsJ2r7Eg) 영상을 보며 작성했습니다.

## 들어가기 전

**변경**은 제품이 성장하기 위해선 필수적인 요소다.  
제품이 변경되지 않으려면 **모든** 사용자가 **어려움**없이 **잘** 사용해야 하는 데 실제로는 그러지 않기 때문이다.

사실 변화라는 건 제품의 성장이라고 봐도 될 것 같다.

변경은 정확히 정의되어 있는 것이 아니기 때문에 모든 변경에 대해서 예측할 순 없다. 그헣기 때문에 우리는 변경을 예측하지 말고 잘 대응해야 한다. 이 내용이 영상의 주제다.

우리는 제품을 만들 때 작은 컴포넌트를 합쳐 기능을 만들고 이 기능이 너무 커지면 **적당히** 분리한다.  
여기서 이 **적당히**라는 게 참 어려운 것 같다. 어떤 기준으로 분리하는지 이 기준이 개발자마다 다르다.

발표자분께서는 되돌아보면 본인은 기준 없이 분리했던 것 같다고 하셨다. 그냥 중복이라서 분리하거나 단순히 크다는 이유로 분리를 했다고 한다. 사실 돌아보면 나도 그런 것 같다.

```tsx
const { filtered, value, index, fetchResponseType } = repos
const Results = (
  <ItemLayout id="fix_scroll">
    {filtered.map((repo: ItemType, i: number) => (
      <Item
        key={’item_${i}’}
        index={i}
        curIndex={index}
        item={repo}
        onClickItem={onClickItem}
      />
    ))}
    {currentTargetValue && (
      <Item
        key={’item_${filtered.length}’}
        index={filtered.length}
        curIndex={index}
        item={{
        id: ‘search_in_google’,
        name: ‘github: ${currentTargetValue}’,
        htmlUrl: currentTargetValue,
        }}
        onClickItem={onClickItem}
      />
    )}
  </ItemLayout>
)
```

위에 코드는 발표자분이 3년전에 작성하신 코드라고 한다.

코드에서 Item이라는 컴포넌트는 어떤 컴포넌트일까? 어떤 데이터를 다루는지 데이터를 어떻게 보여주는지 전혀 예상할 수 없다. 자세히 보면 props 중 item이라는 prop이 있는데 Item 컴포넌트에 item prop라니 무슨 말인지 알 수 없다.

본인이 개발한 컴포넌트지만 무슨 역할을 하는지 파악하기 어렵다.  
이 코드를 변경한다고 하면 어떨까?

우선 어딜 수정해야하는지 모르는 것이 문제다. 프로젝트 전체 코드를 살펴봐야 파악할 수 있을 것 같은데 생각만해도 힘들다.

추측으로는 만들다보니 컴포넌트가 커졌고 **적당히** 덜어내며 만들어졌을거라고 한다.  
그 **적당히**라는 포인트가 문제가 된 것이다.

우리는 변경에 잘 대응하기 위해서 우리가 제어할 수 있는 것 중 **컴포넌트**에 집중해서 이야기해보려고 한다.  
이제 변경에 유연한 컴포넌트는 어떤 특징을 가지고 있고 어떻게 만들 수 있을 지 알아보자

변경에 유연한 컴포넌트의 특징 3가지는

1. Headless 기반의 추상화하기  
   변하는 것 vs 상대적으로 변하지 않는 것
2. 한 가지 역할만 하기  
   또는 한 가지 역할만 하는 컴포넌트의 조합으로 구성하기
3. 도메인 분리하기
   도메인을 포함하는 컴포넌트와 그렇지 않은 컴포넌트를 분리하기

위에 3가지 특징을 이제 하나씩 자세히 알아보자

---

## 1. Headless UI 기반의 추상화하기

Headless라는 개념을 알아보기 전에 먼저 컴포넌트가 하는 3가지 역할에 대해서 살펴보자

1. 데이터를 관리하고
2. UI를 구성하며
3. 어떻게 사용자와 상호작용할 지 정의한다

```tsx
export function Button() {
  const [state, setState] = useState(false); // (1)

  return (
    // (2)
    <button
      // (3)
      onClick={() => {
        setState((x) => !x);
      }}
    >
      버튼 {state ? "on" : "off"}
    </button>
  );
}
```

1. state라는 상태를 관리하고 있고 (데이터)
2. 이 데이터를 'on', 'off'라는 텍스트로 노출하고 있다.
3. onClick 핸들러를 통해 사용자와 상호작용도 정의해줬다.

"어떻게" 보여질지 정의하는 부분은 **디자인**에 의존한다.  
**디자인**에 의존하는 UI를 컴포넌트가 관리하는 "데이터"와 분리해보면 어떨까?

### 데이터 추상화

예로 달력 컴포넌트를 만든다고 가정하면  
달력을 구성하는 데이터는 변하지 않지만 UI는 언제든 변경될 수 있다. 여기서 데이터와 UI를 분리해보자.

달력에 필요한 데이터를 추상화하여 hooks를 만들어주고  
hooks에서 제공하는 데이터를 가지고 UI를 구성하기만 하면 된다.

```tsx
export default function Calendar() {
  const { headers, body, view } = useCalendar();

  return (
    <Table>
      <Thead>
        <Tr>
          {headers.weekDays.map(({ key, value }) => {
            return <Th key={key}>{format(value, ‘E’, { locale })}</Th>
          })}
        </Tr>
      </Thead>
      <Tbody>
        {body.value.map(({ key, value: days }) => (
          <Tr key={key}>
            {days.map(({ key, value }) => (
              <Td key={key}>{getDate(value)}</Td>
            ))}
          </Tr>
        ))}
      </Tbody>
    </Table>
  )
}
```

달력을 구성할 때 필요한 데이터를 계산해야 하는데 이 역할을 useCalendar hooks에 위임한 것이다.

UI를 관심사에서 제외하고 데이터만 모듈화 할 수 있는데 이런 패턴을 **Headless**라고 한다.  
한 가지 문제에만 집중하기 때문에 더 많은 곳에서 사용할 수 있고 다른 변경으로부터 격리시킬 수 있다.

이제 사용자와 상호작용하는 부분을 확인해보자 이번에도 UI와 분리하면 좋을 것 같다.

### 동작 추상화

예를 들어 버튼에 Long Press라는 동작을 정의하려고 한다.  
이떄 버튼 컴포넌트 내부에 동작을 정의하는 로직이 있으면 컴포넌트가 복잡해진다.

그래서 동작을 hooks로 모듈화해서 추상화하면 이제 보여지는 것에 집중할 수 있다.

아래는 Long Press 동작을 hooks로 모듈화한 예제 코드다.

```ts
function useLongPress() {
  return {
    onKeyDown={(e) => {}},
    onKeyUp={(e) => {}},
    onMouseDown={(e) => {}},
    onMouseUp={(e) => {}},
  }
}
```

이렇게 모듈화를 해놓으면 버튼 컴포넌트가 아닌 다른 컴포넌트에서 Long Press라는 동작을 쉽게 정의할 수 있다.

위에 데이터 추상화, 동작 추상화에서 hooks로 모듈화하는 이야기만 했는데  
**그 이유는 변경에 유연해지려면 `각 모듈`이 `한 가지 일`만 하는 게 중요하기 때문이다.**

---

## 2. 한 가지 역할만 하기

이번엔 한 가지 역할만 하는 컴포넌트의 **조합**으로 구성되어야 한다는 점에 대해서 알아볼거다

버튼처럼 간단한 컴포넌트는 한 가지 역할을 잘 수행할 수 있지만 복잡한 컴포넌트의 경우는 어떨까?

예를 들어 흔히 볼 수 있는 Select UI를 생각해보자  
Select UI를 만들다보면 자연스럽게 작은 컴포넌트들을 사용하여 구성하게 된다.

```tsx
function ReactFrameworkSelect({ defaultValue }) {
  const [isOpen, open, close] = useBoolean();
  const [selected, change] = useState(defaultValue);

  return (
    <>
      <InputButton label="React Framework”" value={selected} onClick={open} />
      {isOpen ? (
        <Options onClose={close}>
          {options.map((value) => {
            return (
              <Button
                selected={selected === value}
                onClick={() => change(value)}
              >
                {value}
              </Button>
            );
          })}
        </Options>
      ) : null}
    </>
  );
}
```

위 예제 코드는 **변경**이 발생했을 때 제대로 대응하지 못한다.  
그렇기 때문에 재사용하기도 어려워진다.

예를 들어서 InputButton에 label만 달라져도 재사용이 어렵게 된다.
만약 InputButton이 아닌 다른 컴포넌트를 사용해야 한다면 어떨까?  
**저 구조라면 답이 없다.**

저 답 없는 컴포넌트를 4개의 컴포넌트로 분리해서 살려보자  
분리하는 기준은 담당하는 역할, 데이터를 기준으로 분리한다.

```tsx
function Select({ label, trigger, value, onChange, options }: Props) {
  return (
    <Dropdown label={label} value={value} onChange={onChange}>
      <Dropdown.Trigger as={trigger} />
      <Dropdown.Menu>
        {options.map((option) => (
          <Dropdown.Item>{option}</Dropdown.Item>
        ))}
      </Dropdown.Menu>
    </Dropdown>
  );
}
```

위 예제 코드에서 `Dropdown`은 내부적으로 `Menu`가 보일지 말지 결정한다. `Trigger`와 연결된다.  
`Item`에서 발생한 _onClick_ 이벤트는 `Dropdown`의 _onChange_ 이벤트로 이어진다.

Select UI를 구성한건데 이걸 가지고 저기 위에 있는 답 없는 컴포넌트를 살릴 수 있다.

```tsx
function FrameworkSelect() {
  const {
    data: { frameworks },
  } = useFrameworks();
  const [selected, change] = useState();

  return (
    <Select
      trigger={<InputButton value={selected} />}
      value={selected}
      onChange={change}
      options={frameworks}
    />
  );
}
```

재구성한 코드다.  
`Select` 컴포넌트와 **trigger**로 전달된 `InputButton` 컴포넌트는 서로의 존재에 대해 알지 못한다.  
즉 서로의 변경이 서로에게 영향을 끼치지 않게 된 것이다.  
그럼 각각의 컴포넌트가 변경에 유연해진다. 저기서 **trigger** 부분이 변경되어도 `Select` 컴포넌트 자체는 변경이 없기 때문이다.

이렇게 합성 가능하도록 컴포넌트를 구성하면 **재사용**하기 좋고 **확장**하는 데에도 좋다.

이제 조금 더 복잡한 컴포넌트로 넘어가보자

버튼을 누르면 옵션을 선택할 수 있는 모달이 나오고 그 옵션에는 체크박스로 여러가지를 선택할 수 있다.  
선택을 하고 `적용하기` 버튼을 누르면 선택한 값들이 버튼에 노출되는 기능을 가지고 있다.

UI만 생각해보면 조금 복잡할 수 있지만 데이터를 중심으로 보면 그렇게 복잡하지 않다.  
위에서 했던 Select UI와 비슷하다 선택하고 선택한 데이터를 보여준다는 점에서 비슷하다고 볼 수 있다.  
단지 여러개를 선택해야 한다는 점이 추가된 것이다.

```tsx
function FrameworkSelect({
  selectedFrameworks,
  onFrameworkChange,
  frameworks,
}: Props) {
return (
  <Dropdown value={selectedFrameworks} onChange={onFrameworkChange}>
    <Dropdown.Trigger
      as={<Button>{String(selectedFrameworks ?? ‘선택하기’)}</Button>}
    />
    <Dropdown.Modal
      controls={
        <Flex>
          <Button type="reset">초기화</Button>
          <Button type="submit">적용하기</Button>
        </Flex>
      }
    >
      {frameworks.map(framework => {
        return <Dropdown.Item>{framework}</Dropdown.Item>;
      })}
    </Dropdown.Modal>
  </Dropdown>
  );
}
```

예제는 위에서 만들었던 Dropdown 컴포넌트를 사용하여 만들었다.

`<Dropdown.Trigger>` props 중 `as`에 `Button` 컴포넌트를 넣어서 메뉴가 보알지 말지 상호작용을 할 수 있다. 그리고 `<Dropdown.Modal>`로 노출되는 메뉴를 분리했다. `<Dropdown.Modal>` props에 controls를 보면 적용하기 버튼이 **submit**이다. 하나의 form으로 바라보고 적용하기 버튼을 클릭하면 **onSubmit** 이벤트가 발생하고 그럼 어떤 옵션을 선택했는지 알 수 있다.

이렇게 한 가지 역할만 하는 컴포넌트들의 조합으로 구성해서 빠르게 변경에 대응할 수 있다.  
지금까지 Handless 기반으로 추상화를 하고 조합으로 컴포넌트를 표현하는 걸 살펴보았다.  
이제 레이어링를 알아보자

## 3. 도메인 분리하기

컴포넌트의 인터페이스는 **일반적** 일수록 이해하기 쉽다.  
컴포넌트 이름과 Props 네이밍을 일반적이게 구성하면 조금 더 이해하기 쉬울 것이다.

```ts
interface Props {
  options: Array<{ label: string }>;
  value?: string[];
  onChange?: (selecteds: string[]) => void;
  valueAs: (value?: string[]) => string;
}
```

웹 프론트엔드 개발자라면 Select UI에 기본적인 지식이 있을거라고 생각한다.  
그 기본적인 지식을 바탕으로 컴포넌트의 동작을 예측할 수 있도록 Props를 위에 예제처럼 구성하면  
"select attribute"와 비슷하게 구성하면 예측하기 쉽다.  
이로 알 수 있는건 컴포넌트 인터페이스가 **표준**에 가까울수록 많은 사람들이 **쉽게** 이해할 수 있다는 것이다.

## 정리

컴포넌트가 유연하기 위해서 가져야하는 특징 3가지에 대해서 알아보았다.

1. Handless 기반의 추상화하기
2. 한 가지 역할만 하기
3. 도메인 분리하기

### 첫번째 팁

컴포넌트를 구현할 때 인터페이스를 먼저 고민해보는 것이 좋을 것 같다.  
개발하려는 컴포넌트를 이미 있다고 생각하고 사용하듯 작성해보는 것이다.

변경해야할 때 파악해야 하는 것은

1. 의도가 무엇이고
2. 컴포넌트의 기능은 무엇이고
3. 어떻게 표현이 되어야 하는가

이렇게 3가지인데 그렇기 때문에 인터페이스를 먼저 정의하는 게 도움이 되었다.

### 두번째 팁

그리고 컴포넌트를 나누는 이유에 대해서도 다시 생각해보는 습관을 갖으면 좋을 것 같다.

나누려는 컴포넌트의 이유가 복잡도를 낮추기 위함인지 아니면 재사용을 하기 위해서인지 꼭 분리해야 하는 컴포넌트인지 고민해 볼 필요가 있다.

우리는 제품을 만들 때 많은 컴포넌트를 개발하게 된다. 잘 만들어 두면 미래의 나에게도 큰 도움이 될 것이고 함께 일하는 동료한테도 도움이 된다.

변경에 유연한 코드는 안정적으로 비즈니스를 운영하면서 빠른 속도를 유지하는데 필수적인 요소다.

---

발표자: 한재엽님([JBEE](https://jbee.io/))
