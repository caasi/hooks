[![NPM Status](https://img.shields.io/npm/v/@hereticaljs/hooks.svg?style=flat)](https://www.npmjs.com/package/@hereticaljs/hooks)
[![Build Status](https://travis-ci.org/HereticalJS/hooks.svg?branch=master)](https://travis-ci.org/HereticalJS/hooks)

# @hereticaljs/hooks

Some heretical React Hooks.

## Install

```
npm install @hereticaljs/hooks
```

## Core hooks

### `useConcat`

Yields one value or another.

```javascript
const x = useConcat(0, 1); // yields 0 and 1
```

### `useArray`

Yields every values from a given array.

```javascript
const xs = useMemo(() => [0, 1, 2], []);
const x = useArray(xs); // yields 0, 1 and 2
```

### `useGenerator`

Treats a generator as a stream and collects values from it.

```javascript
function* gen() {
  yield 0;
  return 1;
}

const iter = useMemo(() => gen(), []);
const [value, done] = useGenerator(iter);
```

### `useDefined`

Filters out undefined values.

```javascript
function* gen() {
  yield 0;
  yield undefined;
  return 1;
}

const iter = useMemo(() => gen(), []);
const [value, done] = useGenerator(iter);
const x = useDefined(value); // yields 0 and 1
```

### `useFold`

Folds over a hook value.

```javascript
function* gen() {
  yield 1;
  yield 2;
  return 3;
}

const iter = useMemo(() => gen(), []);
const [value, done] = useGenerator(iter);
const x = useFold(value, (a, b) => a + b, 0); // yields 0, 1, 3 and 6
```

### `useFilter`

Filters out unwanted values.

```javascript
const mod2 = useCallback(x => x % 2 === 0);
const as = useMemo(() => [0, 1, 2], []);
const x = useArray(as);
const y = useFilter(x, mod2); // yields 0 and 2
```

### `usePromise`

Resolves a `Promise` and returns its status.

```javascript
const p = useMemo(() => api.get('https://example.com'), []);
const [value, error, isPending] = usePromise(p);
```

### `useMaybe`/`useOptional`

Chains optional values into another optional value.

```javascript
const ab = useMaybe(
  [a, b],
  (a, b) => a * a + b * b,
);
```

### `useObservable`

Yields values from a given [RxJS][RxJS] `Observable`.

```javascript
const xs = useMemo(() => of(0, 1, 2), []);
const x = useObservable(xs, 0); // yields 0, 1 and 2
```

[RxJS]: https://github.com/ReactiveX/rxjs

## IO hooks

### `useTime`

Gives a `DOMHighResTimeStamp` diff from the time it was called first time.

```javascript
const t = useTime();
```

### `useRange`

Gives a number between `start` and `end`. It's an application of `useTime` and it's useful in simple animations.

```javascript
const theta = useRange(0, 2 * Math.PI);
const x = Math.cos(theta);
const y = Math.sin(theta);
const pt = { x, y };
```

### `useImageData`

Reads an image from a URL and gives you an `ImageData`.

```javascript
const imageData = useImageData('https://example.com/lena.png');
```

### `useBlob`/`useFile`

Reads a `Blob` as an `ArrayBuffer` or a string. Defaults to an `ArrayBuffer`.

```typescript
enum ResultType {
  ARRAY_BUFFER = 'arraybuffer',
  BINARY_STRING = 'binarystring',
  DATA_URL = 'dataurl',
  TEXT = 'text',
}

const dataurl = useBlob(file, useBlob.ResultType.DATA_URL);
```

### `useObjectURL`

Creates an object URL from anything. It's useful with an image blob.

```javascript
const imageData = useImageData(useObjectURL(file));
```

### `useImageFile`

Gives a `ImageData` from an image blob. It's an alias of `file => useImageData(useObjectURL(file))`.

### `useWebSocket`

Opens a web socket and streams messages.

```javascript
const [socket, messages = []] = useWebSocket('wss://echo.websocket.org');
const msgs = messages.filter(x => x).reverse();
```

## UI component related hooks

### `useProp`

Binds a state and a state handler to a React element.

```javascript
const [val, elem] = useProp(element, value, 'value', 'onChange', e => e.target.value);
```

### `useInput`

A shortcut to bind a value to an input element.

```javascript
const [r, rRange] = useInput(
  <input type="range" min="0.0" max="1.0" step="0.01" />,
  '1.0',
)
```

## Other hooks

### `useSpace`

Stores state histories.

It's named as `useSpace` instead of `useHistory` because it flattens a value in time to space(a list).

```javascript
const [state, setState] = useState(0);
const history = useSpace(state);

useEffect(() => {
  setState(s => s + 1);
  console.log(history);
}, [history]);
```

## ToDo

* [ ] test more hooks
  * [ ] `useConcat`
  * [ ] `useImageData`
  * [ ] `useImageFile`
  * [ ] `useProp`
  * [ ] `useInput`
  * [ ] `useObjectURL`
  * [ ] `useTime`
  * [ ] `useRange`
* [ ] rewrite everything in TypeScript
