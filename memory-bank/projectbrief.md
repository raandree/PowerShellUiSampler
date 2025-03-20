# PowerShellUiSampler Project Brief

## Overview
PowerShellUiSampler is a demonstration project showing how to integrate PowerShell execution into .NET applications. It demonstrates both single-threaded and multi-threaded PowerShell invocation methods using the Parallel Task Library.

## Core Components
1. **PowerShellWrapper** - A .NET library that abstracts and simplifies PowerShell interaction
2. **TestApp** - A WPF UI application that demonstrates various ways to call PowerShell via the wrapper

## Primary Goals
- Demonstrate how to call PowerShell from .NET applications
- Show both synchronous and asynchronous PowerShell execution methods
- Implement proper cancellation and error handling for PowerShell operations
- Provide a UI that shows how to handle PowerShell output streams (standard output, errors, warnings, etc.)
- Demonstrate integration with the Parallel Task Library for smooth multi-threaded operations

## Target Users
- .NET developers who need to integrate PowerShell functionality into their applications
- System administrators building tools that leverage PowerShell

## Success Criteria
- Successful execution of PowerShell scripts from the UI
- Clear display of different PowerShell output streams
- Working cancellation and error handling
- Smooth UI experience even during multi-threaded PowerShell operations
