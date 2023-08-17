# Setting Up VS Code Dev Containers for Swift Tokamak & Carton

In this guide, we'll walk you through the process of setting up Visual Studio Code (VS Code) dev containers for working with Swift Tokamak and Carton. These tools allow you to develop and test Swift applications with Tokamak UI components and manage Swift WebAssembly projects using Carton.

## Prerequisites

Before you begin, make sure you have the following prerequisites installed on your system:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Docker](https://www.docker.com/)

## Step 1: Clone Your Repository

Clone your Swift Tokamak project repository to your local machine using Git:

```bash
git clone <repository_url>
cd <repository_folder>
```

## Step 2: Create `.devcontainer.json`

Create a `.devcontainer.json` file in the root directory of your project and add the following content:

```json
{
    "name": "Tokamak",
    "image": "ghcr.io/swiftwasm/carton:latest",
    "features": {
        "ghcr.io/devcontainers/features/common-utils:2": {
            "installZsh": "false",
            "username": "vscode",
            "userUid": "1000",
            "userGid": "1000",
            "upgradePackages": "false"
        },
        "ghcr.io/devcontainers/features/git:1": {
            "version": "os-provided",
            "ppa": "false"
        }
    },
    "runArgs": [
        "--cap-add=SYS_PTRACE",
        "--security-opt",
        "seccomp=unconfined"
    ],
    "customizations": {
        "vscode": {
            "settings": {
                "lldb.library": "/Applications/Xcode.app/Contents/SharedFrameworks/LLDB.framework/Versions/A/LLDB",
                "lldb.launch.expressions": "native",
                "swift.formatOnSave": true,
                "swift.formatOptions": {
                    "lineBreakBeforeEachArgument": true,
                    "indentation": "spaces",
                    "lineLength": 100,
                    "alignMultilineComments": true
                }
            },
            "extensions": [
                "sswg.swift-lang",
                "vadimcn.vscode-lldb"
            ]
        }
    },
    "forwardPorts": [8080],
    "postCreateCommand": "carton --version",
    "remoteUser": "vscode"
}
```

This configuration specifies the development container setup, including the necessary tools and settings.

## Step 3: Create `launch.json`

Inside your `.vscode` directory, create a `launch.json` file if it doesn't exist already. Add the following content to it:

```json
{
    "configurations": [
        {
            "name": "Run Carton Dev",
            "type": "node-terminal",
            "request": "launch",
            "command": "carton dev",
            "cwd": "${workspaceFolder}"
        }
    ]
}
```

This configuration sets up a launch configuration for running `carton dev` inside your container.

## Step 4: Package.swift and Project Structure

Make sure your `Package.swift` and project structure are set up as follows:

```swift
// Package.swift

// ...

let package = Package(
    name: "TokamakApp",
    platforms: [.macOS(.v11), .iOS(.v13)],
    products: [
        .executable(name: "TokamakApp", targets: ["TokamakApp"])
    ],
    dependencies: [
       .package(name: "Tokamak", url: "https://github.com/TokamakUI/Tokamak", from: "0.11.0")
    ],
    targets: [
        .executableTarget(
            name: "TokamakApp",
            dependencies: [
                .product(name: "TokamakShim", package: "Tokamak")
            ],
            linkerSettings: [
                .unsafeFlags(["-Xlinker", "--stack-first", "-Xlinker", "-z", "-Xlinker", "stack-size=16777216"]),
            ]
        ),
        .testTarget(
            name: "TokamakAppTests",
            dependencies: ["TokamakApp"]
        ),
    ]
)
```

## Step 5: Develop with Swift Tokamak and Carton

1. Open your project folder in VS Code.
2. When prompted, click on "Reopen in Container" in the bottom-right corner of VS Code.
3. The development container will be built and started.
4. Open the `Sources/Tokamak/App/App.swift` file to work with your Tokamak app code.
5. Use the "Run Carton Dev" configuration from the Run and Debug menu to start Carton and run your application.

Please note that each time you make changes to your code, the automatic rebuild behavior may not be the same as on Mac or Linux environments. You will need to manually refresh the build by stopping and restarting the "Run Carton Dev" configuration. Additionally, after the build is refreshed, you may need to manually refresh the page in your browser to see the updated changes.

You are now set up to develop Swift Tokamak applications and manage Swift WebAssembly projects using Carton within a VS Code dev container.

## Additional Resources

- [Tokamak GitHub Repository](https://github.com/TokamakUI/Tokamak)
- [Carton GitHub Repository](https://github.com/swiftwasm/carton)

Happy coding!
