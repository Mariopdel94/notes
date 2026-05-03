#angular #clean-code  #good-practice #styleguide #example 

If you’ve spent any significant time working with Angular Material, you are intimately familiar with `MatDialog`. It’s a fantastic, robust service for rendering overlays. But historically, the way we interact with it has been entirely **imperative**.

You inject the service, you call `.open()`, you pass in data via a clunky `config.data` object, and you **manually subscribe** to `afterClosed()` to handle the result.

In the era of modern Angular—with Signals, standalone components, and a heavy push toward reactive, state-driven UI—this imperative pattern feels out of place. We strive to make our templates the source of truth for our UI state. So why are we still burying our dialog logic deep inside component classes?

It’s time to rethink the modal. By wrapping `MatDialog` in a declarative, signal-driven component, we can drastically clean up our codebases, eliminate boilerplate, and flawlessly preserve component context.

## The Problem with the Imperative Approach

Let’s quickly look at the standard way of opening a dialog:

1. **The Boilerplate:** You have to inject `MatDialog` into your parent component.
2. **The Context Chasm:** If the dialog needs data from the parent, you must pass it through `MAT_DIALOG_DATA`. The child component then has to inject that token to read the data.
3. **The Subscription Management:** You have to subscribe to the dialog's closure and remember to clean up that subscription (though `takeUntilDestroyed` helps now).
4. **State Disconnect:** The open/closed state of the dialog exists outside of your component's standard reactive flow.
    
When you scale this across an enterprise application with dozens of context-heavy dialogs (forms, confirmations, detail views), the friction adds up.

## The Solution: A Declarative Wrapper

Instead of treating a dialog as a service call, we should treat it as what it fundamentally is: **a piece of the user interface that toggles based on state.**

By leveraging Angular's `contentChild`, `<ng-template>`, and Signals, we can build a lightweight wrapper around `MatDialog` that allows us to define dialogs directly in our HTML.

```typescript
/**
 * This component acts as a declarative wrapper around the Angular Material MatDialog service.
 * It allows developers to define and control the lifecycle (open/close) of a dialog directly
 * within an Angular template, eliminating the need to manually open dialogs through component classes
 * and manually manage subscriptions to dialog observables.
 * The dialog content must be provided as an `<ng-template>` which is projected into this component.
 *
 * @example
 * // Basic usage triggered by a button click
 * ```html
 * <app-dialog [isOpen]="showDialog" (dialogClosed)="handleClose()">
 * <ng-template>
 * <h2 mat-dialog-title>My Dialog Title</h2>
 * <mat-dialog-content>
 * <p>Dialog content goes here.</p>
 * </mat-dialog-content>
 * <mat-dialog-actions>
 * <button mat-button (click)="showDialog = false">Close</button>
 * </mat-dialog-actions>
 * </ng-template>
 * </app-dialog>
 * ```
 */
@Component({
  selector: 'app-dialog',
  imports: [],
  template: ``,
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class DialogComponent {
  private readonly vcr = inject(ViewContainerRef);
  private readonly destroyRef = inject(DestroyRef);
  /**
   * Angular Material Dialog service injected to manage the dialog instance.
   */
  private readonly dialog = inject(MatDialog);
  /**
   * Stores the reference to the currently open dialog instance, allowing independent control.
   */
  private readonly dialogRef = signal<MatDialogRef<unknown> | null>(null);
  /**
   * A reference to the `<ng-template>` that contains the dialog's content.
   * This is a **required content projection** and must be provided inside the component's tags.
   */
  readonly template = contentChild.required<TemplateRef<unknown>>(TemplateRef);
  /**
   * Optional configuration options for the Angular Material dialog.
   * This maps directly to `MatDialogConfig`.
   */
  readonly config = input<MatDialogConfig>({});
  /**
   * Controls the open/closed state of the dialog.
   * Setting to `true` opens the dialog; setting to `false` closes the currently managed dialog.
   */
  readonly isOpen = input<boolean>(false);
  /**
   * Emits an event when the dialog instance managed by this component is closed.
   * The emitted value is the result passed to `dialogRef.close(result)`.
   */
  readonly dialogClosed = output<void>();

  /**
   * An Angular `effect` that reacts to changes in the `isOpen` signal.
   * It handles the automatic opening and closing of the dialog.
   */
  protected readonly manageDialogStatus = effect(() => {
    const shouldBeOpen = this.isOpen();
    const currentDialogRef = this.dialogRef();

    if (shouldBeOpen && !currentDialogRef) {
      const ref = this.dialog.open(this.template(), {
        viewContainerRef: this.vcr,
        ...this.config(),
      });
      this.dialogRef.set(ref);
      ref
        .afterClosed()
        .pipe(takeUntilDestroyed(this.destroyRef))
        .subscribe((result) => {
          this.dialogClosed.emit(result);
          this.dialogRef.set(null);
        });
    } else if (!shouldBeOpen && currentDialogRef) {
      currentDialogRef.close();
    }
  });
}
```

## Why This Pattern Shines

This seemingly simple wrapper unlocks several massive architectural benefits for your codebase.
### 1. The Magic of Lexical Scope (No More `MAT_DIALOG_DATA`)

Because the dialog content is wrapped in an `<ng-template>` inside the parent component, **it inherits the parent's context.** You no longer need to pass data payloads. The dialog template has direct access to the parent component's properties, signals, and methods.
### 2. State-Driven UI

Modern Angular development (especially with tools like NgRx SignalStore) favors state machines. The UI is simply a reflection of state.

With this wrapper, opening a dialog is as simple as `this.showDialog.set(true)`. Your state dictates the UI, rather than a side-effect method call dictating the UI.

## When to Stick with the Old Way

Is this a silver bullet? Almost, but not entirely.

This declarative pattern is perfect for **UI-bound dialogs** modals that belong conceptually to a specific view or component (like forms, specialized confirmations, or detail views).

However, for **global dialogs** such as generic session timeout warnings triggered by an HTTP interceptor, or global error handlers—the imperative `this.dialog.open()` triggered from a global service remains the correct architectural choice.