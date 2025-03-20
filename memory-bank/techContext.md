# PowerShellUiSampler Technical Context

## Technology Stack

### Core Technologies
- **.NET Framework** - The foundation platform for the application
- **C#** - Primary programming language
- **Windows Presentation Foundation (WPF)** - UI framework
- **PowerShell Integration** - System.Management.Automation namespace
- **Task Parallel Library (TPL)** - For asynchronous operations

### Dependencies
- **System.Management.Automation** - For PowerShell integration
- **System.Threading.Tasks** - For asynchronous programming
- **PresentationFramework** - For WPF UI components

## Key Technical Components

### PowerShell Integration
The project uses the System.Management.Automation namespace to:
- Create and manage PowerShell runspaces
- Execute PowerShell scripts asynchronously and synchronously
- Handle PowerShell output streams (standard output, errors, warnings, etc.)
- Load PowerShell modules

```csharp
// Example - Creating a PowerShell runspace with modules
var sessionState = InitialSessionState.CreateDefault();
sessionState.ImportPSModule(modules);
runspace = RunspaceFactory.CreateRunspace(sessionState);
runspace.Open();
```

### Asynchronous Programming
The application leverages the Task Parallel Library for:
- Non-blocking UI during PowerShell execution
- Proper cancellation handling
- Managing long-running operations

```csharp
// Example - Asynchronous PowerShell execution
var task = Task.Factory.FromAsync(powershell.BeginInvoke(), result => powershell.EndInvoke(result));
await task;
```

### WPF UI Framework
The TestApp uses WPF with:
- XAML-defined UI components
- Data binding for displaying PowerShell results
- Custom DataTemplates for styled output display
- Tab-based interface for different execution scenarios
- Progress bar animations

## Development Environment

### Required Tools
- Visual Studio (2017 or later recommended)
- .NET Framework SDK
- PowerShell (v3.0 or later)

### Project Structure
- **PowerShellWrapper** - Class library project
  - Core PowerShell integration functionality
  - Task extension methods for cancellation
- **TestApp** - WPF Application project
  - UI implementation
  - Sample PowerShell execution scenarios

## Technical Constraints

### PowerShell Version Compatibility
- The wrapper should work with PowerShell 3.0 and later
- PowerShell Core (6.0+) may require modifications

### Thread Safety
- PowerShell operations must be properly synchronized
- UI updates must be marshaled to the UI thread

### Resource Management
- PowerShell runspaces must be properly disposed
- CancellationTokenSource instances should be disposed after use

### Platform Requirements
- Windows operating system required (due to WPF)
- .NET Framework runtime environment
