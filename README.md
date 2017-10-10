# ngx-bootstrap-product-tour

## About

This is a product tour library built with Angular (2+). It's inspired by [ngx-tour](https://github.com/isaacplmann/ngx-tour).
`NgxBootstrapProductTourModule` is an implementation of the tour ui that uses [ngx-bootstrap](https://valor-software.com/ngx-bootstrap)  popovers to display tour steps.

## Installation

1. `npm i ngx-bootstrap-product-tour`
2. Import `NgxBootstrapProductTourModule.forRoot()` into your app module
3. Make sure `RouterModule` is imported in your app module
4. Include bootstrap css somehow - i.e. in your `index.html` add this line:
    `<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/css/bootstrap.min.css" integrity="sha384-/Y6pD6FV/Vv2HJnA6t+vslU6fwYXjCFtcEpHbNJ0lyAFsXTsjBbfaDjzALeQsN6M" crossorigin="anonymous">`
    or if you are using angular-cli add to `angular-cli.json` add under styles
    ` "../node_modules/bootstrap/dist/css/bootstrap.min.css"`

## Usage

1. Add `<ngx-bootstrap-product-tour></ngx-bootstrap-product-tour>` to your root app component
2. Define anchor points for the tour steps by adding the `tourAnchor` directive throughout your app.

    ```html
    <div tourAnchor="some.anchor.id">...</div>
    ```
3.  Define your tour steps using `tourService.initialize(steps)`

    ```typescript
    this.tourService.initialize([{{ '{' }}
      anchorId: 'some.anchor.id',
      content: 'Some content',
      title: 'First',
    }, {{ '{' }}
      anchorId: 'another.anchor.id',
      content: 'Other content',
      title: 'Second',
    }]);
    ```
4. Start the tour with `tourService.start()`
5. Check out the [demo source code](https://nmilicic.github.io/ngx-bootstrap-product-tour/) foran example.

## TourService

The `TourService` controls the tour. Some key functions include:
| Function | Description |
| :-------------|:-------------|
|start()|Starts the tour|
|startAt(stepId: number \| string)|Start the tour at the step with stepId or at the specified index|
|end()|Ends the tour|
|pause()|Pauses the tour|
|resume()|Resumes the tour|
|next()|Goes to the next step|
|prev()|Goes to the previous step|

## Step Configuration

Each step can have the following properties.

| Name | Type | Default | Description |
| --- | --- | --- | --- |
| stepId | string |  "" | A unique identifier for the step |
| anchorId | string | required | The anchor to which the step will be attached |
| title | string |  "" | The title of the tour step |
| content | string |  "" | The content text of the tour step |
| route | string | UrlSegment[] | undefined | The route to which the tour should navigate before attempting to show this tour step. If undefined, no navigation is attempted. |
| nextStep | number | string | undefined | The step index or stepId of the next step. If undefined, the next step in the steps array is used. |
| prevStep | number | string | undefined | The step index or stepId of the previous step. If undefined, the previous step in the steps array is used. |
| placement |  'top' | 'bottom' | 'right' | 'left' | 'top' | Where the tour step should placed with respect to the anchor. 'before' and 'after' are synonyms for 'left' and 'right' respectively.
        When RTL support is implemented, 'before' and 'after' will swap directions when RTL mode is enabled. |
| preventScrolling | boolean | false | Tour steps automatically scroll to the middle of the screen, if they are off the screen when shown. Setting this value to
        true will disable the scroll behavior. |

## Defaults

You can set default values in the `TourService.initialize` function.
```typescript
this.tourService.initialize(steps, {{ '{' }}
  route: '',
  placement: 'left',
  preventScrolling: true,
});
```
Any value explicitly defined in a step will override the default value.

## Event Observables

The `TourService` emits events that can be subscribed to like this:

```typescript
this.tourService.initialize$.subscribe((steps: IStep[]) => {{ '{' }}
  console.log('tour configured with these steps:', steps);
});
```

| Name | Payload | Emitted When |
| --- | --- | --- |
| stepShow$ | IStep | A step is shown |
| stepHide$ | IStep | A step is hidden |
| initialize$ | IStep[] | The tour is configured with a set of steps |
| start$ | IStep | The tour begins |
| end$ | any | The tour ends |
| pause$ | IStep | The tour is paused |
| resume$ | IStepI | The tour resumes |
| anchorRegister$ | string | An anchor is registered with the tour |
| anchorUnregister$ | string | An anchor is unregistered from the tour |

## Custom template

  You can also customize the tour step template by providing an `<ng-template>` inside the `<ngx-bootstrap-product-tour>`

  The default template is equivalent to this:

```html
<tour-step-template>
  <ng-template let-step="step">
    <p class="tour-step-content">{{ '{{' }}step?.content}}</p>
    <div class="tour-step-navigation">
      <button *ngIf="tourService.hasPrev(step)" class="btn btn-sm btn-default" (click)="tourService.prev()">&#xAB; Prev</button>
      <button *ngIf="tourService.hasNext(step)" class="btn btn-sm btn-default" (click)="tourService.next()">Next &#xBB;</button>
      <button class="btn btn-sm btn-default" (click)="tourService.end()">End</button>
    </div>
  </ng-template>
</tour-step-template>
```