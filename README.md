<img width="200px" alt="MVVM2" src="https://raw.githubusercontent.com/daemonyeen/mvvm2/master/images/mvvm2.svg">


MVVM² (Model-View-View Model squared) is an Angular architectural pattern to develop large applications in easy way. Based on MVVM, this pattern allows you to create large scale enterprise applications while maintaining consistency. Designed specifically for Angular, but can be used anywhere!

Here's some benefits:

* Partially uses Domain Driven Design concepts, business oriented out of the box.
* Separate the UI and business logic of the application, easier to read and maintain your codebase.
* Lightning fast and easy to write unit tests for your Angular components.
* You can migrate your application to MVVM² step by step.
* A small amount of boilerplate code.

## Core concepts of MVVM²

Since Angular is already can be considered MVVM framework (template = View, component class = View Model, services = Model), we decided to name MVVM² this way, because it adds MVVM pattern on top of Angular. Just like in regular MVVM, we have _View_, _View Model_ and _Model_, but this time it will mean different things.

### View

In MVVM² _View_ is a component itself. This means that HTML template, component class and styles represent one _View_ entity.

Imagine we want to create Todo app. Let's create some _Views_:

```typescript
// @types/todo.ts
export type Todo = Readonly<{
    id: string;
    name: string;
    isCompleted: boolean;
}>

// @views/todo/todo.component.ts
@Component({
    selector: 'todo',
    template: `<div [class.completed]="todo.isCompleted">{{todo.name}}</div>`
})
export class TodoComponent {
    @Input() todo: Todo;
}
```

```typescript
@Component()
export class TodoList {}
```

### Model

_Model_ — is a service, that is responsible to get and update data from external and internal sources, like:

* Services, that will perform API calls to get and update data from the server
* Services, that use `LocalStorage`, `SessionStorage` or Cookies to store the data
* Other internal services on top of services described above (e.g. to get information about user globally you may use `UserService`)

Let's create a _Model_:

```typescript
// @model/todo-model.ts
export interface TodoModel {
    getTodos(): Observable<Todo[]>;
    addTodo(): Observable<Todo>;
    completeTodo(): Observable<Todo>;
}

// @model/todo-model.service.ts
@Injectable()
export class TodoModelService implements TodoModel {
    constructor(private readonly http: HttpService) {} 

    getTodos(): Observable<Todo[]> {
        return this.http.get<Todo[]>('/todos');
    }

    addTodo(name: string): Observable<Todo> {
        return this.http.post('/todo', { name });
    }

    completeTodo(id: string): Observable<Todo> {
        return this.http.put('/todo/complete', { id });
    }
}
```