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


## Building the Front End

## Building the Back End

## Putting it all together
