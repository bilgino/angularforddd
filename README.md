# Introduction (WORK IN PROGRESS)
An introduction for building large-scale enterprise applications with Angular.

# Application architecture 

Obeying the principles of Object-Oriented Design, Domain-Driven Design and Command-Query-Responsibility-Segregation in the frontend design system, 
we break down complex requirements into logical boundaries and divide business logic into layers with different responsibilities to keep our code in 
good condition.

# Frontend coupled to OOD, DDD and CQRS

The building blocks of Angular already encompasses code organisation strategies. Nevertheless, to gain a better design we will bypass the 
traditional data-centric approach and consider building blocks of Domain-Driven Design for frontend architectures:

![](src/assets/images/frontend_arch.png)

## Object-Oriented Design

Although functional programming has gained a strong foothold in frontend development in recent years, a consistent object-oriented approach might better 
for TypeScript projects. Object-oriented programming allows us to approach a more human-readable code base, where the Ubiquitous Language can help to 
build better semantic and complex data types. Angular embraces both programming paradigms, functional and object-oriented programming.

**» Applying SOLID principles**<br/>

In object orientation the SOLID principles can help to make better design decisions (high cohesion and low coupling). Applying the Dependency
Inversion Principle, we ensure that layers depend on abstraction as opposed to depending on concretion (Programming to an Interface).
This means that high-level modules (the application layer) shouldn't depend on low-level modules (the domain layer).

**» Applying cross-cutting concerns**<br/>

Frontend applications also include cross-cutting concerns such as logging, caching or security. A naive approach to implement cross-cutting
functionality usually leads to duplicated or coupled code, which violates Don't Repeat Yourself and the Single Responsibility Principle.
The Aspect Oriented Programming promotes an abstraction and encapsulation of cross-cutting concerns by interlacing additional code,
resulting in loose coupling between the actual logic and the infrastructure logic. 

## Applying Domain-Driven Design to Angular 

An important concept of Domain-Driven Design is that the domain model is kept isolated from other concerns of the application. Ideally, the
domain layer is self-contained and focuses on abstracting the business domain. Very often frontend applications validate business rules that
are immediately reflected to the UI layer, especially in SPA applications when navigating through HTML forms that have cross-dependencies 
in terms of distributed business rules. As an example, we don't display the "place order" button, if the basket is empty. Another example would be 
offline applications (PWAs) where a big part of the business logic must be replicated to the client side! An isolated domain layer allows us to 
avoid domain logic leaking into other layers or surrounding services. In addition, we don't want to command against the server upon every user input. 
Therefore, considering the domain layer pattern in the frontend architecture sounds like a very good idea.

Domain-oriented layering is often considered the first structuring criterion in Angular applications. For many applications however, it's sufficient to
stick with horizontal slicing, since vertical slicing isn't mandatory. The main reasons for modular segmentation in Angular applications are lazy-loading,
scoping and distribution.

When application services carry out business use cases, it may be a good idea to keep business use cases that contain less logic in the UI
controller, like in the classic MVC pattern. However, we don't want to hide business use cases from the rest of the application and instead use special-purpose
classes! In addition, we want to share state and logic of these classes with other independent classes.

It's fairly debatable whether higher granularity distributed across several layers introduce extra complexity in frontend applications. As a consequence, 
many developers tend to lean toward weaker patterns because they see it as an unnecessary practice. Often a simpler data-centric approach is sufficient. 
For most web applications MVC or Flux/Redux may be more appropriate. Before starting using advanced concepts we must validate incoming requirements.

Considering multilayered architectures, the question arises of how to organize layers in SPA applications? This question refers to code splitting,
communication through layers and demanding business logic from services. The multilayered architecture in Domain-Driven Design embraces following layers:

## Layered Architecture

**» Horizontal slicing**<br/> Slicing the application into layers...

![](src/assets/images/layers_hc.png)

**» Vertical slicing**<br/> Slicing the application into features / use cases / bounded context...

![](src/assets/images/layers_vc.png)

**» Cross slicing**<br/> Slicing the application into modules...

![](src/assets/images/layers_cc.png)

*» Abstraction layers*<br/>

- Presentation layer: Components, Widgets, User Interface Services
- Application layer: Use Case Services, View Models and Providers, Factories <br/>
- Domain layer: Aggregates, Entities, Value Objects, Factories, Domain Services <br/>
- Infrastructure layer: Repositories <br/>

*» Service layers* <br/>

- User Interface services carry out dialog or interaction concepts
- Application services carry out business use cases and are procedural 
- Domain services carry out business concepts or processes that don't fit inside entities or value objects
- Infrastructure services help to separate technical concepts from business concepts <br/>

*» Validation layers*<br/>

- Application layer: Data types (null, undefined), format (length, empty, whitespace), schema (email, creditcard)
- Domain layer: Business Rules, Business Invariants<br/>

*» Angular adoption*<br/>

- Presentation layer: Views (Templates), Directives, Pipes, Animations
- Application layer: Components, Services, Guards, Validator Functions, Forms, Factories <br/>
- Domain layer: Classes, Interfaces, Factories, Services <br/>
- Infrastructure layer: Resolvers, Interceptors<br/>

Examples:<br/>

Presentation layer: *ModalDialogService, BreakpointObserver, LoadingSpinner, NavController*<br/>
Application layer: *Authentication, Search*<br/>
Domain layer: *Order, Payment, Customer, Shipment, Product, Address, Inventory*<br/>
Infrastructure layer: *Persistence, Caching, Messaging, Crypto, Converter, Validation, Translation*
*Logging, Error, Security, Configuration, Token, Monitoring, Date*

# Angular core patterns

Angular's core patterns such as modules, services, factories etc. affords us to comply with domain-driven principles.

## Modules

The angular.io styleguide states categories for organizing blocks of code: **Shared Modules** and **Widget Modules** contain the 
most commonly used code, while **Domain Modules** encapsulate blocks of code, that is not intended to be used outside that module, 
makes **Domain Modules** a good candidate for the bounded context pattern. The **Service Module** shares its content application 
wide as singletons. The **Root Module** includes several domain modules. That is, the entry point is the root module. For a more 
complete overview, visit the following website https://angular.io/guide/module-types#summary-of-ngmodule-categories

Angular's module system gives a clean design response:  

**» Modular Architecture**<br/>

![](src/assets/images/module_arch.png)

**» Examples**<br/>

`Service Module`: Application wide services as singletons e.g. *AuthenticationService, LoggerService*<br/>
`Shared Module`: Highly reusable components as non-singletons e.g. *DropDownComponent, PaginatorComponent*<br/>
`Domain Module`: Domain modules such as *OrderModule* (Bounded Context) or *SalesModule* (Bounded Context)<br/>
`Widget Module`: Highly cohesive and reusable widgets e.g. *MatSidenavModule, MatSnackBarModule* <br/>

**» Modules Checklist**<br/>

Following checklist can help to facilitate the orchestration of ngModules:<br/>

- Every component, directive and pipe must belong to **one** and **only one** module
- **Never** re-declare these elements in another module
- **Do not** share contents of a domain module, instead add reusable elements to a shared module
- **Do not** use the providers array of a module to register global services (use provideIn)
- Module content is private by default and only visible to its own content
- Transitive dependencies aren't visible, reexport them to make them available to other modules
- Module content can be exported without being imported

**» Bounded Context Pattern**<br/>

The bounded context pattern divides the domain model into related domain fragments. In a service-based environment a bounded context marks the 
boundaries of an application service. An application service is a concretion of the bounded context pattern! This is similar to **Domain Modules** where we mark the boundaries based on features. 
Applying the bounded context pattern to domain modules allows us to structure Angular modules in a domain-driven approach. A bounded context should consist of at least one aggregate and may consist of 
several aggregates. 

An important consideration when modeling a bounded context on the server-side is that it doesn't fully comply with RESTful practices. 
Since a bounded context represents one or more aggregates, it's sufficient to couple the bounded context to the root URL (root-resource):
`/BoundedContextA/*API`; `/BoundedContextB/*API`. We still can use sub resource URLs like `/order/{id}/items/{id}` in the router 
configuration to allow "In-App-Navigation" as the UI layer is agnostic of the layers underneath. **A bounded context can be assigned either 
to an entire page or to page segments.**

Interrelationship between the bounded context pattern and Angular domain modules:

![](src/assets/images/BoundedContext.png)

The interrelationship between Angular, REST and DDD aggregates requires more labor which we will discuss shortly.

**» Scaffolding**<br/>

A common practice in Angular projects is to structure the code base into `/core`, `/shared`, `/features` folders according
to the angular.io styleguide: https://angular.io/guide/styleguide#overall-structural-guidelines. 
Unfortunately, this approach isn't sufficient for complex projects and is mainly inspired by technical constraints. 
When setting up a folder structure, a domain-driven approach is better suited for complex applications.

Domain-driven scaffolding:

![](src/assets/images/scaffolding.png)

## Models 

The model in the classic MVC pattern is a representation of application data. The model contains code to create, read, update and delete or 
transform model data. It stores the domain knowledge and is similar to the repository pattern! 

Angular promotes two types of models:

- `View Model`: This object represents data required by a view. It doesn't represent a real world object
- `Domain Model`: This object represents data and logic related to the business domain

The view model and domain model should maintain different data structures to keep the domain model agnostic of view properties

**» Implementation Patterns**<br/>

- Anemic Domain Model
- Rich Domain Model
- View Model 

The anemic domain model is quite often used in CRUD-based web applications as value container without any behavior of its own. However, it's considered an anti-pattern 
because it doesn't encompass business logic and can't protect its invariants. Furthermore, it introduces a tight coupling with the client. Using rich domain models instead, 
we prevent domain logic from leaking into other layers or surrounding services. The following example shows the negative side effect of anemic domain models. 

Domain logic is coupled to the client (UI controller): 

**»  Effects of anemic domain models** <br/> 
```
class Employee {
    public name: 'John Connor';
    public salary: 1000;
}

@Component({
    selector: 'emp',
    templateUrl: './emp.component.html'
}) class EmployeeComponent {
    @Input() emp: Employee; 
    
    public salaryIncreaseBy(percent: number): void {
         emp.salary = (emp.salary * percent / 100) + emp.salary;
    }
}
```

In this example, domain logic tends to be duplicated in distant components and may go out of sync and leads to data inconsistency.
A rich domain model protects and encapsulates domain logic to ensure data consistency:

**»  Effects of rich domain models**<br/>
```
class Employee {
    private name: 'John Connor';
    private salary: 1000;
    
    public salaryIncreaseBy(percent: number): void{
        if(percent > 100) throw new Error(...);
        this.salary = (salary * percent / 100) + salary;
    }
}

@Component({
    selector: 'emp',
    templateUrl: './emp.component.html'
}) class EmployeeComponent {
    @Input() emp: Employee; 

    public salaryIncreaseBy(percent:number): void {
         emp.salaryIncreaseBy(percent);
    }
}
```

In the second example, domain logic is decoupled from the UI controller. Encapsulation protects the integrity of the model data.
Keeping the model as independent as possible improves reusability and allows easier refactoring.
Neither domain state nor domain logic should be written as part of the UI controller.

Consequently, using feature services for structural and behavioral modeling while domain models remain pure value containers is another 
common bad practice in Angular projects and known as "Fat Service, Skinny Model" pattern: 

```
@Injectable()
class AccountService {
    private accounts = [{ id: 1, balance: 4500 }, { id: 2, balance: 2340 }];
    
    constructor(){}
    
    public changeBalance(id: number, amount: number): void {
        if (id > 0 && amount < AMOUNT.MAX_VALID) {
            this.accounts[id].balance += amount;
        }
        return this.accounts[id];
    }
    
    public deposit(){}
    public widthDraw(){}
}
```

We should strive to push domain logic into entities making boundaries become more clear:

```
@Injectable()
class AccountService {

    constructor(private accountRepository: AccountRepositoryService){}  
    
    public changeBalance(id: number, amount: number): Observable<Account> {
        return this.accountRepository.getById(id).pipe(
          map((account: Account) => {
            account.updateBalance(amount);
          })
        );
    }
    
    public deposit(){}
    public widthDraw(){}
}

class Account {
    private id: number;
    private balance: number;
    
    constructor(){}
    
    public updateBalance(amount: number): void {
        if (amount > AMOUNT.MAX_VALID) {
           throw Error(...)
        }
        this.balance += amount;
    }
    
    public deposit(){}
    public widthDraw(){}
}

@Injectable()
class AccountRepositoryService {
    private accounts = [new Account(1, 4500), new Account(2, 2340)];
    
    constructor(){}
    
    public getById(id: number): Observable<Account> {
        if (id <= 0) {
            throw Error(...);
        }
        return accounts.find(...);
    }
}
```

In the preceding example, we have seperated one business service into three layers with different responsibilities. Later, we will discuss the service layer 
pattern in the form of application-, domain- and infrastructure services conform to Domain-Driven Design practices. 

Put simply, using rich domain models means more entities than services. Building rich domain models is a major objective in object-oriented design.

**» Domain Model (DDD Aggregate Pattern)**<br/>

The domain model entities contain data and behavior modeled around the business domain.
In terms of DDD and CQRS, the domain model entity is an aggregate that contains only write operations that result in state transitions.

Example of a Domain Model Entity (CQS):

```
class Order {
    private quantity;
    private totalCost;
    private custId;
    private deliveryAddress;

    contructor(){}
    
    public placeOrder(){}
    public getOrderItems(){}
    public totalQuantity(){}
    public changeDeliveryAddress(){}
    public getCustomerId(){}
}
```

In traditional object-oriented development the software model lacked of explicit boundaries. Relationships between classes brought a
complexity that required an efficient design. The DDD aggregate pattern takes a contextual approach by embracing groupings of entities and value objects 
that are modeled around business rules and clear boundaries inside the software model making the system easier to reason about.
One of the most important aspects of the aggregate pattern is to protect it from being invalid and having an inconsistent state. 

**» Aggregate entity checklist:**

- It's a top-level core business object
- It's bounded from the viewpoint of a business use cases
- It's based on a root entity and typically acts as a cluster of related domain entities and value objects
- It's global identity, state, lifecycle and receives the name of the bounded context
- It's modeled around protecting business invariants, encapsulation and data integrity
- It validates all incoming actions and ensures that modifications don't contradict business invariants
- All possible business invariants must be satisfied for each state change, when one part is updated, other parts might too
- The internal state can only be mutated by the public interface of the root aggregate 
- Objects from outside can't make changes to inside objects, they can only change the root object
- Each use case should have only one aggregate but can use other aggregates to retrieve information
- Multiple aggregates can reuse a value object
- Each aggregate root gets its own repository 

The aggregate spans objects relevant to the use case and its business invariants:

![](src/assets/images/Aggregate_BR.PNG)

**» From the viewpoint of frontend development:**

- Aggregates are immutable per default 
- Aggregates don't publish domain events and won't be out‐of‐sync due to data reactivity using RxJS
- Inter-Aggregate references established by global IDs (primary keys) rather than by object declarations is optional
- Since the web browser is a monolithic environment with a homogenous stack, aggregates can be reused anywhere
- Aggregates build the foundation for view models, if the backend architecture isn't in adherence with CQRS.

**» Routing, REST and DDD Aggregates**<br/>

Since the navigation principle of the Angular router engine adheres to the navigational behavior of hypermedia systems (HATEOAS) where URIs identify linked resources conform to RESTful 
practices, we should reexamine the idea of building client-side aggregates. Since an aggregate builds a group of related domain entities and value objects, wouldn't we then have to group 
linked resources instead?

In the classic data-centric approach, database tables and their relations are used as a foundation for the resource model. But is this common and always true? 
Well, it all depends on the use case and how we interpret a REST resource! A REST resource may be a representation of a single entity, database table or a materialized view. 
But how do we map a REST URL like `/addresses/22` etc. to a client-side aggregate such as `/orders/4` or `/customers/54`?

If we consume a fine-grained REST API, we would have to stitch together all resources to create an aggregate for each initial routing event. Consequently, an application or 
repository service provides the public interface to handle all queries to the aggregate. In this scenario, the repository service acts as an anti-corruption layer to the underlying 
resource model. Unfortunately, this approach might not work well, since the creation of an aggregate on the client-side could result in countless additional HTTP requests 
(N + 1 Problem)! Hence, the aggregate entity as a conceptual whole should be negotiated with the REST API!

Even in the case of server-side aggregates, something doesn't feel right here! First, we shouldn't provide a pure domain entity to the client side. Secondly, if 
the requested aggregate e.g. order aggregate (`GET: /orders/22`) already contains related data like the delivery address, then how do we update the delivery address of an order? 
Either we invoke a business method of the order aggregate like `Order.updateDeliveryAddress(newAddress)` and consequently process an HTTP update command: `PUT: /orders/22 : {order:{ deliveryAddress... }}` 
or we can use a dedicated REST endpoint for updating the order delivery address: `PUT: orders/22/addresses/5 : {deliveryAddress:{}}`. The server-side request handler would now fetch the order 
aggregate entity using its ID property from the repository and process an update like `Order.updateDeliveryAddress(newAddress)`. The second approach seems to contradict the basic 
idea of an aggregate to avoid revealing its internal state to ensure transactional consistency boundaries!? 

We shouldn't strive to use REST URLs like `/addresses/5`, since the address resource is without any context! As an example, calling `DELETE: /addresses/5 : {address:{id:5}}` may delete the address 
of an ongoing order process! But now here's a question: can an address exists outside an order or customer context? 

Navigating a resource model and its relationships or adhering to use case(s) related aggregates can have a big impact to the frontend architecture!
For more information about the drawbacks of REST please visit the following website: https://www.howtographql.com/basics/1-graphql-is-the-better-rest/

<br/>

![](src/assets/images/Aggregate_ACL.PNG)

**» Further considerations when building aggregates:**

**Q**: Is building aggregates a poor choice, if requests for aggregates are rare?<br>
**A**: It all depends on the complexity of the application, particularly of the GUI layer!

**Q**: Are network cost between the client and the server critical when retrieving large amounts of data?<br>
**A**: The trend is toward higher bandwidth, HTTP/2 and faster computer systems. Retrieving large amounts of data is quite normal nowadays!
Instead, focusing on reducing round-trips is still valid.

**Q**: How can a GUI designer help to reduce the requests for aggregates?<br>
**A**: By defining GUI patterns that don't require compositions and comply with the navigational behaviour of hypermedia APIs!

**» View Model**<br/>

View models are mere data objects and usually don't contain any domain behavior. Hence, they are not a part of the domain layer.
View models are supportive in providing data to the view and might extend super view model classes to inherit common properties or behaviour.
They are typically created by merging two or more existing models into one model and are an essential part of every good frontend architecture.

The view model should hold the data necessary to render the UI if:

- View demands a subset of one or more domain model properties
- View demands additional properties mixed up with UI related properties

View model checklist:

- Should have an ID property
- Should be immutable and has properties of type `readonly string`
- Behaves like a value object, also called a data transfer object
- Might or might not have dependencies
- Should be located in its own file, repository service or UI container component
- The name ends with the suffix -View e.g. UserProfileView, UserListView, UserDetailsView
- The name of the view model is equivalent to the name of the UI Component e.g. UserListComponent

Examples:

```
class OrderViewModel {
    private _orderId: string;
    private _customerId: string;
    private _total: string;
    private _balance: string;
   
    get total(){}
    set total(data) { this._total = this.format(data) }
    get balance(){}
    set balance(data) { this._balance = this.calc(data) }
    
    constructor(){}
    
    private format(){}
    private calc(){}
}

const newOrderViewModel = new OrderViewModel()
newOrderViewModel.total = 444;
newOrderViewModel.balance = -44;
```

Necessary data transformations can reside in the same view model class. A better approach would be to use a dedicated component such as a mapper, translator,
factory or an abstract super class that performs all UI-related transformations. In this way, we can decouple the transformation responsibilities to promote
code reusability by subclassing.

```
abstract class ViewModel {

    constructor(){}
    
    protected format(){}
    protected calc(){}
}

class OrderViewModel extends ViewModel {
    private _orderId: string;
    private _customerId: string;
    private _total: string;
    private _balance: string;
     
    get total(){}
    set total(data){ this._total = this.format(data) }
    get balance(){}
    set balance(data){ this._balance = this.calc(data) }
    
    constructor(){
      this.super();
    }
}

const newOrderViewModel = new OrderViewModel()
newOrderViewModel.total = 444;
newOrderViewModel.balance = -44;
```

Due to performance implications, it's not recommendable to bind `getters` in the template. Instead, we use public properties (primitive types).

Hardcoding transformation methods in the view model class causes tight coupling. A better approach is to process data transformations such as filtering, sorting, grouping or destructuring etc.
in reactive streams pipes and hand over the result to an object factory.

**» Object Factory Pattern:**<br/>

Objects can be constructed using regular constructors or using static factories. The object factory pattern helps to create complex objects like aggregates that involve the 
creation of other related objects and more importantly assists in type safety with JavaScript objects and ES6+ features such as spread, rest, destructuring and 
merging.

Option 1:

```
class OrderFactory {
    public static create() {
      return new Order();
    }
}

const newOrder = OrderFactory.create();
newOrder.propertyA = ...
newOrder.propertyB = ...
```

Option 2:

```
interface IOrder{
    status: OrderStatus;
}

class Order implements IOrder {
    public status: OrderStatus;
    
    private constructor(props : IOrder){
        this.status = props.status;
    }
    
    public static create(props: IOrder): Order {
        if(!props) return new Order();
        return new Order(props);
    }
    
    public static empty(): Order {
      return new Order();
    }
    
    public toJSON(): object {
        const serialized = Object.assign(this);
        delete serialized.status;
        return serialized;
    }
    
    public toString(): string {
        return "";
    }
}

const newOrder = Order.create({status:OrderStatus.Pending});
const jsonOrder = newOrder.toJSON();
```

Option 3:

```
abstract class ViewModel {

  constructor() {}
  
  protected transformPrice(price: string): string {
    return // Do somthing with price value
  }
}
 
interface IProductViewModel = {
  id: number;
  name: string;
  price: string;
  type: string;
  active: boolean;
};

class ProductViewModel extends ViewModel {
  public id!: number;
  public name!: string;
  public price!: string;
  public type!: string;
  public active!: boolean;

  private constructor(props: IProductViewModel) {
    super();
    this.price = this.transformPrice(props.price); 
    ... // set or transform other properties
  }
  
  private static canNotCreate(props: IProductView): boolean {
    // validate props and return error
  }
  
  public static create(props: IProductViewModel): Readonly<ProductViewModel> {
    if (this.canNotCreate(props)) {
        throw Error("Can not create ProductViewModel");
    }
    return new ProductViewModel(props) as Readonly<ProductViewModel>;
  }
}

cosnt productViewModel = ProductViewModel.create({
  id: 1,
  name: 'screw',
  price: '28$',
  type: 'pans',
  active: false
});
```

**» Model Declaration Strategies**<br/>

- Type Signature Pattern

```
type Order = {
     orderId: string;
     status: Orderstatus;
     customer: Customer;
};

// or

interface Order {
     orderId: string;
     status: Orderstatus;
     customer: Customer;
};

const newOrder: Order = {
     orderId = '1';
     status = Orderstatus.Pending;
     customer = {};
}
```

- Object Constructor Pattern

```
class Order {
    public status: OrderStatus;
    public customer: Customer;
    
    constructor(){}
    
    public placeOrder(){}
}

const newOrder: Order = new Order();
```

**» Mapper Pattern**<br/>

A domain layer in the frontend architecture ensures that business behavior works. 
With higher functional ability using rich domain models, we must take the mapper pattern into consideration. 
A common practice for preserving type safety is to declare interfaces in support of plain JavaScript object literals. 
In the context of "mapping", it's important to make a clear distinction between the typing system and the data structure of models.

Mapping JSON-encoded server data in the frontend is mandatory if:

- The domain model object defines any methods
- The schema of the Web-API is different from its representation in the application
- The typing system shall consist of classes instead of interfaces or type aliases

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

The data mapper pattern is used in the repository service to assemble the appropriate model schema.

**» Structural Mapper Pattern**<br/>

The structural mapper performs a bidirectional transfer of data structures between two objects to ensure type safety with 
class instances instead of JSON object declarations and interfaces used in arrays. In addition, we should always ensure the appropriate 
data schema by using interfaces as contracts between the target and the source object. 

Option 1 - Constructor Assignment:

```
interface IOrder {
    id: number;
    status: Status;
    total: number;
}

class Order {
    public id; 
    public status; 
    public total;
    
    constructor(data: Partial<IOrder>) {
       this.id = data.id;
       this.status = data.status;
       this.total = data.total
    }
}

const orders = [new Order({id=22,status:Status.Pending,total:450})];
```

Option 2 - Constructor Assignment with ES6+:

```
class Order {
    public id; 
    public status; 
    public total;
    
    constructor({ id, status, total = 0 }: Partial<IOrder>) {
        Object.defineProperty(this, 'id', { value: id, writable: false });
        Object.assign(this, { status, total });
    }
}

class Order {
    public id; 
    public status; 
    public total;
    
    constructor({ id, status, total }: Partial<IOrder>) {
        Object.defineProperties(this, {
            id: { value: id, writable: false },
            status: { value: status },
            total: { value: total },
        });
    }
}

const orders = [new Order({id=22,status:Status.Pending})];
```

Option 3 - Constructor Assignment with Index Signature:

```
enum Status {
    PENDING = "Pending",
    COMPLETE = "Complete"
}

class Order {
    [key: string]: any;
    
    constructor(input: { [key: string]: any }) {
        Object.keys(input).forEach((k: string) => {
            this[k] = input[k];
        });
    }
}

const orders = [new Order({id:33,status:Status.PENDING})];
```

Use an abstract super class to decouple the constructor assignment:

```
enum Status {
    PENDING = "Pending",
    COMPLETE = "Complete"
}

abstract class IOrder {
    [key: string]: any;

    constructor(input: { [key: string]: any }) {
        Object.keys(input).forEach((k: string) => {
            this[k] = input[k];
        });
    }
}

class Order extends IOrder {}

const orders = [new Order({id:33,status:Status.PENDING})];
```
Unfortunately, index signature assignments don't support access modifier (public, private, protected).

Option 4 - Factory Mapper Assignment:

```
class OrderMapper {

    constructor() {}
    
    public static mapToOrder(Order, Dto): Order {
        Order.id = Dto.id;
        Order.status = Dto.Status;
        Order.total = Dto.total;
        return Order;
    }
    
    public static mapFromOrder(Order, Dto): Dto {
        Dto.id = Order.id;
        Dto.status = Order.Status;
        Dto.total = Order.total;
        return Dto;
    }
}

const orders = [OrderMapper.mapToOrder(new Order(), {id:33,status:Status.PENDING})];
```

Option 5 - Builder Pattern:

```
class Order {
    public id;
    public status;
    public total;
    public address: Address;
    public customer: Customer;
    
    constructor() {}
    
    public setAddress(newAddress: Address): this {
        this.address = new Address(newAddress);
        return this;
    }
    
    public setCustomer(newCustomer: Customer): this {
        this.customer = new Customer(newCustomer);
        return this.;
    }
}

const orders = [new Order().setAddress({...}).setCustomer({...})];
```

Mapper checklist:

- Bidirectional mapping is inevitable if using model-driven reactive forms
- The mapper pattern in the repository service assists to assemble a pure model
- Don't map view models in the repository service because view models may require multiple sources

**» REST, HATEOAS & Mapping.**<br/>

When building multi-layered, distributed web applications, data transformation is among the major challenges that occur when data traverses 
all layers (data flows up and down the stack). If the domain layer has been replicated to the client-side, we may need to transform the 
server response schema to a complex object graph (domain model): 

![](src/assets/images/Mapper_Response.png)

For example, HATEOAS embraces hyperlinks between external resources to make transitions through the application state by navigating links. 
However, mapping links to a client-side domain model isn't possible! In addition, when consuming REST APIs very often multiple HTTP request 
need to be performed asynchronously to create a model for a specific use case in the UI layer. If the applied HATEOAS pattern
forms links in the response it would limit and enforce the UI layer to incorporate with REST APIs synchronously. 
**UX designers usually don't model their interaction-, navigation- or screen patterns around hypermedia systems**. Furthermore, the Angular router engine doesn't 
comply well with the URI templates of hypermedia formats. HATEOAS may decouple the backend from the frontend, but couples the frontend to the backend.
Although the Angular router engine complies with the navigational behaviour of hypermedia systems, you should avoid HATEOAS for Angular SPA applications!

## Services

Singleton services are important constructs in Angular applications. Most of the functionality that doesn't belong to UI components typically
resides in the service layer. Later, we will discuss the service layer pattern in the form of application-, domain- and infrastructure services conform
to Domain-Driven Design practices.

**» Stateful Services vs. Stateful Repositories**<br/>

Just as mentioned before, it's common for Angular projects to use services for business functionality and state management. 
We typically use stateful services if we need to share data across components or process HTTP requests and responses that perform CRUD operations. 
In order to comply with Domain-Driven Design, we will use a reactive repository services in favor of an active data store to save the domain state and UI state.
The repository service acts as a reactive storage place for globally accessible data which can be used by other independent components. 
Repositories aren't only for entities, but for all objects including anemic domain models or view models. Repositories often act as an 
anti-corruption layer allowing us to assemble data models without their structure being influenced by the underlying Web-API.

Furthermore, we will introduce the CQRS pattern to stem the heavy-lift when building complicated page flows and user interfaces. 
The CQRS pattern enables us to answer different use cases with the respective data model. State transitions in the repository will immediately
replicate back to the view model (read side), that is, reactive projection through observables. A projection can be leveraged in several ways or layers. 
The most common approach is an event-based projection causing an eventually consistent system. However, we will not encounter any problems of this kind,
due to the reactive design of Angular (RxJS).

A reactive API exposes hot observables (BehaviorSubjects etc.) to manage the complexity of asynchronous data handling. If we share data with 
other components, we must keep track of state transitions to prevent stale data and keep the UI in sync. RxJS gives us many great tools and 
operators to implement the "projection phase" between the read and write side, which we will discuss shortly as "Projection Patterns".

**» CQRS in the frontend?**<br/>

With traditional CRUD-based web applications conform to the REST architectural style and the single data model approach,
we may fall into the situation where we have to stitch together several resources to build a rich (view) model.
Even in the case of RPC-style Web APIs, it's likely that we will encounter problems of this kind. Developers often use UI controller methods 
to assemble view models which in the end leads to monolithic UI controllers:

![](src/assets/images/Up_Down_Flow.png)

The domain model focuses on business logic rather than presentation needs. Introducing a view model provider services to manage 
complicated page flows and user interfaces allows us to query the appropriate view model for different UI flows. A view model provider service is a perfect 
fit to pre-compute filtering and sorting logic (https://angular.io/guide/styleguide#style-04-13). That is, the CQRS pattern helps to avoid over-bloated all-in-one models. 
The CQRS pattern may overcomplicate the system design, instead of simplifying it. Use it with care! 

CQRS in the frontend design system has many advantages:

- Separating concerns of each data model
- Unidirectional data flow
- Composing several API endpoints
- Immutable view models comply with the `.onPush` strategy
- sort() and filter() pipes can be detached from templates (https://angular.io/guide/styleguide#do-not-add-filtering-and-sorting-logic-to-pipes)

A view model provider may appear in different forms. It may appear as a query method in an application service, as a resolver or as special-purpose class:

**» CQRS with API Segregation using Feature Services**<br/>

![](src/assets/images/Service_CQRS.png)

**» CQRS with DDD tactical patterns using Providers**<br/>

![](src/assets/images/Reactive_Flow.png)

Typically, application services provide query methods for retrieving view models of domain state (CQS). However, for 
complicated page flows and user interfaces it would be inefficient to assemble view models in a query method, 
due to the large amounts of additional dependencies. Instead, we can use view model provider services to facilitate access to view models 
in a more efficient way. Consequently, the application service, resolver service, UI Controller or any other component can use the view model 
provider service to retrieve presentation data. 

![](src/assets/images/QuerySideService.PNG)

This might seem more complex than just using a single feature service for business logic and state management. 
The level of abstraction is up to the developer and is dependent on the incoming requirements. 

Using a single feature service or repository service for reads and writes (CQS):

![](src/assets/images/SingleService_CQRS.png)

```
@Injectable()
class ProductsService { 

    private selectedProductIds: number[] = [1,4,9];
    private products: Product[] = [];
    private selectedProductIds$: Subject<number> = new BehaviorSubject<number>(selectedProductIds);
    private products$: Subject<Product[]> = new BehaviorSubject<Product[]>(products);

    private productListView: Observable<Readonly<ProductListView>[]> = combineLatest([
        this.products$,
        this.selectedProductIds$,
    ]).pipe(
        map(([products, selected]: [Product[], number[]]) => {
            return products.map((item: Product) => {
                return ProductListView.create({
                    ...item,
                    active: selected.includes(item.id),
                });
            });
        }),
        shareReplay(1)
    );

    private constructor(): void {
        this.loadProducts();
    }

    private loadProducts(): void {
        return this.http.get<Product[]>('/products').subscribe(products => this.products$.next(products));
    }
    
    // READ
    public getSelectedProductListView(): Observable<Readonly<ProductListView>[]> {
        return this.productListView.asObservable();
    }
    
    // ADD
    public addSelectedProduct(id:number): void {
        this.selectedProductIds = [...selectedProductIds, id];
        this.selectedProductIds$.next(this.selectedProductIds);
    }
    
    // CREATE
    public createProduct(): void {
        ...
    }
    
    // UPDATE
    public updateProduct(): void {
        ...
    }
    
    // DELETE
    public deleteProduct(): void {
        ...
    }
}
```

The single service approach makes it difficult to gather multiple sources and could lead to circular dependencies. 

**» CQRS with Commands and Queries using the Command Pattern**<br/>

@TODO [text]
@TODO [image]

**» View Model Provider Patterns**<br/>

With the "projection by entity" pattern state transitions will be reflected almost simultaneously. 

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
Using abstract classes, we can remove the factory methods:

```
abstract class OrderViewModel {
    abstract orderId: number;
    abstract quantity: number;

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
What if a view model requires several sources? We can create a special-purpose class in form of a view model provider service. 
The purpose of the view model provider service is to tailor view models for specific use cases. 

```
@Injectable()
class OrderViewProvider {
   
    constructor(
      private orderRepository: OrderRepositoryService,             
      private productRepository: ProductRepositoryService,
      private customerRepository: CustomerRepositoryService,                             
      private translateService: TranslationService              
      ){}

    public getOrdersByStatus(status:string): Observable<Array<OrderView>> {
        return this._orderRepository.getAll()
        .pipe(
          filter(status),
          map(()=>{
             return translateService();             
          })
          mergeMap(()=>{
             return of([new OrderView()...]);             
          })
        )
    }
    
    public getProductByOrderId(id:number): Observable<Array<ProductView>> {
        return combineLatest([this._orderRepository.getOrders(), this._productRepository.getProducts()])
        .pipe(
          filter(id),
          groupBy(),
          mergeMap([...]) => {
            return of([new ProductView()...]);           
          }),
          shareReplay(1)
        )        
    }
    ...
}
``` 

Requesting the view model provider service in the view component class:

```
@Component({
  selector: 'order',
  template: `...`,
  providers: [OrderViewProvider]
})
export class OrderComponent {

  constructor(private orderProvider: OrderViewProvider) {
    this.orderProvider.getOrdersByStatus('pending').subscribe(()=>{})
  }
}
``` 

View model objects may also be assembled with Angular resolver services!

## Application-, Domain-, Infrastructure- and UI Services

One downside of sharing and binding state through services is that they are coupled to the view. Delayed state transitions must be managed
through asynchronous techniques to **keep the shared state in sync**. However, with EventEmitters, Subjects or BehaviorSubjects we share data
through event notifications. We subscribe and react to state transitions using notification services. Those notifications are more than just changes to bound values.

**» Application Service**<br/>

@TODO [text]
@TODO [image]

**» Domain Service**<br/>

@TODO [text]
@TODO [image]

**» Infrastructure Service**<br/>

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

# State Management 

With Single Page Applications (SPA), we get the flexibility and cross-platform functionality of web applications as well as the 
client state management of native applications. Typically, SPA applications have more complex states than traditional server-side 
applications. In Angular applications, stateful services are first-class entities for sharing state across components. 

There are many different state types to deal with:

Type | Example                  | Location  | 
------------|--------------------------|-----------|
Domain State | Entity, Resource, Model  | Im-Memory, Browser API
User Interface State | Position, Panel, Zoom    | Im-Memory, Browser API
Addressable State | /?sort /?filter /?search | Browser History API
Draft State | Comments, Mails          | In-Memory, Browser Storage API
Session State | Tokens, Keys             | In-Memory, Browser Storage API, Cookies
Application State | Online/Offline           | Browser API
ERROR State | throw Error()            | Client / Server

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

By implementing a CQRS-oriented repository, we share state and communicate domain state transitions through reactive operators (RxJS BehaviorSubjects),
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

## Router State

Angular's router service allows us to manage domain and UI state. Put simply, the router state determines which components are visible on the screen, and 
it manages navigation through application state (HATEOAS). Any state transition results in a URL change! It's important to note, the router is a 
resource-oriented engine, we **cannot place more than one component into the same location at the same time** (~Auxiliary Routes!). 
This means, if building a router SPA, we should drive with UX-Driven Design to determine the appropriate data models for the Web API interface. 
The UI project should comply with User-Centered Design (UCD), where user actions define the URL workflow.

![](src/assets/images/Router.png)

In Addition to that, we must ensure that routes are provided by the Web API layer. For example, don't use routes 
like /products/:id/edit?filter='mam', if the Web API layer doesn't support query parameters. Always validate if API routes will be available 
through the Web API! 

## UI State

Build a UI service anytime a component needs to stash away some property values or for communication with itself or others (action stream). 
It offers a simple set of properties to share state. This pattern is good for retaining view state or draft state.

```
@Injectable 
export class UIService{
    public showPicture: boolean;
    public filterBy: string; 
}
```

**» Keep the state in sync**<br/>

Angular's built-in change detection allows us to synchronize state transitions through `getter` accessor methods. Observables, Subjects or 
BehaviorSubjects advocates to simplify asynchronous data processing. When sharing data between components that should always be in sync, reactive extensions 
are the best solutions to this challenge.

# Component Tree Design

When developing a single page application based on the router module, we should primarily think of the component hierarchy and sketch 
wireframes alongside the component tree. This way, we can easily approach an UX-Driven API design. The top-down flow ensures that the GUI 
storyboard is compatible with the resource representation enforced by RESTful practices. By mapping a GUI storyboard to the component tree 
we are able to identify full business use cases. The following phase model will be used as a basis:

`Information Architecture` &rarr; `Interaction Design` &rarr; `Visual Design` &rarr; `Usability Testing`

![](src/assets/images/component_tree.png)

Almost conform to REST and HATEOAS, we notice a clear navigation path which makes it considerable to map wireframes to the component tree. 
It is obvious that this approach doesn't comply with a DDD task-based UI projection because the router configuration is tide coupled to HATEOAS.
Moreover, while with task-based UI components we expect appropriate view models, the HATEOAS approach provides us with CRUD-based resource models. 
Very often service providers create RESTful Web APIs, where clients have to stitch data together by themselves. In order to satisfy UX requirements 
it is not feasible to prepare read models for every client's use case! In this context a HATEOAS approach is excellent for mobile devices and CRUD-based applications, 
but can be crucial to smart desktop applications. For more information about REST and data aggregation visit the following website: https://phauer.com/2015/restful-api-design-best-practices/ 

## Navigation Patterns

As layout complexity increases with screen resolution, it requires careful considerations when starting from a mobile-first approach 
and scaling up to desktop layouts. Traditional desktop layouts require more complex interaction and navigation patterns because UX engineers 
normally address usability problems at first place and do not take into account any RESTful practices. As mentioned before, the 
router engine is a resource-oriented engine (HATEOAS) with which we have limited possibilities regarding arbitrary navigation patterns. 
The most commonly used navigation patterns are: 

![](src/assets/images/Master2Details.png)

With the master-master and master-details patterns we comply with RESTful resource association and resource aggregation 
with reference to one and only one component. Indeed secondary (Auxiliary) and pathless (Master-Children) routes allows us 
to initiate several components in parallel, but bringing limitations and sacrifices to a special syntax that doesn't comply 
with RESTful practices. 

![](src/assets/images/Master2Aux.png)

Pathless or componentless routes are a good way to share data between sibling components. This kind of routes provide a way
to load several components at a time. However, deep-linking is not supported how it should be. It exists a hack to enable 
deep-linking to some extend. This is achieved by checking route params in named router outlets or by intervening with
Resolvers or Route Guards. If specific properties evaluate to true, we will display the component:

`<router-outlet *ngIf="id==='22'" name='employee'></router-outlet>` 

The pathless strategy is not well documented, especially when it comes to deep-linking it leads to unexpected behavior.
Secondary (Auxiliary) routes should be addressed in any use case that requires a few components to be initiated in 
parallel at random places. The router module is therefore well suited for mobile related navigation patterns. 

![](src/assets/images/Notebook.png)

## Customizing

When deciding on a design, customizing the look of default components can quickly become challenging.
Creating frontends that feature modular and reusable CSS without overriding existing CSS rules is an important part of
every frontend project. Usually every component in an Angular project appears in the default style and serves a singular purpose.
Design adjustments for reusable components to specific content areas can be implemented with the `:host()` and `:host-context()` selectors.

![](src/assets/images/Customizing.png)

Unfortunately the `:host-context()` CSS pseudo-class function lacks browser support. Luckily, Angular supports an emulated version
through the default settings of the view encapsulation mode.

# Business Rule Validation & ErrorHandling

@TODO [text]
@TODO [image]

# Summary

With multi-layered applications it is clear that critical decisions have to be made regarding technical aspects at the micro-level. 
Most of them are determined by the requirements at the macro-level, which includes decisions on the scope of:

- SPA vs. MPA
- UX vs. Domain-Driven vs. Data-Driven vs. API First
- Smart vs. Dump client
- Public vs. Private Web API
- Mobile vs. Desktop first
- Offline vs. Online first
- Functional vs. Object-Oriented Design
