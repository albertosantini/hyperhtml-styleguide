# hyperHTML styleguide

*Abstraction is the price you pay for the magic.*

hyperHTML is a DOM & ECMAScript standard compliant, zero-dependency, fully cross platform library suitable for declarative and reactive Web Applications. 

This architecture and styleguide has been written for showing best practices about hyperHTML in my use cases and context, mainly in ES2015 without any transpiling step (and finally without any bundling one).

In the latest years I developed web applications with AngularJS. That framework served me very well and introduced me a lot of concepts used in MVC architectures.

When I chose hyperHTML in early 2017, at the beginning it was natural to reproduce that kind of context, even if the paradigm was different.

Be aware your mileage may vary.

This is an opinionated styleguide for syntax, conventions and structuring hyperHTML applications. Anyway the framework is very flexible, intending it is not too much opinionated. It adds enough abstraction to hide boring details and not too much bells and whistles to hide the platform.

Before thinking there is something wrong in the framework, check the docs (RTFM) and the issues - they are a very worthy reading - and try to discover (again) the original specs (DOM) behind the framework. I need to shift my mind to be used to hyperHTML.

## Table of Contents

1. [Modular architecture](#modular-architecture)
    1. [Theory](#module-theory)
    1. [Root module](#root-module)
    1. [Component module](#component-module)
    1. [Common module](#common-module)
    1. [File naming conventions](#file-naming-conventions)
    1. [Scalable file structure](#scalable-file-structure)
1. [Components](#components)
    1. [Theory](#component-theory)
    1. [Controllers](#controllers)
    1. [Templates](#templates)
    1. [Services](#services)
1. [Utils](#utils)
1. [Made with hyperHTML](#made-with-hyperhtml)
1. [Resources](#resources)
1. [Documentation](#documentation)
1. [Contributing](#contributing)
1. [Credits](#credits)

# Modular architecture

A module component is the root definition for that module that encapsulates the component boostrap, the logic, controller, template, services and, finally, child components.

**[Back to top](#table-of-contents)**

### Module theory

The design in the modules maps directly to our folder structure, which keeps things maintainable and predictable. We should ideally have three high-level modules: root, component and common. The root module defines the base module that bootstraps our app, and the corresponding template. We then import our component and common modules into the root module to include our dependencies. The component and common modules then require lower-level component modules, which contain our components, controllers, templates, services, utilities and tests for each reusable feature.

**[Back to top](#table-of-contents)**

### Root module

The root module may be loaded via script tag with type module or it may be the entry point for your preferred bundler.

`<script type="module" src="app/root.module.js"></script>`

```js
// root.module.js
import "./root.component.js";
import "./common/common.module.js";
import "./components/components.module.js";
```

The root component defines the base element for the entire application.

```js
// root.component.js
import { Util } from "./util.js";
import { RootTemplate } from "./root.template.js";

export class RootComponent {
    static bootstrap() {
        const render = hyperHTML.bind(Util.query("root"));

        RootTemplate.update(render);
    }
}

RootComponent.bootstrap();

```

**[Back to top](#table-of-contents)**

### Component module

A Component module is the container reference for all reusable components. See above how we import `components.module.js` and inject them into the Root module, this gives us a single place to import all components for the app. 

These modules we require are almost decoupled from all other modules and thus can be moved into any other application with ease. Finally there is an explicit dependency between the modules, because there is not a dependency injection system. 

```js
// components.module.js
import "./header/header.module.js";
import "./toasts/toasts.module.js";
import "./stats/stats.module.js";
import "./basket/basket.module.js";
import "./assets/assets.module.js";
import "./mostused/mostused.module.js";
import "./latest/latest.module.js";
import "./other/other.module.js";

```

**[Back to top](#table-of-contents)**

### Common module

The Common module is the container reference for all application specific components, that we don't want to use in another application. This can be things like layout, navigation and footers. See above how we import `common.module.js` and inject them into the Root module, this gives us a single place to import all common components for the app.

```js
// common.module.js
import "./app.module.js";
```

**[Back to top](#table-of-contents)**

### File naming conventions

Keep it simple and lowercase, use the component name, e.g. `calendar.*.js*`, `calendar-grid.*.js` - with the name of the type of file in the middle. Use `*.module.js` for the module definition file, as it keeps it verbose and consistent.

```
calendar.module.js
calendar.component.js
calendar.controller.js
calendar.service.js
calendar.service.spec.js
calendar.template.js
```

**[Back to top](#table-of-contents)**

### Scalable file structure

File structure is extremely important, this describes a scalable and predictable structure. An example file structure to illustrate a modular component architecture.

```
├── app/
│   ├── components/
│   │  ├── calendar/
│   │  │  ├── calendar.module.js
│   │  │  ├── calendar.component.js
│   │  │  ├── calendar.service.js
│   │  │  ├── calendar.service.spec.js
│   │  │  ├── calendar.template.js
│   │  │  └── calendar-grid/
│   │  │     ├── calendar-grid.module.js
│   │  │     ├── calendar-grid.component.js
│   │  │     ├── calendar-grid.controller.js
│   │  │     ├── calendar-grid.template.js
│   │  ├── events/
│   │  │  ├── events.module.js
│   │  │  ├── events.component.js
│   │  │  ├── events.service.js
│   │  │  ├── events.template.js
│   │  │  └── events-signup/
│   │  │     ├── events-signup.module.js
│   │  │     ├── events-signup.component.js
│   │  │     ├── events-signup.service.js
│   │  │     ├── events-signup.service.spec.js
│   │  │     ├── events-signup.template.js
│   │  └── components.module.js
│   ├── common/
│   │  ├── nav/
│   │  │     ├── nav.module.js
│   │  │     ├── nav.component.js
│   │  │     ├── nav.service.js
│   │  │     ├── nav.sevice.spec.js
│   │  │     ├── nav.template.js
│   │  ├── footer/
│   │  │     ├── footer.module.js
│   │  │     ├── footer.component.js
│   │  │     ├── footer.service.js
│   │  │     ├── footer.service.spec.js
│   │  │     ├── footer.template.js
│   │  └── common.module.js
│   │  └── app.module.js
│   │  └── app.template.js
│   │  └── app.component.js
│   │  └── app.css
│   ├── root.module.js
│   ├── root.component.js
│   └── root.template.js
└── index.html
```

The rationale behind this structure is a separation based on feature (folders-by-feature) and not on file type (folders-by-type).

The high level folder structure simply contains `index.html` and `app/`, a directory in which all our root, component, common and low-level modules live along with the markup and styles for each component.

**[Back to top](#table-of-contents)**

# Components

### Component theory

Components are essentially templates with a controller. 

The component is bootstrapped in the module file.

```js
// assets.module.js

import { AssetsComponent } from "./assets.component.js";

AssetsComponent.bootstrap();
```

In the component it is defined the tag used in the markup and it is created the controller instance.

```js
// assets.component.js
import { Util } from "../../util.js";
import { AssetsTemplate } from "./assets.template.js";
import { AssetsController } from "./assets.controller.js";

export class AssetsComponent {
    static bootstrap() {
        const render = hyperHTML.bind(Util.query("assets"));

        this.assetsController = new AssetsController(render, AssetsTemplate);
    }

    static update() {
        this.assetsController.update();
    }
}
```

Generally speaking, the component interface contains only the `bootstrap` api, but when the component is updated from another one, there is also an `update` api, eventually passing the new state.

#### Note

There are different approaches how to create a `component`, described in the docs, and based on `hyper.Component` and `HyperHTMLElement`. This topic would deserve a complete dedicated section.

**[Back to top](#table-of-contents)**

### Controllers

Controllers should only be used alongside components, never anywhere else. If you feel you need a controller, what you really need is likely a stateless component to manage that particular piece of behaviour. Basically a controller contains the logic of the user actions in the template.

```js
// asset.controller.js
import { Util } from "../../util.js";
import { AssetsService } from "./assets.service.js";
import { BasketComponent } from "../basket/basket.component.js";
import { StatsComponent } from "../stats/stats.component.js";
import { ToastsComponent } from "../toasts/toasts.component.js";

export class AssetsController {
    constructor(render, template) {
        this.render = render;
        this.template = template;
        this.events = (e, payload) => Util.handleEvent(this, e, payload);
        this.state = {
            // cut
        };

        this.update();
    }

    update() {
        // cut

        this.template.update(this.render, this.state, this.events);
    }

    onAssetClick(e, item) {
        // cut
    }
}

```

So `render` and `templates` are passed by the component instance. The controller passes `events` and `state` to the template.

You may use an observer pattern, intending to update automagically the template, when the state is modified.

```js
// activity.controller.js
export class ActivityController {
    constructor(render, template) {

        this.state = Introspected({
            activities: []
        }, state => template.update(render, state));

        this.activityService = new ActivityService(this.state.activities);
    }
}
```

If you use a third party lib (for instance `Introspected`), watching an object or array for changes, the state (or a slice of the state) is passed to the service. Finally, when the componenent (or other ones) change the properties in the model, using the service methods, the template is update.

Stretching a bit further this approach, you may use a state container.

**[Back to top](#table-of-contents)**

### Templates

```js
// assets.template.js
import { Util } from "../../util.js";

export class AssetsTemplate {
    static update(render, state, events) {
        if (!state.assets.length) {
            /* eslint-disable indent */
            render`
                <h2>Assets</h2>

                <p>No assets.</p>
                <p>Add, at least, three or more assets from Basket to get an optimal portfolio.</p>
            `;
            /* eslint-enable indent */

            return;
        }

        const headerClasses = "fw6 bb b--black-20 tl pb1 pr1 bg-white";
        const trClasses = "pv1 pr1 bb b--black-20";
        const trClassesLink = `${trClasses} pointer dim`;
        const trClassesNumber = `${trClasses} tr`;

        /* eslint-disable indent */
        render`
            <h2>Assets</h2>

            <table class="f7 mw8 pa2" cellpsacing="0">
                <thead>
                    <th class="${headerClasses}">Symbol</th>
                    <th class="${headerClasses}">Description</th>
                    <th class="${headerClasses}">Weight TD</th>
                    <th class="${headerClasses}">Weight YOY</th>
                </thead>

                <tbody>${state.assets.map((asset, index) =>
                    hyperHTML.wire()`<tr>
                        <td id="${`asset-${asset.symbol}`}" onclick="${e => events(e, asset)}"
                            class="${trClassesLink}"
                            title="Click to remove the asset">${asset.symbol}</td>
                        <td class="${trClasses}">${asset.name}</td>
                        <td class="${trClassesNumber}">${Util.formatNumber(state.weightsTD[index] * 100, 1)}%</td>
                        <td class="${trClassesNumber}">${Util.formatNumber(state.weightsYOY[index] * 100, 1)}%</td>
                    </tr>`)
                }</tbody>
            </table>
        `;
        /* eslint-enable indent */
    }
}
```

The css classes are embedded in the template, a literal template, with `wire` partials.

#### Note

```js
id="${`asset-${asset.symbol}`}" onclick="${e => events(e, asset)}"
``` 

- Be aware partial attributes are not allowed.
- The event handler is automagically `onAssetClick` due to `Util.handleEvent`.
- `asset` is the payload of the event handler.

**[Back to top](#table-of-contents)**

### Services 

Services are essentially containers for business logic that our components shouldn't request directly.

```js
// assets.service.js
import { LatestComponent } from "../latest/latest.component.js";

const CONPA_ASSETS = "conpa.assets";

export class AssetsService {
    static getAssets() {
        return AssetsService.assets;
    }

    static addAsset(item) {
        const assets = AssetsService.assets;
        const isNew = assets
            .filter(asset => item.symbol === asset.symbol)
            .length === 0;

        if (isNew) {
            assets.push(item);
        }

        return isNew;
    }

    static removeAsset(item) {
        const assets = AssetsService.assets;
        let isRemoved = false;

        assets.forEach((asset, index) => {
            if (item.symbol === asset.symbol) {
                assets.splice(index, 1);
                isRemoved = true;
            }
        });

        return isRemoved;
    }

    static getLastAsset() {
        return AssetsService.assets.slice(-1)[0];
    }

    static saveAssets() {
        localStorage.setItem(CONPA_ASSETS, JSON.stringify(AssetsService.assets));
    }

    static calcOptimalPortfolioYOY() {
        const refDate = new Date(new Date().getTime() - (1000 * 60 * 60 * 24 * 365));

        return AssetsService.calcOptimalPortfolio({
            referenceDate: refDate.toString()
        });
    }

    static calcOptimalPortfolio({ referenceDate = new Date().toString() } = {}) {
        return workway("node://finance.js").then(async({ namespace: finance }) => {
            const lows = [];
            const highs = [];

            const prods = AssetsService.assets.map((asset, index) => {
                lows[index] = 0;
                highs[index] = -1;

                return asset.symbol;
            });

            const ptfParams = {
                prods,
                referenceDate,
                targetReturn: null,
                lows,
                highs
            };

            return finance.getOptimalPortfolio(ptfParams).then(res => {
                if (res.message) {
                    throw new Error(res.message);
                }

                finance.savePortfolio({
                    symbols: prods,
                    weights: res.optim.solution,
                    ref: referenceDate,
                    ret: res.optim.pm,
                    risk: res.optim.ps,
                    perf: res.perf,
                    highs,
                    lows
                }).then(() => {
                    LatestComponent.update();
                });

                return res;
            }).catch(err => {
                throw new Error(err);
            });
        });
    }
}

AssetsService.assets = JSON.parse(localStorage.getItem(CONPA_ASSETS)) || [];

```

#### Note

Here, in this service, there is an example of `workway` approach: it is a general purpose, Web Worker driven, remote namespace with classes and methods.

**[Back to top](#table-of-contents)**

# Utils

The following utilities allow to improve the error handling and, finally, to generalize the event handling for different targets.

```js
// utils.js
export class Util {
    static query(selector) {
        return document.querySelector(selector) ||
            console.error(selector, "not found");
    }

    static handleEvent(context, e, payload) {
        const type = e.type;
        const id = e.target.id || console.warn(e.target, "target without id");
        const method = `on${id[0].toUpperCase()}${id.split("-")[0].slice(1)}` +
            `${type[0].toUpperCase()}${type.slice(1)}`;


        return method in context ? context[method](e, payload)
            : console.warn(method, "not implemented");
    }
}
```

See the `Controllers` section above for the usage.

**[Back to top](#table-of-contents)**

# Made with hyperHTML

* [Argo](//github.com/albertosantini/argo), a forex trading platform.
* [ConPA](//github.com/albertosantini/node-conpa), an asset allocation app. 

**[Back to top](#table-of-contents)**

# Resources

* [Patterns For Large-Scale JavaScript Application Architecture](//addyosmani.com/largescalejavascript/)
* [Web technology For developers](//developer.mozilla.org/en-US/docs/Web/JavaScript)

**[Back to top](#table-of-contents)**

# Documentation
For anything else, including API reference, check the [hyperHTML documentation](//viperhtml.js.org/hyperhtml/documentation/).

**[Back to top](#table-of-contents)**

# Contributing

Open an issue first to discuss potential changes/additions.

**[Back to top](#table-of-contents)**

# Credits

* [@WebReflection](//twitter.com/webreflection) for creating hyperHTML.
* [@toddmotto](//twitter.com/toddmotto) for the original styleguide ([AngularJS styleguide](//github.com/toddmotto/angularjs-styleguide), a masterpiece) and for the great posts on AngularJS.
* [@john_papa](//twitter.com/john_papa) for the first styleguide ([Angular 1 Style Guide](https://github.com/johnpapa/angular-styleguide/tree/master/a1)) I read how to structure a web application.

<a href="//js.org" target="_blank" title="JS.ORG | JavaScript Community" class="pull-right">
<img src="//logo.js.org/dark_horz.png" width="102" alt="JS.ORG Logo"/></a>
<!-- alternatives [bright|dark]_[horz|vert|tiny].png (width[horz:102,vert:50,tiny:77]) -->

**[Back to top](#table-of-contents)**
