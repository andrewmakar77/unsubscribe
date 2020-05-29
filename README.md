# Unsubscribe in Angular's component

### Function:

```angular2
export function takeUntilDestroy<T>(component: any): MonoTypeOperatorFunction<T> {
  const proto = Object.getPrototypeOf(component);
  const onDestroy = proto.ngOnDestroy;
  if (!(onDestroy && typeof onDestroy === 'function')) {
    throw new Error(`
      ${(proto.constructor.name || 'Component')} 
        should have the "ngOnDestroy" method for takeUntilDestroy operator to work.
    `);
  }
  if (!component.__destroy$__) {
    component.__destroy$__ = new Subject<void>();
  }
  component.ngOnDestroy = function() {
    component.__destroy$__.next();
    component.__destroy$__.complete();
    onDestroy.apply(this, arguments);
  };
  return takeUntil<T>(component.__destroy$__);
}
``` 

### Usage into component:

```angular2
@Component({
  selector: 'my-example',
  template: `
      <h1>Example Component</h1>
    `,
})
export class ExapmleComponent  implements OnDestroy {
  // ... //
  constructor() {// ... //}
  // ... //
  public ngOnDestroy(): void {// ... //}
  // ... //

  someObservable$
    .pipe(
      takeUntilDestroy(this),
    )
    .subscribe(// ... //)
}
```
