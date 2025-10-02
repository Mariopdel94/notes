# Server rendering & reliability

**Will components render correctly on our server-rendered pages (ex: Next.js) with no errors or mismatch warnings?**
According to the [documentation](https://stenciljs.com/docs/react) on StencilJS and the integration with React: Yes.
>🚀 Support for Server Side Rendering (SSR) when used with frameworks like [Next.js](https://nextjs.org/)

**If JavaScript is slow/off, do we still get meaningful HTML/text?**
Yes, the components will work as any other normal HTML/JS element and these include meaningful `aria` attributes for accesibility.

**Do any components need special server setup?***
No, the components are created with reusability and customization on mind. These components are not only for 7NOW to use, but across all teams through a simple framework. We will work on creating meaningful documentation including the API for each component.

# Custom tag → browser behavior
**Do we need transformers/polyfills for the custom tags?**
Short answer: No.
Long answer: StencilJS builds the components for the different (specified) frameworks (Angular, React, Web Elements). The build output is a normal component for the respective framework. Example: A button component named `SlpButton` in Angular will default to html element tag as `<slp-button>` but for React it will default to `<SlpButton>`. 

**Are there plain-HTML fallbacks if features aren’t supported?**
More context is needed to provide an answer.

**Any backward-compatibility limits (older Chrome/Safari/Firefox/Edge, mobile)?**
React - Supports: React v17+ • TypeScript v5+ • Stencil v4.2.0+
Angular - v19.x and above
Web
![[Pasted image 20251002164735.png]]

Note: AFAIK This table doesn't mean all components will be available as said in the table. In order to be available the version of the Stencil components must be supported on the React project, and this React project must support the different browsers in the different versions.

# Accessibility
**Are all components WCAG AA by default?**
The components are built based on the specifications of the Slurpee Design System team. [You can see them here](https://zeroheight.com/19087d1a1/p/8538c9-components-list)

**How is this tested (automated + manual screen-reader/keyboard)?**
@Chris might answer this.

**For forms/dialogs: are focus, errors, and announcements handled out of the box?**
**Forms**: The UI part is handled automatically. For example: The border of a field is `gray` by default, once the user focuses on the field the border will automatically turn `black` and when there's an error it will turn `red`. The field/form component does **not** know when there's an error. This is part of the responsibility of the developer to let the component know when there's an error through the `errorText` input. 
**Note on forms**: So far we **haven't** created a `form` component, only a `field` component. While a form might be put simply as a collection of fields, it might be more difficult since deeper logic might need to be handled. This is why it is left for the developer and the framework they decide to use. This means that an Angular developer will be able to take full advantage of the capabilities of `template-driven-forms` and `reactive-forms`.
**Dialogs**: These components don't have error handling. For this component to work a required input called `isOpen` is needed. This expects a reactive state variable that changes, when this value changes to true the dialog component will open. Example utilizing Angular signals:
```html
<slp-button class="margin-top" (click)="isOpen.set(true)" size="large">
  Open dialog
</slp-button>

<slp-dialog
  [isOpen]="isOpen()"
  id="mySampleDialog"
  (didDismiss)="isOpen.set(false)"
  [showBackdrop]="showBackdrop()"
  [backdropDismiss]="backdropDismiss()"
  [escapeDismiss]="escapeDismiss()"
>
  <h4 slot="header">Lorem, ipsum dolor</h4>
  <p>
    Lorem ipsum
  </p>
  <p>
    Lorem ipsum dolor sit amet
  </p>
  <div slot="actions">
    <slp-button variant="flat" size="medium" (click)="isOpen.set(false)">
      Lorem
    </slp-button>
    <slp-button variant="primary" size="medium" (click)="isOpen.set(false)">
      Ipsum
    </slp-button>
  </div>
</slp-dialog>
```

# How we consume it
**Is there an NPM package with versions and a changelog?**
For now no, but there will be.
**Can we tree-shake/lazy-load to keep bundles small?**
For now no, but there will be.
**Can we theme (colors, fonts, spacing) without hacks?**
For now yes with some caveats, but this will become simpler in upcoming releases.

# Interoperability
**Clean usage in React/Angular/Vue (events, forms, two-way data)?**
Yes. Stencil build output generates native components for each framework and thus the usage of each is what a React/Angular/Vue developer would expect.
**Any gotchas with routing, modals?**
The routing is something that must be done through the framework selected and by the developer. These are components to be used after the routing is done. 
If by modals you mean dialogs then look at the answer above. If it was a typo and it was "models", we are also exporting the required typings. Although not many are needed.

# SEO & analytics
**Will search engines see meaningful content without waiting for JS?**
This doesn't change as if you were creating the components in the respective framework of choice. 

**Can we attach our analytics/telemetry easily?**
All components have inputs/props and outputs/events. You can easily attach your analytics to each event triggered through the components.

Examples:
* You can listen for click events on a button.
* You can listen for focus/typing/blur events on a field

There are also several events with the same name to keep consistency across components. Like:
* slpChange
* didPresent
* didDismiss
* slpFocus
* slpBlur
* slpShow
* slpHide