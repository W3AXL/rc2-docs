# Developing for RadioConsole2

RC2 is an expansive and ambitious project - we always welcome and encourage contributions to add additional control modes, interfaces, and bugfixes.

## Radio Daemon (Windows/Linux)

The radio `daemon` is a .NET 8.0 cross-platform command line application designed to connect with physical radio endpoints (base stations and other physical devices which provide TX & RX audio).

### Development Environment

You will need the .NET SDK in order to build the RC2 `daemon` application. Use the following page to get information on how to install the SDK:

[Install .NET on Windows, Linux, and macOS](https://learn.microsoft.com/en-us/dotnet/core/install/)

The RC2 Daemon also requires SDL2 for cross-platform audio interfacing. The repository includes SDL2 as a submodule and it can be built directly from this submodule,
assuming that you clone the repo using `--recurse-submodules`.

### Building the Daemon

Once the .NET SDK is installed, you can build the daemon easily using the following steps:

```console
git clone --recurse-submodules https://github.com/W3AXL/RadioConsole2.git
cd RadioConsole2/daemon
dotnet build
```

The `dotnet build` command should automatically install all required dependencies via NuGET and build the daemon.

## Console Client (Windows/Linux/OSX)

### Development Environment

To build the frontend console client, you will need to install Node.JS and the PNPM package manager. Follow the instructions below
to install Node on your operating system. Ensure that you select `pnpm` as the package manager instead of the default `npm`.

[Download Node.js](https://nodejs.org/en/download)

### Building the Console

Once Node and PNPM are installed, you can use the following steps to install all required Node dependencies and run the console in development mode

```console
git clone --recurse-submodules https://github.com/W3AXL/RadioConsole2.git
cd console
pnpm install
pnpm run start
```

Additionally, you can package the console into a single-file application using the command `pnpm run dist`. This will use [electron-builder](https://www.electron.build/) to compile a pre-packaged single executable binary in the `output/` folder.

## Daemon-Console Communication Protocol

The console communicates with the radio daemons via a websocket connection using a custom JSON messaging protocol. To learn more about this protocol and develop your own apps for RC2, read the protocol specification here: [Websocket Protocol Specification](websocket-protocol.md)