---
title: Building TicTacToe from scratch -- Swift WebAssembly
date: 12/04/2020
abstract: How to create a TicTacToe game using WebAssembly and Swift
image_url: https://awgsalesservices.com/wp-content/uploads/2019/10/TikTok-Logo-1180x655.png
tags: technology, swift, tutorial, web
---

If you just want to see how WebAssembly in Swift looks, and not how to build a TicTacToe game, skip [here](####Tracking)

I'll briefly go over how to setup your environment to building with SwiftWasm but, for a more in-depth look, take a look at the [official documentation](https://book.swiftwasm.org/index.html). 

## Setting up the environment

The offical Swift compiler does not, [yet](), support WebAssembly as a target so we'll have to use a [forked version]().

You can:
- manually install it
- use [swiftenv]()
- use [carton](https://github.com/swiftwasm/carton)

I reccomend carton -- it does all the heavy lifting for you.

On macOS, you can install `carton` via brew:
`brew install swiftwasm/tap/carton`

On Linux:

1. you'll have to clone `carton`
`git clone git@github.com:swiftwasm/carton.git && cd carton`

2. install the binaries
`./install_ubuntu_deps.sh`
> curl and copy [binaryen](https://github.com/WebAssembly/binaryen) to `/usr/local/bin`

3. build `carton`
`swift build -c release`
> build carton, the resultant binary will be in `.build/release`

4. put `carton` in your path
`sudo cp .build/release/carton /usr/local/bin`
> (Optional) move the compiled binary to your preferred bin directory 

#### if you're not using Ubuntu

I use Arch Linux, btw, so the Swift binary carton tries to fetch responds with:
`Error: This version of the operating system is not supported`

1. install [swiftenv](https://github.com/kylef/swiftenv)
2. get the latest release from [swiftwasm/swift](https://github.com/swiftwasm/swift/releases)
3. `swiftenv install {LATEST_RELEASE}`
> where LATEST_RELEASE is a url
> when writing this, the url is: https://github.com/swiftwasm/swift/releases/tag/swift-wasm-5.3.1-RELEASE

carton should now work correctly (baring clang, libc++, llvm, &c. errors)

## Setting up the project

Now that carton's installed, all you have to run is `carton init` to initialize a new project.

`carton init --name tic-tac-toe`

Carton will build a Swift package for us with [JavaScriptKit](todo) as it's only dependency.

I'm also going to bring in [HyperSwift](https://github.com/johngarrett/HyperSwift), a DSL to generate HTML & CSS, to build out the front end.

Your `Package.swift` file should look something like this now:

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
>Note: I've removed the defaults `Tests` product, you don't have to

## Designing the Game Object

First, I'm going to break out some common components into enums and structs

```swift
// the state of each tile
enum TileState: Equatable {
    case unoccupied, occupied(Player)
}

// the status of the game
enum GameStatus: Equatable {
    case inProgress
    case tie, won(Player, Set<Int>)
}

// each player
struct Player: Equatable {
    let identifier: String
    func occupiedTiles(from tiles: [TileState]) -> Set<Int> {
        Set(tiles.enumerated().filter { $0.element == .occupied(self) }.map { $0.offset })
    }
}
```

The player object may be a little overkill. If you want to change from the default `X` and `O`s or custom colors later on, they could be helpful

`occupiedTiles(from tiles) -> Set(tiles.enumerated().filter { $0.element == .occupied(self) }.map { $0.offset })`
This will return a `Set<Int>` of indecies pertaining to each tile this player occupies

```swift
tiles // An array of tile states: [TileState]
    .enumerated() // an array of (index, TileState): [(index, TileState)]
    .filter { // only keep values that satisify the following condition
        $0.element == .occupied(self)  // keep if this TileState is occupied by self, the Player
    }
    .map({ $0.offset }) // drop all the tile states: [Int]
```

Then wrap it all in a `Set( . . . )` for safetly and comparisons later on



```swift
struct Game {
    let primaryPlayer: Player // traditionally "X"
    let secondaryPlayer: Player // traditionally "O"
    var status: GameStatus 
    var currentPlayer: Player
    var trackedTiles: [TileState] // the state of each tile on the board
}
```

The games status can be: `.inProgress`, `.tie`, or `.won(/* by */ Player, /* with */ Set<Int>)`
    - the won case takes two parameters, the `Player` who won and the `Set<Int>` they won with

Each tile within `trackedTiles` is either `.occupied(/* by a*/ Player)` or `.unoccupied`

---

Let's init with some default values

```swift
init() {
    status = .inProgress
    primaryPlayer = Player(identifier: "X", color: Style.Color.primaryPlayer)
    secondaryPlayer = Player(identifier: "O", color: Style.Color.secondaryPlayer)
    currentPlayer = primaryPlayer
    trackedTiles = Array(repeating: TileState.unoccupied, count: 9) // 9 unoccupied tiles
}
```

Then we should build a function for occupying a tile externally
```swift
mutating func occupyTile(at index: Int) {
    trackedTiles[index] = TileState.occupied(currentPlayer)
    // update currentPlayer
    currentPlayer = currentPlayer == primaryPlayer ? secondaryPlayer : primaryPlayer
}
```
This way, from the front end, if the `Player` clicks on a button, we can track that in the `Game` object

And lastly, we need a way to check for a winner each round
```swift
private let winningCombinations = [
    // horizontal wins
    Set([0, 1, 2]),
    Set([3, 4, 5]),
    Set([6, 7, 8]),
    // vertical wins
    Set([0, 3, 6]),
    Set([2, 5, 8]),
    Set([1, 4, 7]),
    // diagnoal wins
    Set([0, 4, 8]),
    Set([2, 4, 6])
]

. . .

private mutating func checkForWinner() {
    // get each player's tiles
    let primaryTiles = primaryPlayer.occupiedTiles(from: trackedTiles)
    let secondaryTiles = secondaryPlayer.occupiedTiles(from: trackedTiles)
    
    
    // for every possible win 
    for combination in winningCombinations {
        // see if the player's tiles contain this winning set
        if combination == primaryTiles.intersection(combination) {
            status = .won(primaryPlayer, combination)
        }
        if combination == secondaryTiles.intersection(combination) {
            status = .won(secondaryPlayer, combination)
        }
    }

    if primaryTiles.count + secondaryTiles.count == 9 {
        // if there are 9 tiles occupied and no winner, the game was a tie
        status = .tie
    }
}
```

Here's what my `Game.swift` looks like now

```swift
import Foundation
import HyperSwift

enum TileState: Equatable {
    case unoccupied, occupied(Player)
}

enum GameStatus: Equatable {
    case inProgress
    case tie, won(Player, Set<Int>)
}

struct Player: Equatable {
    let identifier: String
    func occupiedTiles(from tiles: [TileState]) -> Set<Int> {
        Set(tiles.enumerated().filter { $0.element == .occupied(self) }.map { $0.offset })
    }
}

struct Game {
    let primaryPlayer: Player
    let secondaryPlayer: Player
    var status: GameStatus
    var currentPlayer: Player
    var trackedTiles: [TileState]
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
    
    init() {
        status = .inProgress
        primaryPlayer = Player(identifier: "X", color: Style.Color.primaryPlayer)
        secondaryPlayer = Player(identifier: "O", color: Style.Color.secondaryPlayer)
        currentPlayer = primaryPlayer
        trackedTiles = Array(repeating: TileState.unoccupied, count: 9)
    }
}

extension Game {
    mutating func occupyTile(at index: Int) {
        trackedTiles[index] = TileState.occupied(currentPlayer)
        currentPlayer = currentPlayer == primaryPlayer ? secondaryPlayer : primaryPlayer
        checkForWinner()
    }
    
    private mutating func checkForWinner() {
        let primaryTiles = primaryPlayer.occupiedTiles(from: trackedTiles)
        let secondaryTiles = secondaryPlayer.occupiedTiles(from: trackedTiles)
        
        for combination in winningCombinations {
            if combination == primaryTiles.intersection(combination) {
                status = .won(primaryPlayer, combination)
            }
            if combination == secondaryTiles.intersection(combination) {
                status = .won(secondaryPlayer, combination)
            }
        }

        if primaryTiles.count + secondaryTiles.count == 9 {
            status = .tie
        }
    }
}
```

## Designing the Front End

#### Hello World

Navigate to `./Sources/{project name}/main.swift`, it should look like this:

```swift
print("Hello, world!")
```

Lets move this introduction to the web. First, we need to import the required Frameworks:

```swift
import HyperSwift // HTML/CSS Generation
import JavaScriptKit
```

Next, we'll need to connect to the DOM and insert our "Hello, world!"
```swift
let document = JSObject.global.document // capture the document

var paragraph = document.createElement("p") // create a paragraph node
paragraph.innerText = "Hello, world!"
_ = document.body.appendChild(paragraph) // insert into the DOM
```

We're offically using WaSM!
[hello world]()

#### Creating the TicTacToe Board

The board we're trying to create looks something like this:

| X | O | X |
| - | - | - |
| O | X | O |
| - | - | - |
| X | O | X |

Here's what I ended up with:
![finished board]()

Each "cell" will be a `Tile` and the overall collection a `board`.

First, I've set constants for most of the styling I'll be doing

```swift
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

Here's how I'm designing my tiles
```swift
 Button("tile", id: id) {
    Header(.h2) { "⠀" }
        .font(size: Style.Font.headerSize, family: Style.Font.family)
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

Basically, just an `<h2>` element inside a `<button>` with some styling


I broke out tile creation since we're going to need 9 tiles
```swift

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
And here are those tiles

```swift
let tiles = (1...9).map { createTile(with: "tile-\($0)") }
```

Each tile is given it's own, *unique*, `id`; we'll need those to track the button events later on

Next, we need a board:
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

And, lastly, a status board:
```swift
let header = VStack(id: "header-pane", align: .center) {
     Header(.h1) { "X's turn" }
        .color(Style.Color.text)
}.flexGrow(1)
```

At the end of all that, we now have something that looks like this:
![end result]()

#### Tracking events

This is the real WebAssembly stuff -- interaction between the front end and backend.

The `onclick` handler:

```swift
func onClickHandler(index: Int, id: String) -> JSClosure {
    JSClosure { _ in
        game.occupyTile(at: index)
    }
}
```

Every time a tile is clicked, we need to know it's `id` and `index`.

With the `index`, we can tell the `Game` object what tile was just occupied

Then we need to connect each button to it's onclick handler
```swift
// a tuple of ButtonID and it's onClickHandler
let trackedReferences = tiles.enumerated().map { offset, element in
    (element.id, onClickHandler(index: offset, id: element.id))
}

// for each tracked refrence, attach their handler
trackedReferences.forEach { id, handler in
     document.getElementById(id).object?.onclick = .function(handler)
}
```

Each button reference should be tracked *TALK ABOUT GARABGE COLLECTION WHAT EVER*


## Combining the Back and the Front

In the game, we need a way to tell the front end about our state
```swift
typealias updateCallback = (_ status: GameStatus, _ currentPlayer: Player) -> Void

struct Game {
    var didUpdate: updateCallback
    . . .
    init(onUpdate callback: @escaping updateCallback) {
        didUpdate = callback
        . . .
    }

```
`didUpdate` is now a closure we can call each time that happens

Let's call it each time a tile is occupied
```swift
func occupyTile(at index: Int) {
    trackedTiles[index] = TileState.occupied(currentPlayer)
    currentPlayer = currentPlayer == primaryPlayer ? secondaryPlayer : primaryPlayer
    checkForWinner()
    didUpdate(status, currentPlayer)
}
```

Now, each time the game updates, we can tell the front end the new status and who's up next


On the front end add a function `gameDidUpdate` and pass it into `Game` 
``` swift
var game = Game(onUpdate: gameDidUpdate)

func gameDidUpdate(_ status: GameStatus, _ currentPlayer: Player) {
    print("Game updated with: \(status) and the current player is: \(currentPlayer.identifier)")
}
```

#### Updating the front end each round

```swift
func gameDidUpdate(_ status: GameStatus, _ currentPlayer: Player) {
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
        if case GameStatus.won(let player, let combinations) = status {
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

## Final code

`main.swift`
```swift
import HyperSwift
import JavaScriptKit

let document = JSObject.global.document
var game = Game(onUpdate: gameDidUpdate)
let tiles = (1...9).map { createTile(with: "tile-\($0)") }

let trackedReferences = tiles.enumerated().map { offset, element in
    (element.id, onClickHandler(index: offset, id: element.id))
}

document.body.object?.innerHTML = generateBody().jsValue()

trackedReferences.forEach { id, handler in
     document.getElementById(id).object?.onclick = .function(handler)
}

let customCSS = """
.losing-tile {
    opacity: 35%;
}
.\(game.primaryPlayer.identifier)-tile {
    color: \(Style.Color.primaryPlayer);
}
.\(game.secondaryPlayer.identifier)-tile {
    color: \(Style.Color.secondaryPlayer);
}
body {
    margin: 0px;
    padding: 0px;
}
"""

var style = document.createElement("style")
style.innerHTML = (CSSStyleSheet.generateStyleSheet() + customCSS).jsValue()
_ = document.head.appendChild(style)

func generateBody() -> String {
    Div {
        header
        board
    }
    .display(.flex)
    .flexWrap(.wrap)
    .justifyContent(.spaceBetween)
    .backgroundColor(Style.Color.background)
    .color(Style.Color.text)
    .alignItems(.center)
    .font(family: "monospace")
    .height(100, .percent)
    .width(100, .percent)
    .render()
}

func onClickHandler(index: Int, id: String) -> JSClosure {
    JSClosure {_ in
        game.occupyTile(at: index)
    }
}

func gameDidUpdate(_ status: GameStatus, _ currentPlayer: Player) {
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
        if case GameStatus.won(let player, let combinations) = status {
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

`Game.swift`
```swift
import Foundation
import HyperSwift

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

struct Game {
    typealias updateCallback = (_ status: GameStatus, _ currentPlayer: Player) -> Void
    let primaryPlayer: Player
    let secondaryPlayer: Player
    var status: GameStatus = .inProgress
    var currentPlayer: Player
    var trackedTiles: [TileState]
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
    var didUpdate: updateCallback
    
    init(onUpdate callback: @escaping updateCallback) {
        status = .inProgress
        primaryPlayer = Player(identifier: "X", color: Style.Color.primaryPlayer)
        secondaryPlayer = Player(identifier: "O", color: Style.Color.secondaryPlayer)
        currentPlayer = primaryPlayer
        trackedTiles = Array(repeating: TileState.unoccupied, count: 9)
        didUpdate = callback
    }
}

extension Game {
    mutating func occupyTile(at index: Int) {
        trackedTiles[index] = TileState.occupied(currentPlayer)
        currentPlayer = currentPlayer == primaryPlayer ? secondaryPlayer : primaryPlayer
        checkForWinner()
        didUpdate(status, currentPlayer)
    }
    
    private mutating func checkForWinner() {
        let primaryTiles = primaryPlayer.occupiedTiles(from: trackedTiles)
        let secondaryTiles = secondaryPlayer.occupiedTiles(from: trackedTiles)
        
        for combination in winningCombinations {
            if combination == primaryTiles.intersection(combination) {
                status = .won(primaryPlayer, combination)
            }
            if combination == secondaryTiles.intersection(combination) {
                status = .won(secondaryPlayer, combination)
            }
        }
        
        if primaryTiles.count + secondaryTiles.count == 9 {
            status = .tie
        }
    }
}
```

`Style.swift`
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