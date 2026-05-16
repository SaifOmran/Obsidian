### cmdlets
- It consists of verb-none
- It returns objects so you can filter and sort them, unlike the Unix commands which returns text
- Examples
```Powershell
Get-ChildItem
Get-Service
Set-Location
Start-Service
Stop-Service
```

- To show all verbs
```Powershell
Get-Verb
```

- Some of cmdlets have aliases to make it easy for the Unix users or CMD users
```Powershell
ls = dir = Get-ChildItem
pwd = Get-Location
cp = Copy-Item
cd = Set-Location
```

- To show the cmdlets command of an alias 
```Powershell
Get-alias [alias]

#Example
Get-alias ls
# output
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           ls -> Get-ChildItem
```

- As `Get-Alias` is cmdlets command so it has alias too!!
```Powershell
gal = Get-Alias
```

- To show all aliases and their definitions
```Powershell
gal or Get-Alias
```

- you can use wildcards with the aliases
```Powershell
gal *s # Get all aliases which ends with 's'
```

- you can get the alias of cmdlets command and you can use the wildcards too.
```Powershell
gal -Definition Get-ChildItem
# output
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           dir -> Get-ChildItem
Alias           gci -> Get-ChildItem
Alias           ls -> Get-ChildItem
```

### Native  commands
```Powershell
ping
calc
notepad
ipconfig /all
```

### How to get help in powershell
```Powershell
Get-Help [cmdlets | alias] == help [cmdlets | alias] == man [cmdlets | alias] # man is alias for help
#example
Get-Help ls == help ls == man ls
```

- You can use wildcards with `help` also
```Powershell
help get*
help *service*
help *adcomputer
```

- What if we need more help ? like more explanation and maybe some examples of the command 
```Powershell
help [cmdlets | alias] -Detailed
#Example
help Get-Service -Detailed
```

- To get the examples directly
```Powershell
help ls -Examples
```

- There is a parameter with `help` which is `-Full`, it shows what `-Detailed` shows + more info.
```Powershell
Get-Help Get-ChildItem -Full
```

- What if you want to get the official online documentation of a command ?
```Powershell
Get-Help Get-Service -online
```

- What if you want to get small windows of the output of the help ?
```Powershell
Get-Help Get-Service -ShowWindow
```

### Pipeline
- The pipeline operator helps you get better result for your output and making different changes to the output.
```Powershell
Get-Service | Out-File service.txt
Get-Service | ConvertTo-HTML | Out-File Service.html == Get-Service | Export-CSV service.csv
#Export = ConvertTo-CSV + Out-File
```

- To see what will happen if you make an action
```Powershell
Stop-Service bits -WhatIf
#output 
What if: Performing the operation "Stop-Service" on target "Background Intelligent Transfer Service (BITS)".
```

- What if you want the sell to ask you if are you sure about the action you will take ?
```Powershell
Stop-Service BITS -confirm
#output 
Confirm
Are you sure you want to perform this action?
Performing the operation "Stop-Service" on target "Background Intelligent Transfer Service (BITS)".
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

### Snap-ins and Modules
- PowerShell has limited number of cmdlets, providers and functions, so we need to extend the shell.
- So imagine you need to manage VMware using powershell, How could we do that ? By importing the modules of VMware to our shell.
- Snap-ins is the old way to extend the shell and it needs you to register the snap-ins in the windows, so we use modern things call modules.
```Powershell
Install-Module -Name VMware.PowerCLI
Import-Module -Name VMware.PowerCLI
```

- To show the modules that are loaded in the current session
```Powershell
Get-Module
```

- To show all installed modules
```Powershell
Get-Module -ListAvailable
```

### Objects
- In Linux when you see the output of a command it is just a text, while in powershell it is an object which helping you to filter, sort and make other things on the output thanks to it.
- The objects have properties and methods
	- ![[Pasted image 20260510173703.png]]
- To show all properties and methods of objects pipe it to `Get-Member == gm`
```Powershell
Get-Process | gm
```

- To filter the process to only get the process with number higher than 1200
```Powershell
Get-Process | where -Handles -gt 1200
```

- What if you want them sorted ? Easy, they are objects
```Powershell
Get-Process | where -Handles -gt 1200 | Sort-Object Handles
```

- There are too many things you can make to objects
- ![[Pasted image 20260510174528.png]]
- you can use aliases of the object commands, check them `gal -Definition *object*`


- ![[Pasted image 20260510174225.png]]
- In the previous image, what if you want to get only Id and ProcessName properties ?
```Powershell
Get-Process | Select-Object -Property Id, ProcessName
```

- `$_` -> means the current object passing the pipeline, it is used for filtering
- `$PSItem` == `$_`
```PowerShell
Get-Service | Where-Object {$_.Status -eq "Running"}
Get-Service | Where-Object {$PSItem.Status -eq "Running"}
```

- In the previous command, every service will be evaluated by its status and if it matches the condition, it will be shown, otherwise it is discarded.
> `Where-Object` does 3 steps 
> 1. Assign the object to `_`
> 2. Evaluate the code
> 3. Pass if True, discard if False

### Pipelines Deep Dive
1. Pass by value -> when the object type is supported by a parameter that accepts the pipeline input and also with same object type
```PowerShell
Get-Service | Stop-Service
```

- Here, `Get-Service` returns object type = System.ServiceProcess.ServiceController, and `Stop-Service` has a parameter with takes this object type, so the pipeline passes the objects by value.

2. Pass by property -> when the object type is not supported by a parameter, but there is a parameter = property of the input objects
```PowerShell
[PSCustomObject]@{
    Name = "Spooler"
} | Stop-Service
```

- Here, `PSCustomObject` returns object type = System.Management.Automation.PSCustomObject, and `Stop-Service` doesn't have a parameter that accepts this object type, but it has a parameter = property of `PSCustomObject` (which is Name property), so the pipeline matches between them and choose this parameter implicitly, so it becomes as you have typed
```PowerShell
Stop-Serive -Name "Spooler"
```

3. Customized property -> It is used when you have Pass by property, but the parameter name is not same as the property name, so you customize the property name to equal the parameter name and then it can match.
![[Pasted image 20260513044050.png]]

4. Extract the property value itself using `Select-Object -ExpandProperty <property>`
- Imagine there is a parameter that accepts strings only
```PowerShell
Stop-Service -Name (Get-Service | Select-Object -ExpandProperty Name)
# There is a simpler way
Stop-Service -Name (Get-Service).Name
```

![[Pasted image 20260513045501.png]]

- Extracting the value using scripting block
![[Pasted image 20260513050557.png]]
