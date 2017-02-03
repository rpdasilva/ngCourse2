# Reading your Application State using Selectors

To read your application state in Redux, we need to use the `select()` method on
the `NgRedux` class. This method creates and
returns an `Observable` that is bound to a specific property in your application
state.

For example, here's how you would select the `counter` object:

```typescript
ngRedux.select('counter'); // Returns Observable<Counter>
```

To fetch the counter's `currentValue`, we can pass in a `string` array,
where each string plucks a single property from the application state one at a
time in the order specified:

```typescript
ngRedux.select(['counter', 'currentValue']); // Returns Observable<number>
```

While `select()` allows for several variations of strings to be passed in, it
has it's shortcomings - namely you won't actually know if the plucking is
working properly until you execute your code.

Because of that, `select()` allows you to select values using functions too,
which makes things more type-safe and your selectors will be more refactorable
by your IDE.

```typescript
ngRedux.select(appState => appState.counter.currentValue);
```

## Creating a Counter Service

While you could inject `NgRedux` and select values directly in your Angular
components, it's considered to be a best practice to wrap this functionality
into separate services. This approach encapsulates all of the selection logic
and eliminates any duplication where the selection path is repeated
throughout your application.

Let's tie everything together by building out a `CounterService` example:

_app/services/counter.service.ts_
```typescript
import {Injectable} from '@angular/core';
import {NgRedux} from 'ng2-redux';
import {Observable} from 'rxjs/Observable';

import {AppState} from '../models';

@Injectable()
export class CounterService {

  constructor(private ngRedux: NgRedux<AppState>) {}

  getCurrentValue(): Observable<number> {
    return this.ngRedux.select(appState => appState.counter.currentValue)
      .filter(Boolean);
  }

}
```

Because `select()` returns an `Observable`, the `getCurrentValue()` method also
applies a `filter()` to ensure that subscribers do not receive any *falsy*
values. This greatly simplifies the code and templates in your components, since
they don't have to repeatedly consider the falsy case everywhere the value is
used.

## @select

[ng2-Redux](https://github.com/angular-redux/ng2-redux) provides us another
way to read application state, and that is through the `@select` property
decorator.

`@select` is simply an abstraction of the `select()` method that
we've just learned about. It enables us to automatically bind state selections
to class properties.

The decorator shares the same API as the `select()` method and so the syntax
should look quite familiar. Below are examples of the various ways you can use
the `@select` decorator.

```typescript
import {Injectable} from '@angular/core';
import {NgRedux, select} from 'ng2-redux';
import {AppState} from '../models';

@Injectable()
export class CounterService {
  // This will select 'counter' from the store and attach it to
  // a property of the same name `counter`
  @select() counter;

  // Same as above. This will select 'counter' from the application state
  // (ignoring the $) and bind it to a property of the same name `counter$`
  // It is common convention to suffix observable properties with a `$` to
  // signify in a glanceable way, that this property is an observable stream.
  @select() counter$;

  // This will select 'counter' from the application state and attach it to
  // a property `myCounter$`
  @select('counter') myCounter$;

  // This will pluck 'currentValue' from the application state and attach it to
  // a property `currentValue$`
  @select(['counter', 'currentValue']) currentValue$;

  // Uses a function to return 'currentValue' from application state and
  // attaches it to a property `myCurrentValue$`
  @select(appState => appState.counter.currentValue) myCurrentValue$;
}
```
