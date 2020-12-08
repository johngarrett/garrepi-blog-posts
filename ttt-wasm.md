---
title: TicTacToe in WebAssembly
date: 12/04/2020
abstract: How to create TicTacToe from scratch using WebAssembly and Swift
image_url: https://awgsalesservices.com/wp-content/uploads/2019/10/TikTok-Logo-1180x655.png
tags: technology, swift, tutorial, web
---

I'll briefly go over setting up your environment for building with SwiftWasm but, for a more in-depth look, read the [official documentation](https://book.swiftwasm.org/index.html). 

## Setting up the environment

I've had a lot of trouble switching between Swift compilers, espeically on Linux, so I'll be using [carton](https://github.com/swiftwasm/carton). 

On macOS, you can install carton via brew:
`brew install swiftwasm/tap/carton`

On Linux, you'll have to build it from source:
`git clone git@github.com:swiftwasm/carton.git && cd carton`
> clone carton

`./install_ubuntu_deps.sh`
> curl and copy [binaryen](https://github.com/WebAssembly/binaryen) to `/usr/local/bin`

`swift build -c release`
> build carton, the resultant binary will be in `.build/release`

`sudo cp .build/release/carton /usr/local/bin`
> (Optional) move the compiled binary to your preferred bin directory 

#### if you're not using Ubuntu

I use Arch Linux, btw, so the Swift compiler carton tries to fetch responds with:
`Error: This version of the operating system is not supported`

1. install [swiftenv](https://github.com/kylef/swiftenv)
2. get the latest release from [swiftwasm/swift](https://github.com/swiftwasm/swift/releases)
3. `swiftenv install ''`
> where '' is the latest swiftenv url, when writing this, the url is: https://github.com/swiftwasm/swift/releases/tag/swift-wasm-5.3.1-RELEASE

carton should now work correctly

## Setting up the project

Now that carton's installed, all you have to run is `carton init` to initialize a new project.

`carton init --name tic-tac-toe`

Carton will build a Swift package for us with [JavaScriptKit](todo) as it's only dependency.

We're also going to bring in [HyperSwift](https://github.com/johngarrett/HyperSwift), a DSL to generate HTML & CSS, to build our front end.

Your `Package.swift` file should look something like this:

```swift
// swift-tools-version:5.3
import PackageDescription
let package = Package(
    name: "tic-tac-toe",
    products: [
        .executable(name: "tic-tac-toe", targets: ["tic-tac-toe"])
    ],
    dependencies: [
        .package(name: "JavaScriptKit", url: "https://github.com/swiftwasm/JavaScriptKit", from: "0.8.0"),
        .package(url: "https://github.com/johngarrett/HyperSwift", .branch("master"))
    ],
    targets: [
        .target(
            name: "tic-tac-toe",
            dependencies: [.product(name: "JavaScriptKit", package: "JavaScriptKit"), "HyperSwift"]
        )
    ]
)
```
>Note: I've removed the defaults `Tests` product

## Budiling the Game object

I've opted to build a class to track and manage the game state.

First, I'm going to break some common components out into enums and structs

```swift
enum TileState: Equatable {
    case unoccupied, occupied(Player)
}

enum GameStatus: Equatable {
    case inProgress
    case tie, won(Player, Set<Int>)
}

struct Player: Equatable {
    static func == (lhs: Player, rhs: Player) -> Bool {
        lhs.identifier == rhs.identifier
    }
    
    let identifier: String
    let color: CSSColor
    func occupiedTiles(from tiles: [TileState]) -> Set<Int> {
        Set(tiles.enumerated().filter { $0.element == .occupied(self) }.map { $0.offset })
    }
}
```

```swift
class Game {
    let primaryPlayer: Player
    let secondaryPlayer: Player
    var status: GameStatus
    var currentPlayer: Player
    var trackedTiles: [TileState]
}
```

- primaryPlayer -> "X"
- secondaryPlayer -> "O"
- The games status can be: `.inProgress`, `.tie`, or `.won(/* by */ Player, /* with */ Set<Int>)`
    - the won case takes two parameters, the `Player` who won and the `Set<Int>` they won with 
- currentPlayer -> who's turn it is
- trackedTiles -> a state for every tile on the board
    - each tile is either `.occupied(/* by a*/ Player)` or `.unoccupied`

Let's init with some values

```swift
init() {
    status = .inProgress
    primaryPlayer = Player(identifier: "X", color: Style.Color.primaryPlayer)
    secondaryPlayer = Player(identifier: "O", color: Style.Color.secondaryPlayer)
    currentPlayer = primaryPlayer
    trackedTiles = Array(repeating: TileState.unoccupied, count: 9)
}
```

Then we should build a function for occupying a tile externally
```swift
func occupyTile(at index: Int) {
    trackedTiles[index] = TileState.occupied(currentPlayer)
    currentPlayer = currentPlayer == primaryPlayer ? secondaryPlayer : primaryPlayer
}
```

And lastly, let's build a way to check for a winner
```swift
private let winningCombinations = [
    Set([0, 1, 2]),
    Set([3, 4, 5]),
    Set([6, 7, 8]),
    Set([0, 4, 8]),
    Set([2, 4, 6]),
    Set([0, 3, 6]),
    Set([1, 4, 7]),
    Set([2, 5, 8])
]
private func checkForWinner() {
    let primaryTiles = primaryPlayer.occupiedTiles(from: trackedTiles)
    let secondaryTiles = secondaryPlayer.occupiedTiles(from: trackedTiles)
    
    guard primaryTiles.count + secondaryTiles.count != 9 else {
        status = .tie
        return
    }
    
    for combination in winningCombinations {
        if combination == primaryTiles.intersection(combination) {
            status = .won(primaryPlayer, combination)
        }
        if combination == secondaryTiles.intersection(combination) {
            status = .won(secondaryPlayer, combination)
        }
    }
}
```

## Building the Front End

#### Hello World

Navigate to `./Sources/{project name}/main.swift`, it should look like this:

```swift
print("Hello, world!")
```

Lets move this introduction to the web. First, we need to import the required Frameworks:

```swift
import HyperSwift
import JavaScriptKit
```

Next, we'll need to connect to the DOM and insert our "Hello, world!"
```swift
let document = JSObject.global.document // capture the document

var paragraph = document.createElement("p") // create a paragraph node
paragraph.innerText = "Hello, world!"
_ = document.body.appendChild(paragraph) // insert into the DOM
```

#### TicTacToe Board

The board we're trying to create looks something like this:

| X | O | X |
| - | - | - |
| O | X | O |
| - | - | - |
| X | O | X |

Here's what I ended up with:
![finishedboard]()

Let's call each "cell" a `Tile` and the overall collection a `board`.

Firstly, let's define constants for our styling:

```swift
import HyperSwift

enum Style {
    enum Color {
        static let text = CSSColor("#f8f8ff")
        static let background = CSSColor("#262335")
        static let tileBackground = CSSColor("#503c52")
        static let tileBorder = CSSColor("#454060")
        static let primaryPlayer = CSSColor("#ffbb6c")
        static let secondaryPlayer = CSSColor("#d4896a")
    }
    enum Font {
        static let headerSize = CSSUnit(4, .em)
        static let family = "monospace"
    }
}
```

then, the tiles:

```swift
let tiles = (1...9).map { createTile(with: "tile-\($0)") }

func createTile(with id: String) -> HTMLElement {
    Button("tile", id: id) {
        Header(.h2, cssClass: "title") { "⠀" }
            .font(size: Style.Font.headerSize, family: Style.Font.family)
            .padding(0)
            .margin(0)
    }
    .border(4, .solid, color: Style.Color.tileBorder)
    .borderRadius(16)
    .padding(10)
    .color(Style.Color.text)
    .backgroundColor(Style.Color.background)
    .width(100, .percent)
    .height(100, .percent)
}
```

and the board: 

```swift
let board = Div("board") {
    tiles
}
.display(.grid)
.maxWidth(800)
.width(100, .percent)
.height(80, .percent)
.gridTemplate("auto auto auto / auto auto auto")
.gridGap(5)
.padding(10)
.placeItems(.center)
.flexGrow(1)
```

lastly, the status board:
```swift
let header = VStack(id: "header-pane", align: .center) {
     Header(.h1) { "X's turn" }
        .color(Style.Color.text)
}.flexGrow(1)
```

At the end of all that, we now have something that looks like this:
![end result]()

#### Tracking events

```swift
func onClickHandler(index: Int, id: String) -> JSClosure {
    JSClosure {_ in
        game.occupyTile(at: index)
    }
}

let trackedReferences = tiles.enumerated().map { offset, element in
    (element.id, onClickHandler(index: offset, id: element.id))
}

. . .

trackedReferences.forEach { id, handler in
     document.getElementById(id).object?.onclick = .function(handler)
}
```
manual GC, etc.


## Putting it all together

game did update:

```swift
var didUpdate: ((_ status: GameStatus, _ currentPlayer: Player) -> Void)?
. . .

func occupyTile(at index: Int) {
    trackedTiles[index] = TileState.occupied(currentPlayer)
    currentPlayer = currentPlayer == primaryPlayer ? secondaryPlayer : primaryPlayer
    checkForWinner()
    didUpdate?(status, currentPlayer)
}
```

then in main.swift:

``` swift
game.didUpdate = { status, currentPlayer in
    print("Game updated with: \(status) and the current player is: \(currentPlayer.identifier)")
}
```


```swift
game.didUpdate = { status, currentPlayer in
    // update occupied tiles
    tiles.enumerated().forEach { index, tile in
        let state = game.trackedTiles[index]
        var ref = document.getElementById(tile.id)
        if case .occupied(let player) = state {
            _ = ref.classList.add("\(player.identifier)-tile")
            ref.firstChild.innerHTML = player.identifier.jsValue()
            ref.disabled = true.jsValue()
        }
    }
    
    guard status == .inProgress else {
        var subtext: String = ""
        if case GameStatus.won(let player, let combinations) = game.status {
            tiles.enumerated().filter({ !combinations.contains($0.offset) }).forEach {
                _ = document.getElementById($0.element.id).object?.classList.add("losing-tile")
            }
            subtext = "Player \(player.identifier) won!"
        } else {
            subtext = "tie"
        }
        
        tiles.forEach { document.getElementById($0.id).object?.disabled = true.jsValue() }
        
        document.getElementById("header-pane").object?.innerHTML = VStack(align: .center) {
            Header(.h1) { "Game Over!" }
            Header(.h3) { subtext }
                .padding(bottom: 15)
        }
        .render()
        .jsValue()
        
        return
    }
   
    document.getElementById("header-pane").object?.lastChild.innerHTML = createCurrentPlayerStatus(from: currentPlayer).render().jsValue()
}
```