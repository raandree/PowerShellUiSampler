# PowerShellUiSampler

## Overview

PowerShellUiSampler is a demonstration project that shows how to integrate PowerShell execution into .NET applications. It provides a clean, reusable approach for running PowerShell commands from .NET code with proper UI integration, offering both synchronous and asynchronous execution patterns.

> **Note**: This project is intended for demonstration purposes only and is not recommended for production use.

![Sample UI](/docs//assets//UiScreenshot.jpg) *The TestApp UI*

## Core Components

The project consists of two main components:

1. **PowerShellWrapper** - A .NET library that abstracts and simplifies PowerShell interaction
   - Manages PowerShell runspace creation and disposal
   - Provides methods for script execution (sync and async)
   - Handles PowerShell output streams
   - Implements proper resource cleanup

2. **TestApp** - A WPF UI application that demonstrates various ways to call PowerShell
   - Tab-based interface for different execution scenarios
   - Color-coded output display for different PowerShell streams
   - Progress bar for operation feedback
   - Cancellation support for long-running operations

## Features

- **Multiple Execution Models**
  - Synchronous execution with `InvokeSynchronous`
  - Asynchronous execution with `Invoke` (Task-based)
  - Script file execution with `InvokeScriptFileSynchronous`

- **Complete Stream Handling**
  - Standard output
  - Error output
  - Warning messages
  - Verbose output
  - Debug information
  - Progress updates

- **Advanced Integration**
  - Cancellation support for long-running operations
  - Pre-loading PowerShell modules
  - Thread-safe UI updates
  - Progress reporting

- **Resource Management**
  - Proper disposal of PowerShell resources
  - Cleanup on application exit

## Getting Started

### Prerequisites

- Windows operating system
- .NET Framework 4.5 or later
- PowerShell 3.0 or later
- Visual Studio 2017 or later (for development)

### Building the Project

1. Clone the repository
2. Open `source/PowerShellUiSampler.sln` in Visual Studio
3. Build the solution (F5 or Ctrl+Shift+B)

## Usage Examples

### Basic Usage of PowerShellWrapper

```csharp
// Create a PowerShell wrapper
using (var ps = new PowerShellWrapper())
{
    // Execute a simple command
    var results = ps.InvokeSynchronous("Get-Process | Select-Object -First 5");
    
    // Process the results
    foreach (var result in results)
    {
        Console.WriteLine(result.ToString());
    }
}
```

### Executing PowerShell with Module Support

```csharp
// Specify modules to load
string[] modules = new string[] { "MyModule" };

// Create a PowerShell wrapper with modules
using (var ps = new PowerShellWrapper(modules))
{
    // Execute a command that uses the module
    var results = ps.InvokeSynchronous("Get-MyModuleData");
    
    // Process the results
    // ...
}
```

### Asynchronous Execution with Stream Handling

```csharp
using (var ps = new PowerShellWrapper())
{
    // Setup handlers for different output streams
    Action<object, DataAddedEventArgs> errorHandler = (sender, e) => 
        Console.WriteLine($"Error: {((PSDataCollection<ErrorRecord>)sender)[e.Index]}");
    
    Action<object, DataAddedEventArgs> warningHandler = (sender, e) => 
        Console.WriteLine($"Warning: {((PSDataCollection<WarningRecord>)sender)[e.Index]}");
    
    // Similar handlers for verbose, debug, and progress...
    
    // Execute asynchronously
    var task = ps.Invoke(
        "1..10 | ForEach-Object { Write-Warning $_; Start-Sleep -Seconds 1 }",
        errorHandler,
        warningHandler,
        verboseHandler,
        debugHandler,
        progressHandler);
        
    // Wait for completion
    await task;
    
    // Process results
    foreach (var result in task.Result)
    {
        Console.WriteLine($"Result: {result}");
    }
}
```

### Cancelling Long-running Operations

```csharp
using (var ps = new PowerShellWrapper())
{
    var cts = new CancellationTokenSource();
    
    // Start long-running operation
    var task = ps.Invoke("1..100 | ForEach-Object { Start-Sleep -Seconds 1; $_ }");
    
    // In another thread or UI event handler:
    cts.Cancel();  // Request cancellation
    ps.Stop();     // Stop PowerShell execution
    
    try
    {
        await task.HandleCancellation(cts.Token, ps.Stop);
    }
    catch (OperationCanceledException)
    {
        Console.WriteLine("Operation was cancelled");
    }
}
```

### Loading and Executing Script Files

```csharp
using (var ps = new PowerShellWrapper())
{
    // Execute script from a file
    var results = ps.InvokeScriptFileSynchronous(@"C:\Scripts\MyScript.ps1");
    
    // Process results
    // ...
}
```

## Demo Application Features

The included WPF TestApp demonstrates several scenarios:

1. **Data Initialization Tab**
   - Shows how to pre-load data from PowerShell on application startup
   - Populates a ComboBox with directory information
   - Sets a Calendar control's date using PowerShell

2. **Synchronous Execution Tab**
   - Execute PowerShell scripts synchronously
   - Configure number of iterations
   - Simple command execution without UI thread concerns

3. **Asynchronous Execution Tab**
   - Execute PowerShell scripts asynchronously with TPL
   - Progress bar updates during execution
   - Cancellation support for long-running operations
   - Color-coded output for different stream types

4. **Script File Tab**
   - Load and execute PowerShell script files
   - File picker dialog integration

## Known Limitations

This demonstration project has several known limitations:

1. **Hard-coded Module Path**
   - The sample code in MainWindow.xaml.cs uses a hard-coded path (`D:\TestModule.psm1`) for loading modules
   - This should be made configurable for real-world use

2. **Limited Error Handling**
   - Basic error handling is implemented but not comprehensive
   - PowerShell exceptions could be parsed and displayed more effectively

3. **No Script Validation**
   - Scripts are executed without validation or syntax checking
   - Could lead to unexpected errors during execution

4. **UI Threading Considerations**
   - While the code uses Dispatcher.Invoke for UI updates, edge cases may exist
   - More comprehensive thread safety may be needed for production code

5. **PowerShell Core Compatibility**
   - This sample is designed for Windows PowerShell
   - Modifications may be needed for PowerShell Core compatibility

6. **No Security Measures**
   - This demo does not implement security best practices
   - Production code should address script execution security

## Contributing

This project is primarily for demonstration purposes. However, suggestions for improvements or bug fixes are welcome through issues and pull requests.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
