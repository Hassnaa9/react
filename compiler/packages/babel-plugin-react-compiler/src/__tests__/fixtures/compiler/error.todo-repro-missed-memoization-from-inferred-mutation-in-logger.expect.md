
## Input

```javascript
// @flow @validatePreserveExistingMemoizationGuarantees
import {useFragment} from 'react-relay';
import LogEvent from 'LogEvent';
import {useCallback, useMemo} from 'react';

component Component(id) {
  const {data} = useFragment();
  const items = data.items.edges;

  const [prevId, setPrevId] = useState(id);
  const [index, setIndex] = useState(0);

  const logData = useMemo(() => {
    const item = items[index];
    return {
      key: item.key ?? '',
    };
  }, [index, items]);

  const setCurrentIndex = useCallback(
    (index: number) => {
      const object = {
        tracking: logData.key,
      };
      // We infer that this may mutate `object`, which in turn aliases
      // data from `logData`, such that `logData` may be mutated.
      LogEvent.log(() => object);
      setIndex(index);
    },
    [index, logData, items]
  );

  if (prevId !== id) {
    setPrevId(id);
    setCurrentIndex(0);
  }

  return (
    <Foo
      index={index}
      items={items}
      current={mediaList[index]}
      setCurrentIndex={setCurrentIndex}
    />
  );
}

```


## Error

```
  19 |
  20 |   const setCurrentIndex = useCallback(
> 21 |     (index: number) => {
     |     ^^^^^^^^^^^^^^^^^^^^
> 22 |       const object = {
     | ^^^^^^^^^^^^^^^^^^^^^^
> 23 |         tracking: logData.key,
     | ^^^^^^^^^^^^^^^^^^^^^^
> 24 |       };
     | ^^^^^^^^^^^^^^^^^^^^^^
> 25 |       // We infer that this may mutate `object`, which in turn aliases
     | ^^^^^^^^^^^^^^^^^^^^^^
> 26 |       // data from `logData`, such that `logData` may be mutated.
     | ^^^^^^^^^^^^^^^^^^^^^^
> 27 |       LogEvent.log(() => object);
     | ^^^^^^^^^^^^^^^^^^^^^^
> 28 |       setIndex(index);
     | ^^^^^^^^^^^^^^^^^^^^^^
> 29 |     },
     | ^^^^^^ CannotPreserveMemoization: React Compiler has skipped optimizing this component because the existing manual memoization could not be preserved. This value was memoized in source but not in compilation output. (21:29)
  30 |     [index, logData, items]
  31 |   );
  32 |
```
          
      