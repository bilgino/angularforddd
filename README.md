# Introduction (WORK IN PROGRESS)
An introduction for building large-scale enterprise applications with Angular.

# Application architecture 

Applying Object-Oriented Design, Domain-Driven Design and Command-Query-Responsibility-Segregation in the frontend design system, we break down complex requirements 
into logical boundaries and divide business logic into layers with different responsibilities to keep our code in good condition.

## Frontend coupled to OOD, DDD and CQRS

The building blocks of Angular already provides us with code organisation strategies. Nevertheless, to gain a better design we will bypass the 
traditional data-driven approach and consider strategies like Domain-Driven Design and Command-Query-Responsibility-Segregation:

![](src/assets/images/frontend_arch.png)

## Object-Oriented Design

Although functional programming has gained a strong foothold in frontend development in recent years, a consistent object-oriented approach
is better suited for Angular projects. Object-oriented design allows us to approach a more human-readable code base, where the UL
(Ubiquitous language) can help to design a better taxonomy and complex data types. It's important to note, Angular embraces both programming
paradigms (FP & OOP).

**» Applying SOLID principles**<br/>

In object orientation the SOLID principles can help to make better design decisions (high cohesion and low coupling). Applying the Dependency
Inversion Principle, we ensure that layers depend on abstraction as opposed to depending on concretion. => Programming to an Interface!

For example, we provide the domain layer as an abstraction by using interfaces / type aliases.

**» Applying cross-cutting concerns**<br/>

The infrastructure layer includes cross-cutting concerns such as logging, caching or security. A naive approach to implement cross-cutting
functionality usually leads to duplicated or coupled code, which violates Don't Repeat Yourself and Single Responsibility Principle.
The Aspect Oriented Programming promotes an abstraction and encapsulation of cross-cutting concerns by interlacing additional code,
resulting in loose coupling between the actual logic and the infrastructure logic. For more information about AOP in TypeScript please
visit the following website: https://jaxenter.com/cross-cutting-concerns-angular-2-typescript-128925.html

## Layered architecture

Considering multilayered architectures, the question arises of how to organize layers in SPA applications? This question refers to code splitting,
communication across layers and demanding business logic through services. The multilayered architecture consists of the following layers:

**» Horizontal cut**<br/> Cutting the application into layers...

![](src/assets/images/layers_hc.png)

**» Vertical cut**<br/> Cutting the application into features / use cases...

![](src/assets/images/layers_vc.png)

**» Cross cut**<br/> Cutting the application into modules...

![](src/assets/images/layers_cc.png)

*» Abstraction layers*<br/>

- Presentation layer: Components, Widgets, User Interface Services
- Application layer: Use Case Services, View Models, View Model Providers <br/>
- Domain layer: Aggregates, Entities, Value Objects <br/>
- Infrastructure layer: Repositories <br/>

*» Service layers* <br/>

- User Interface services carry out ...
- Application services carry out business and UI use cases and are procedural 
- Domain services carry out business use cases at a higher level than entities or value objects
- Infrastructure services help to separate technical and business concepts <br/>

*» Validation layers*<br/>

- Application layer: Data types (null, undefined), format (length, empty, whitespace), schema (email, creditcard)
- Domain layer: Business/Domain Rules, Domain Invariants<br/>

Examples - Presentation layer: *ModalDialog, PopUp*<br/>
Examples - Application layer: *Authentication, Search*<br/>
Examples - Infrastructure layer: *Repository, Persistence, Caching, Messaging, Crypto, Converter, Validation, Translation*<br/>
*Logging, Error, Tracing, Security, Configuration, Token, Monitoring, Date*

*» Angular adoption*<br/>

- Presentation layer: Components, Directives, Templates, Pipes, Animations
- Application layer: View Controller Classes, Guards, Validator Functions <br/>
- Domain layer: Classes <br/>
- Infrastructure layer: Resolvers, Interceptors<br/>

**» Applying DDD to Angular**<br/>

An important aspect of Domain-Driven Design is that the complexity of the domain model is kept isolated from other concerns of the application. 
Ideally, the domain layer is self-contained and focused on abstracting the business domain. Very often frontend applications evaluate business rules 
that will immediately be reflected in the presentation layer, especially in SPA applications when navigating through HTML forms that have 
cross-dependencies in terms of composite business rules. A dedicated domain layer supports us to prevent domain logic from scattering everywhere. 
In addition to that, we don't want to command against the server upon every user input; therefore, a domain layer in the frontend sounds like a 
good idea.

Domain-oriented layering is often considered the first structuring criterion in Angular applications. However, layered architecture 
is independent of vertical structuring criterion. It's sufficient to comply with horizontal layers. The main reasons for modular segmentation in 
Angular is lazy-loading, scoping and distribution. 

When application services carry out business or UI use cases, it may be a good idea to write use cases that contain less logic directly in the 
UI controller, like in the MVC pattern. However, we don't want to hide use cases from the rest of the application and use dedicated classes instead! 
Additionally, we may want to share state and logic of that dedicated class with other independent components. 

It's questionable whether higher granularity distributed across several layers introduce extra complexity in the frontend design system. 
Should we really comply with Domain-Driven Design in frontend development? As a consequence, many developers tend to lean toward weaker 
patterns because they see it as an unnecessary practice. Often a simpler data-driven approach is sufficient. For most web applications MVC 
or Flux/Redux may be more appropriate. Before starting using advanced concepts we must evaluate incoming requirements.

# Angular core patterns

Angular's core patterns such as modules, services, entities, factories affords us to comply with domain-focused principles.

## Modules

The angular.io styleguide states categories for organizing blocks of code: **Shared Modules** and **Widget Modules** contain the 
most commonly used code, while **Domain Modules** encapsulate blocks of code, that is not intended to be used outside that module, 
makes **Domain Modules** a good candidate for the bounded context pattern. The **Service Module** shares its content application 
wide as singletons. The **Root Module** includes several domain modules. That is, the entry point is the root module. For a more complete 
overview, visit the following website https://angular.io/guide/module-types#summary-of-ngmodule-categories

Angular's modular system gives a clean design response:  

**» Modular architecture**<br/>

![](src/assets/images/module_arch.png)

**» Examples**<br/>

`Service Module`: Application wide services as singletons e.g. *AuthenticationService, LoggerService*<br/>
`Shared Module`: Highly reusable components as non-singletons e.g. *DropDownComponent, PaginatorComponent*<br/>
`Domain Module`: Domain modules such as *OrderModule* (Bounded Context) or *SalesModule* (Bounded Context)<br/>
`Widget Module`: Highly cohesive and reusable widgets e.g. *MatSidenavModule, MatSnackBarModule* <br/>

**» Module guidelines**<br/>

Following checklist can help to facilitate the orchestration of ngModules:<br/>

- Every component, directive and pipe must belong to **one** and **only one** module
- **Never** re-declare these elements in another module
- **Do not** share contents of a domain module, instead add reusable elements to a shared module
- **Do not** use the providers array of a module to register global services (use provideIn)
- Module content is private by default and only visible to its own content
- Transitive dependencies aren't visible, reexport them to make them available to other modules
- Module content can be exported without being imported

**» Bounded context pattern**<br/>

The bounded context pattern in Domain-Driven Design divides the domain model into related domain fragments. In a service-based environment a 
bounded context marks the boundaries of an application service. An application service is a concretion of the bounded context pattern! 
This is similar to **Domain Modules** where we mark the boundaries based on features. Applying the bounded context pattern to domain modules 
allows us to structure modules in a domain-driven approach. A bounded context should consist of at least one aggregate and may consist of 
several aggregates. An important aspect with regard to SPA applications is that the (client- or server-side) bounded context must integrate 
a REST interface because the router engine in Angular complies with the navigational behaviour of hypermedia APIs. A bounded context should not only
be coupled to the URI of the entry point (root): `/BoundedContextA/*API`; `/BoundedContextB/*API`. 
**A bounded context can be assigned either to an entire page or to page segments**.

Interaction between the bounded context pattern and domain modules:

![](src/assets/images/BoundedContext.png)

**» Project scaffolding**<br/>

A common practice in Angular projects is to structure the project into `/core`, `/shared`, `/features` folders. 
Unfortunately this approach isn't sufficient for a complex projects and is mainly inspired by technical constraints. 
When setting up a project, we should use a folder structure that is driven by business logic.

Domain-driven folder structure for Angular applications:

![](src/assets/images/scaffolding.png)

**» Customizing**<br/>

When deciding on a design, customizing the look of default components can quickly become challenging.
Creating frontends that feature modular and reusable CSS without overriding existing CSS rules is an important part of
every frontend project. Usually every component in an Angular project appears in the default style and serves a singular purpose.
Design adjustments for reusable components to specific content areas can be implemented with the `:host()` and `:host-context()` selectors.

![](src/assets/images/Customizing.png)

Unfortunately the `:host-context()` CSS pseudo-class function lacks browser support. Luckily, Angular supports an emulated version of it 
in the default settings of the view encapsulation mode.

## Models 

The model in the classic MVC pattern is a representation of application data. The model contains code to create, read, update and delete or 
transform model data. It stores the domain knowledge and is very similar to the Repository pattern! The differences between the various patterns 
come down to a historical context of the model: Data Model (MVC), Resource Model (REST), Domain Model (DDD), View Model (UX), Class Model (UML), 
Entity Model (ERM) and so forth. 
  
Angular promotes two types of models:

- `View Model`: This object represents data required by a view. It doesn't represent a real world object
- `Domain Model`: This object represents data and logic related to the business domain

The view model and domain model should maintain different schemas to keep the domain model separate from view properties

**» Implementation patterns**<br/>

- Anemic Domain Model
- Rich Domain Model
- View Model 

The anemic domain model is quite often used in CRUD-based web applications as value container, conform to RESTful practices. 
The anemic domain model, however, is considered an anti-pattern because it does not contain business logic except `get` and `set` (CRUD) methods. 
It introduces a tight coupling with the UI controller and can't protect its invariants. Hence, the rich domain model is a more suitable candidate. 
Using rich domain model implementations, we prevent domain logic scattering everywhere multiple times. 
The following example shows the negative side effects when using anemic domain models. 

Domain logic coupled in the UI controller: 

*»  Effects of anemic models* <br/> 
```
@Component({
    selector: 'emp',
    templateUrl: './emp.component.html'
}) class EmployeeComponent {
    @Input() emp: Employee; 

    public salaryIncreaseBy(percent:number){
         emp.salary = (emp.salary * percent / 100) + emp.salary;
    }
}
```

A rich domain model instead hides and encapsulates domain logic:

*»  Effects of rich models*<br/>
```
@Component({
    selector: 'emp',
    templateUrl: './emp.component.html'
}) class EmployeeComponent {
    @Input() emp: Employee; 

    public salaryIncreaseBy(percent:number){
         emp.salaryIncreaseBy(percent);
    }
}
```

In the second example, the domain logic is decoupled from the UI controller. Encapsulation protects the integrity of the model data.
Keeping the model as independent as possible improves usability and allows easier refactoring.
Neither domain state nor domain logic should be developed as part of UI controllers.

Using business services only for structural and behavioral modeling while domain models remain pure value containers that can't protect their
integrity (invariants) is a common bad practice in frontend projects. Hence, building rich domain models is a major objective in
object-oriented applications.

```
@Injectable({
  providedIn: 'root'
})
export class AccountService {
    private accounts = [{ id: 1, balance: 1200 }];

    constructor() { }

    changeBalance(id: number, amount: number) {
        if (id > 0 && amount < AMOUNT.MAX_VALID) {
            this.accounts[id].balance += amount;
        }
        return this.accounts[id].balance;
    }
}
```

Another better solution is to create rich domain models that encloses domain logic:

```
@Injectable({
  providedIn: 'root'
})
export class AccountService {

    constructor(//Inject Account Repository) { }

        changeBalance(id: number, amount: number): void {
            if (id) {
                const account = this.accountRepositoryService.getAccountById(id);
                account.updateBalance(amount);
            }
        }
}

class Account {
    readonly id: number;
    balance: number;

    constructor() {}
    
    updateBalance(amount: number): number {
        if (amount < AMOUNT.MAX_VALID) {
            this.balance += amount;
        }
    }
}

```

In general, using rich domain models means more entities than business services.

**» Mapper pattern**<br/>

By implementing a domain layer in the frontend, we ensure that business behavior works. 
With the higher functional ability using domain models, we should take the mapper pattern into consideration. 
A common practice for the reason of typesaftyness is to declare interfaces in support of plain JavaScript object literals. 
In the context of mapping, it's important to make a clear distinction between the typing system in TypeScript and the data structure of models.

Mapping JSON-encoded server data in the frontend is mandatory if:

- The domain model object defines any methods
- The schema in the database is different from its representation in the application
- The typing system should consist of classes instead of interfaces or type aliases

The Mapper pattern transfers data between two different schemas:

![](src/assets/images/data_mapper.png)

Let's have a look at how to map the server response schema:

```
read(): Observable<Customer[]> {
    return this.http.get<Customer[]>("/api/customers")
        pipe(
            map((customers: Customer[]) : Customer[] => {
                let result: Customer[] = [];
                customers.forEach((customer:Customer) => {
                    result = [new Customer(customer.firstName, customer.lastName), ...result];
                });
                return result;
            }),
            catchError(()=>{})
        );
};
```

The data mapper is associated to the repository to elaborate an appropriate (view or domain) model schema. 

**» Translator pattern (Mapping VM to DM and vice versa, XMapper.toXY)**<br/>

@TODO [text]
@TODO [image]

**» REST, HATEOAS & CO.**<br/>

When building multi-layered, distributed web applications, data transformation is among the major challenges that occur when data traverses 
all layers (data flows up and down the stack). If the domain layer has been replicated to the client-side, we may need to transform the 
server response schema to a complex domain model: 

![](src/assets/images/Mapper_Response.png)

For example, HATEOAS forms hyperlinks between external resources to make transitions through the application state by navigating hyperlinks. 
However, mapping hyperlinks to a client-side domain model is not desirable! In addition to consuming REST APIs, very often multiple HTTP request 
need to be sent asynchronously to assemble a model for a specific use case in the presentation layer. If the applied HATEOAS implementation pattern
forms hyperlinks in a response schema, it would limit the user interface to incorporate with REST APIs synchronously. 
UX designers usually don't model their interaction, navigation and screen patterns around HATEOAS. Furthermore, the Angular router engine doesn't 
comply well with the URI templates of HATEOAS  implementation patterns. HATEOAS has its advantages as well as disadvantages. Even though the router in Angular 
complies with the navigational behaviour of hypermedia APIs, you should avoid HATEOAS for Angular SPA applications!

**» Domain model**<br/>

@TODO [text]
@TODO [image]

**» View model**<br/>

@TODO [text]
@TODO [image]

**» Model declaration strategies**<br/>

@TODO [text]
@TODO [image]

## Services

Singleton services are important concepts in Angular applications. Most of the functionality that doesn't belong to UI components 
will be written as part of the service layer, which we will abstract in form of application-, domain- and infrastructure services. 
We will also implement the repository pattern in favor of state management services. 

Following checklist can help to facilitate the orchestration of providers:

**» Services shared through the module providers array**<br/>

-	**Never export a service**: Services added to the `providers` array of a module are registered at the root of the application, making them available for injection to any class in the application. They already shared as an application wide singleton
-	**Do not** add services to the `providers` array of a shared module, instead create a service module with a set of services and import them once into the root module
- Services must be registered at the root of the application, making them available to other services
-	For lazy-loaded modules, please see official Angular documentation

**» Services shared through the component providers array**<br/>

-	The component `providers` array will request a service instance from the injector and shares the service class with its children as singleton
-	If a component instantiated more than once, a new service instance will be injected to the respective component
- Use DI lookup hooks `@Host, @Optional, @Skip or @SkipSelf` for dependency management 

**» Services shared through the provideIn property**<br/>

@TODO [text]
@TODO [image]

**» Stateful services vs. stateful repositories**<br/>

Just as mentioned before, it's common for Angular projects to use services for business functionality or state management. 
We use stateful services if we need to share data across independent components. 
Often simple services process HTTP requests and responses that perform CRUD operations. 
**We will deviate from that and use reactive repositories in favor of a reactive data store**. 
A repository serves as a central shared state repository used by other independent components. 
Repositories are not just for Entities, but for all domain objects including anemic domain or view models.

In addition to that, we can introduce the CQRS pattern to stem the heavy-lift when building complicated page flows and user interfaces. 
The CQRS pattern supports us to answer different use cases with the respective model structure. State changes in repositories will immediately 
replicate back to a view model provider (read side). This process is called "projection". Projections can be leveraged in several ways or layers. 
The most common approach is an event-based projection causing an eventually consistent system. However, we will not encounter any problems of this kind,
due to the reactive design pattern of Angular (RxJS). 

**A reactive API exposes hot observables (BehaviorSubjects etc.)** to manage the complexity of asynchronous data handling. If we share data with 
other components, we must keep track of changes by applying reactivity to prevent stale data and keep the UI in sync. Hence, we ensure 
"eventual consistency" that normally arises when CQRS spans the client- and server-side, won't occur. RxJS gives us many great tools and 
operators to implement the "projection phase" between the read and write side. 

![](src/assets/images/Reactive_Flow.png)

Application services usually provide query methods for retrieving view models of domain state, besides command methods (CQS). However, for 
complicated page flows and user interfaces it would be inefficient to create view models in an application service method requiring many dependencies.
By using a view model provider however, we facilitate access to view models in a more efficient manner. Consequently, the application service uses the 
view model provider to report presentation data. 

![](src/assets/images/QuerySideService.PNG)

This might seem more complex than just using a single state management service. The level of abstraction is up to the developer and requirements. 

**» Why CQRS in the frontend?**<br/>

With traditional CRUD-based web applications conform to the REST architectural style and the single data model approach, 
we may fall into the situation where we have to stitch together several resources to build a rich view models . 
Even in the case of sophisticated Web APIs, it's likely that we will encounter these problems. Developers often implement the 
mapper pattern in UI controllers to elaborate view models. Which in the end leads to fat and unmanageable UI controllers: 

![](src/assets/images/Up_Down_Flow.png)

The domain model focuses on invariants and use cases rather than reporting needs. Introducing view model providers in the frontend for the purpose 
of building complicated page flows and user interfaces allows us querying appropriate dependencies that are necessary to view properties. 
For complicated page flows and user interfaces the CQRS pattern can help to avoid over-bloated single models for every use case scenario. 
A view model provider is a perfect layer to pre-compute filtering and sorting logic (https://angular.io/guide/styleguide#style-04-13). 
An important aspect which is neglected by many frontend developers. 

A view model provider in the frontend design system has many advantages:

- Separating concerns of each data model
- Unidirectional data flow 
- Composing several API endpoints 
- Immutable view models complies with the `.onPush` strategy
- sort() and filter() pipes can be detached from templates (https://angular.io/guide/styleguide#do-not-add-filtering-and-sorting-logic-to-pipes)

**» Projection patterns**<br/>

The "projection by entity" pattern makes domain events and eventual consistency redundant as changes will be reflected almost simultaneously. 

![](src/assets/images/VMPRO.png)

Let's have a look at how to keep models in sync using factory methods:

```
class Order {
    private orderId: number;
    private quantity: number; 

    public getOrderForSalesView(): OrderForSalesView {
        return new OrderForSalesView(this.quantity);
    }

    public getOrderForCatalogView(): OrderForCatalogView {
        return new OrderForCatalogView(this.orderId);
    }
}
```

Elaborating view models of domain entities violates the single responsibility rule! 
Using abstract classes, we can aggregate reusable factory methods:

```
abstract class OrderViewModel {
    abstract orderId:number;
    abstract quantity:number;

    public getOrderForSalesView(): OrderForSalesView {
        return new OrderForSalesView(this.quantity);
    }

    public getOrderForCatalogView(): OrderForCatalogView {
        return new OrderForCatalogView(this.orderId);
    }
}

class Order extends OrderViewModel {
    private orderId: number;
    private quantity: number; 
}
``` 

This implementation has some drawbacks either. It only works for a single entity! 
What if a view model requires several sources? 
When building complicated page flows and user interfaces that require several sources (aggregates), we should create a helper class in form of a view model provider. 
The purpose of the view model provider is to encapsulate and build view models for specific use cases. 

```
@Injectable()
class OrderViewModelProvider {
   
    constructor(
      private orderRepository: OrderRepository,             
      private productRepository: ProductRepository,         
      private dateService: DateService                      
      private translateService: TranslationService              
      ){}

    public getOrdersByStatus(status:string): Observable<Array<OrderViewModel>> {
        return this._orderRepository.getAll()
        .pipe(
          groupBy(status),
          filter(status),
          map(()=>{
             return translateService(data);             
          })
          mergeMap(()=>{
             return of([new OrderViewModel(data)...]);             
          })
        )
    }
    
    public getProductsByOrderId(id:number): Observable<Array<ProductViewModel>> {
        return combineLatest([this._orderRepository.getOrders(), this._productRepository.getProducts()])
        .pipe(
          filter(id),
          mergeMap([...]) => {
            return of([new ProductViewModel(data)...]);           
          }),
          shareReplay(1)
        )        
    }
    ...
}
``` 

Requesting the view model provider in the view controller class:

```
@Component({
  selector: 'order',
  template: `...`,
  providers: [OrderViewModelProvider]
})
export class OrderComponent {

  constructor(private orderViewModelProvider: OrderViewModelProvider) {
    this.orderViewModelProvider.getOrdersByStatus('pending').subscribe(()=>{})
  }
}
``` 

View model abstractions can also be achieved in Angular resolver services:

```
@Injectable()
export class OrderResolver implements Resolve<Order> {

    constructor(
      private orderRepository: OrderRepository,             
      private productRepository: ProductRepository,         
      private dateService: DateService                      
      private translateService: TranslationService              
      ){}

    resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<Order> {
        const id = route.paramMap.get('id');
        return combineLatest([this._orderRepository.getOrders(), this._productRepository.getProducts()])
        .pipe(
          filter(id),
          mergeMap([...]) => {
            return of([new OrderViewModel(data)...]);           
          })
        )  
    }
}
``` 

**» CQRS and the Command Pattern**<br/>

@TODO [text]
@TODO [image]

# State Management 

With Single Page Applications (SPA), we get the flexibility and cross-platform functionality of a web application as well as the 
client state management of native applications. Typically, SPA applications have more complex states than traditional server-side 
applications. In Angular applications, stateful services are the first choice to share state beyond the lifetime of components. 

There are an array of different states to deal with:

Domain State | Addressable State (URL) | Draft State | Persisted State | UI State | Session State | Application State |
------------|------------------|-------------|-----------------|--------------|--------------|--------------|
Entity | Sort/Filter/Search | E-Mail, Comments | IndexedDB, Local Storage | Scroll-position| Cookies, Session Storage | Online/Offline|

## Domain state   

**» Enity**<br/>

Create an entity to manage the domain state of an application. The entity encapsulates methods, that need to operate on that data.

```
class Customer {
    private firstName: string;
    private lastName: string;
        
    get firstName(){}
    set firstName(){}
    get lastName(){}
    set lastName(){}
    
    public isLoggedIn(){}
}
``` 

**» Reactive CUD Repository**<br/>

By implementing a CQRS-oriented repository, we share state and communicate domain state changes through reactive operators (RxJS BehaviorSubjects),
along with the operations, transformations, and rules for creating, manipulating and storing domain state, emitting data anytime a business action
occurs.The repository is allowed to perform data queries, but we don't use the repository for reporting! Unlike the Redux pattern where all the 
state got located in a single central store, we undoubtedly must think a bit more complex on how to manage state crossing several layers. 

Let's have a look at how to define a shared reactive CUD repository: 
           
```
@Injectable()
export class DomainModelRepository<T> extends ... {
    private _data : BehaviorSubject<T[]> = new BehaviorSubject<T[]>([]);
    public data$ = this._data.asObservable();
    
    constructor(private httpClient: HttpClient){}
    
    public findById(...): Observable<T> {}
    public create(...): void {}
    public update(...): void {} 
    public delete(...): void {}
}
```

## Router state

Angular's router service allows us to manage domain and UI state. Put simply, the router state determines which components are visible on the screen, and 
it manages navigation through application state (HATEOAS). Any state transition results in a URL change! It's important to note, the router is a 
resource-oriented engine, we **cannot place more than one component into the same location at the same time** (~Auxiliary Routes!). 
This means, if building a router SPA, we should drive with UX-Driven Design to determine the appropriate data models for the Web API interface. 
The UI project should comply with User-Centered Design (UCD), where user actions define the URL workflow.

![](src/assets/images/Router.png)

In Addition to this, we must ensure that routes are provided by the Web API layer. For example, don't use routes 
like /products/:id/edit?filter='mam', if the Web API layer doesn't support query params. Always validate if API routes will be available 
through the Web API! 

## UI state

Build a UI service anytime a component needs to stash away some property values or for communication with itself or others (action stream). 
It offers a simple set of properties to share state. This pattern is good for retaining view state or draft state.

```
@Injectable 
export class UiService{
    public showPicture: boolean;
    public filterBy: string; 
}
```

**» Keep the state in sync**<br/>

Angular's change detection provides notification of any changes to state values by `getter` accessor methods, if the values are bound in the template. 
This way, we keep the state in sync. Observables, Subjects or BehaviorSubjects can help to simplify asynchronous data-handling. 
When sharing data that should always be in sync, reactive extensions are good solutions to this situation.
       
## Application-, Domain- and Infrastrucutre services

One downside of sharing and binding state through services is that they are coupled to the view. Delayed changes to the state must be managed 
by asynchronous binding techniques to **keep the shared state in sync**. However, with EventEmitters, Subjects or BehaviorSubjects we share data 
through notifications. We subscribe and react to changes using notification services. Those notifications are more than just changes to bound values. 

**» Application service**<br/>

@TODO [text]
@TODO [image]

**» Domain service**<br/>

@TODO [text]
@TODO [image]

**» Infrastrucutre service**<br/>

Let's have a look at how to build a notification service based on a Subject:

```
@Injectable()
export class NotificationService {
    private _subject = new Subject<any>();
 
    public notify(news: string): void {
        this._subject.next({ news: news });
    }
 
    public listen(): Observable<any> {
        return this._subject.asObservable();
    }

    public complete(): void {
        return this._subject.complete();
    }
}
```

# Component Tree Design

When developing a single page application based on the router module, we should primarily think of the component hierarchy and sketch 
wireframes alongside the component tree. This way, we can easily approach an UX-Driven API design. The top-down flow ensures that the GUI 
storyboard is compatible with the resource representation enforced by RESTful practices. By mapping a GUI storyboard to the component tree 
we are able to identify full business use cases. The following phase model will be used as a basis:

`Information Architecture` &rarr; `Interaction Design` &rarr; `Visual Design` &rarr; `Usability Testing`

![](src/assets/images/component_tree.png)

Almost conform to REST and HATEOAS, we notice a clear navigation path which makes it considerable to map wireframes to the component tree. 
It is obvious that this approach does not comply with a DDD task-based UI projection because the router configuration is tide coupled to HATEOAS.
Moreover, while with task-based UI components we expect appropriate view models, the HATEOAS approach provides us with CRUD-based resource models. 
Very often service providers create RESTful Web APIs, where clients have to stitch data together by themselves. In order to satisfy UX requirements 
it is not feasible to prepare read models for every client's use case! In this context a HATEOAS approach is excellent for mobile devices and CRUD-based applications, 
but can be crucial to smart desktop applications. For more information about REST and data aggregation visit the following website: https://phauer.com/2015/restful-api-design-best-practices/ 

## Navigation patterns

As layout complexity increases with screen resolution, it requires careful considerations when starting from a mobile-first approach 
and scaling up to desktop layouts. Traditional desktop layouts require more complex interaction and navigation patterns because UX engineers 
normally address usability problems at first place and do not take into account any RESTful practices. As mentioned before, the 
router engine is a resource-oriented engine (HATEOAS) with which we have limited possibilities regarding arbitrary navigation patterns. 
The most commonly used navigation patterns are: 

![](src/assets/images/Master2Details.png)

With the master-master and master-details patterns we comply with RESTful resource association and resource aggregation 
with reference to one and only one component. Indeed secondary (Auxiliary) and pathless (Master-Children) routes allows us 
to initiate several components in parallel, but bringing limitations and sacrifices to a special syntax that does not comply 
with RESTful practices. 

![](src/assets/images/Master2Aux.png)

Pathless or componentless routes are a good way to share data between sibling components. This kind of routes provide a way
to load several components at a time. However, deep-linking is not supported how it should be. It exists a hack to enable 
deep-linking to some extend. This is achieved by checking route params in named router outlets or by intervening with
Resolvers or Route Guards. If specific invariants evaluate to true, we will display the component:

`<router-outlet *ngIf="id==='22'" name='employee'></router-outlet>` 

The pathless strategy is not well documented, especially when it comes to deep-linking it leads to unexpected behavior.
Secondary (Auxiliary) routes should be addressed in any use case that requires a few components to be initiated in 
parallel at random places. The router module is therefore well suited for mobile related navigation patterns. 

![](src/assets/images/Notebook.png)

# Summary

With multi-layered applications it is clear that critical decisions have to be made regarding technical aspects at the micro-level. 
Most of them are determined by the requirements at the macro-level, which includes decisions on the scope of:

- SPA vs. MPA
- UX vs. Domain-driven vs. Data-driven vs. API First
- Smart vs. Dump client
- Public vs. Private Web API
- Mobile vs. Desktop first
- Offline vs. Online first
- Functional vs. Object-oriented 

Bibliography:

- Yakov Fain; Angular with TypeScript - 2nd; Manning Publications; Mart 21, 2019. 
- Victor Savkin; Angular Router; Packt Publishing; Mart 20, 2017. 
- Cornelia Davis; Cloud Native Patterns; Manning Publications; May 22, 2019.

- Deborah Kurata; Pluralsight; Jan 30, 2018; Accessed September 12, 2018. <br/>
https://www.pluralsight.com/courses/angular-component-communication.
- Deborah Kurata; Pluralsight; Aug 20, 2019; Accessed August 26, 2019. <br/>
https://www.pluralsight.com/courses/angular-routing
- Thomas Rundle; medium.com; Nov 4, 2016; Accessed August 12, 2018. <br/>
https://medium.com/@ct7/angular2-module-architecture-and-example-seed-project-35b7410264f5
