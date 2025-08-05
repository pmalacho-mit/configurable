# configurable

```ts
type ConfigurableInstance<T extends object> = {
  config: T;
} & {
  [K in keyof T]: (value: T[K]) => ConfigurableInstance<T>;
};

type ConfigurableConstructor<T extends object> = {
  new (DoNotDefineYourOwnConstructor: never): ConfigurableInstance<T>;
  readonly Configure: ConfigurableInstance<T>;
};

export function configurable<T extends object>(
  defaults: T
): ConfigurableConstructor<T> {
  class C {
    config: T;

    constructor() {
      this.config = { ...defaults };
    }

    static get Configure(): ConfigurableInstance<T> {
      return new (this as any)() as ConfigurableInstance<T>;
    }
  }

  const keys = Object.keys(defaults) as (keyof T)[];
  const prototype = C.prototype as any;

  for (const key of keys)
    prototype[key] = function (value: any) {
      this.config[key] = value;
      return this;
    };

  return C as ConfigurableConstructor<T>;
}
```
