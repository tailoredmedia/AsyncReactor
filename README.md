# AsyncReactor Example

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://github.com/diamirio/AsyncReactor/assets/19715246/56eef378-e63e-4732-8710-040d3440afbb">
  <img alt="DIAMIR Logo" src="https://github.com/diamirio/AsyncReactor/assets/19715246/8424fef3-5aeb-4e15-af36-55f1f3fc37b0">
</picture>

![iOS](https://img.shields.io/badge/iOS-000000?style=for-the-badge&logo=ios&logoColor=white)
![Swift](https://img.shields.io/badge/Swift-FA7343?style=for-the-badge&logo=swift&logoColor=white)

AsyncReactor is a reactive architecural pattern to organize your Swift code.

## Table of Contents
- [AsyncReactor Example](#asyncreactor-example)
  - [Table of Contents](#table-of-contents)
  - [Usage](#usage)
    - [General](#general)
  - [Integration](#integration)
    - [State updates](#state-updates)
    - [Actions](#actions)
    - [Action Bindings](#action-bindings)
  - [Example ](#example-)
  - [License ](#license-)

## Usage<a name="usage"></a>
### General
AsyncReactor is a reactive architecural pattern. The main component is an `AsyncReactor`. This `AsyncReactor` holds the `State` as well as the `Actions`.

```Swift
class RepositorySearchReactor: AsyncReactor {
    enum Action {
        ...
    }
    
    struct State {
        ...
    }
    
    @Published
    private(set) var state: State

    func action(_ action: Action) async { 
        ...
    }
}
```

## Integration
The `AsyncReactor` is provided to the child view as an `EnvironmentObject`. This is set by the `ReactorView`.
```Swift
ReactorView(RepositorySearchReactor()) {
    RepositorySearchView()
}
```

Now the reactor can simply be used in the SwiftUI view as follows.
```Swift
struct RepositorySearchView: View {

    @EnvironmentObject
    private var reactor: RepositorySearchReactor

    var body: some View { 
        ... 
    }
}
```

### State updates
Whenever the `State` in the reactor changes, the view will updated accordingly. It is also possibel to bind the `State`.

```Swift
var body: some View { 
    Text(reactor.state.name)
}
``````


### Actions
Actions are used to trigger a behaviour in our reactor. 

```Swift
var body: some View { 
    Button("Click me") {
        reactor.action(.buttonClick)
    }
}
``````


### Action Bindings
Action bindings enable us to bind values of the state in our view.

```Swift
struct RepositorySearchView: View {

    ...

    @ActionBinding(RepositorySearchReactor.self, 
                   keyPath: \.hidePrivate,
                   action: RepositorySearchReactor.Action.onHidePrivateToggle)
    private var hidePrivate: Bool

    var body: some View { 
        Toggle("Hide Private Repos", isOn: $hidePrivate)
    }
}

class RepositorySearchReactor: AsyncReactor {
  
    ...

    func action(_ action: Action) async {
        switch action {
        case .onHidePrivateToggle:
            state.hidePrivate.toggle()

        ...

        }
    }
}
```

## Example <a name="example"></a>
An example can be found in the [Example](./Example/AsyncReactorExample) folder.



AsyncReactor is a generic swift protocol which mainly stands for holding the state & handling background operations of the related SwiftUI view. Classes to be used as reactor of a view just need to implement the "AsyncReactor" protocol and implement the necessary predefined abstract functions/variables.

- Each SwiftUI view which needs to hold/observe a variable/state or needs to do some background operations should have a "Reactor" class defined in the SwiftUI view struct as "@EnvironmentObject".
- Each UI or background operations should be defined in "Action" enum in the reactor. "action()" function is the place where actual operations of these predefined actions are defined and executed. Each case in the Actions enum should be defined through a "switch" statement spesifically.
- Reactor State:
    - All the data, variables etc. should be placed in "State" struct.
    - Variables in the struct should have a default value so it can be initialized in the init block of the reactor.
    - There should be a state variable which holds the current state of the reactor. State var should be "Published" so the related SwiftUI view is notified whenever the data changed in the reactor. 
    - Setter of the reactor state should be **private** so it must be editable only from the reactor itself.
- "send()" function can be also used to invoke an action outside in non-background context.
- Variables which will be used as a "@Binding" variable in SwiftUI views can be annotated with "@ActionBinding" with an action in the reactor so the state will be updated automatically when this binding variable is reached. Various example use cases can be found in [RepositorySearchView](https://github.com/diamirio/AsyncReactor/blob/main/Example/AsyncReactorExample/Features/Repository/Search/RepositorySearchView.swift)

**Note:** "action()" function is already an async function and all the operations are being executed in a background task. So there is no need to create seperate "Task(s)" for the background operations. 

An example use case for such reactor can be found in the project ([here](https://github.com/diamirio/AsyncReactor/blob/main/Example/AsyncReactorExample/Features/Repository/Search/RepositorySearchReactor.swift))

## License <a name="license"></a>
```
MIT License

Copyright (c) 2023 DIAMIR Holding

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
