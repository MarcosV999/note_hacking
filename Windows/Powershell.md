domain windows machine:
```powershell
whoami /all
systeminfo
(Get-ComputerInfo).CsDomain
```

The description of the computer designated as a Domain Controller
```powershell
# 1. Descubrimos el nombre del Domain Controller 
$DC = (Get-ADDomainController).Name
# 2. Buscamos sus propiedades completas 
Get-ADComputer -Identity $DC -Properties Description | Select-Object -ExpandProperty Description
```

**Get-Command** retrieves information about one or more commands, such a the name, category, version and even the module than contains it. **Get-Help** retrieves help content about the command. Those commands accept wildcards, such as the asterisk (\*). 
``` powershell
Get-Command *command*
Get-Help *command* -ShowWindow
Get-Help *command* -Online
```

**Get-Alias** accepts wildcards, such as the asterisk (\*)
```powershell
Get-Alias di*  # its alias is 'man'
New-Alias  # create customer alias
```

> [!info] The **Where** command is an alias for **Where-Object**, and the **Select** command is an alias for **Select-Object**.
# Discover object members in PowerShell

For most commands that you run, the default on-screen output doesn't include all of an object’s properties. Some objects have hundreds of properties, and the full list won't fit on the screen.
To use `Get-Member`, just pipe any command output to it. For example, enter the following command in the console, and then select Enter:
```powershell
Get-Service | Get-Member
Get-ChildItem | gm -Name Open
Get-ChildItem | gm -MemberType Properties
```

> [!info] `Get-Member` has an alias: `gm`.
# Control the formatting of pipeline output
PowerShell provides several ways to control the formatting of pipeline output. The default formatting of the output depends on the objects that exist in the output and the configuration files that define the output.
The formatting cmdlets are:
- `Format-List`: alias is fl
- `Format-Table`: alias is ft
- `Format-Wide`: alias is fw
- `Format-Custom`

You can override the default output formatting by specifying any of the preceding cmdlets as part of the pipeline. Each formatting cmdlet accepts the `-Property` parameter. The `-Property` parameter accepts a comma‑separated list of property names, and then it filters the list of properties that display and the order in which they display. Keep in mind that when you specify property names for this parameter, the original command must have returned those properties.
```powershell
Get-ADObject -filter * -Properties * | ft -Property Name, ObjectClass, Description -AutoSize -Wrap
Get-GPO -all | fw -Property DisplayName -Column 3
```

# Sort and group objects by property in the pipeline
## Sort-Object

```powershell
Get-Service | Sort-Object –Property Name –Descending 
Get-Service | Sort Name –Desc 
Get-Service | Sort Status,Name
```
You can also sort multiple properties in different directions by using hash tables as property expressions. Each hash table specifies a property name and an `Expression` and `Descending` key. For example:
```powershell
Get-Service | Sort-Object -Property @{Expression = "Status"; Descending = $true}, @{Expression = "Name"; Descending = $false}
```
## Grouping objects by property
The `Format-List`, `Format-Table`, and `Format-Wide` formatting cmdlets have a `-GroupBy` parameter that accepts a property name. By using the `-GroupBy` parameter, you can group the output by the specified property. For example, the following command displays the names of services running on the local computer in two two-column lists that are grouped by the `Status` property:
```powershell
Get-Service | Sort-Object Status,Name | fw -GroupBy Status
```
The `-GroupBy` parameter works similarly to the `Group-Object` command. The `Group-Object` command accepts piped input and gives you more control over the grouping of the objects. `Group-Object` has the alias `group`.

# Measure objects in the pipeline
The `Measure-Object` command can accept any kind of object in a collection. By default, the command counts the number of objects in the collection and produces a measurement object that includes the count. The `Measure-Object` command has the alias `Measure`.
The `-Property` parameter of `Measure-Object` allows you to specify a single property, which must contain numeric values. You can then include the `-Sum`, `-Average`, `-Minimum`, and `-Maximum` parameters to calculate those aggregate values for the specified property.
```powershell
Get-ChildItem -File | Measure -Property Length -Sum -Average -Minimum -Max
```

# Select a set of objects in the pipeline
Sometimes, you might not need to display all the objects that a command produces. For example, you've already learned that the **Get-EventLog** command has a parameter _-Newest_ that produces a list of only the newest event log entries. Not all commands have the built-in ability to limit their output like that. However, the **Select-Object** command can limit the output from any command. The **Select-Object** command has the alias **Select**.

```powershell
Get-Process | Sort-Object –Property VM | Select-Object –First 10
Get-Service | Sort-Object –Property Name | Select-Object –Last 10
Get-Process | Sort-Object –Property CPU –Descending | Select-Object –First 5 –Skip 1
```

## Selecting unique objects
```powershell
Get-ADUser -Filter * -Property Department | Sort-Object -Property Department | Select-Object Department -Unique
```
# Select object properties in the pipeline
```powershell
Get-Process | Select-Object –Property Name,ID,VM,PM,CPU | Format-Table
Get-Process | Sort-Object –Property CPU –Descending | Select-Object –Property Name,CPU –First 10
```

# Create and format calculated properties in the pipeline
`Select-Object` can create custom, or _calculated_, properties. Each of these properties has a label, or name, that PowerShell displays in the same way it displays any built-in property name. Each calculated property also has an expression that defines the contents of the property. You create each calculated property by entering the values in a hash table.
## Select-Object hash tables
When you use a hash table to create calculated properties by using `Select-Object`, you must use the following keys that PowerShell expects:

- `label`, `l`, `name`, or `n`. This specifies the label, or name, of the calculated property. Because the lowercase `l` resembles the number 1 in some fonts, try to use either `name`, `n`, or `label`.
- `expression` or `e`. This specifies the expression that sets the value of the calculated property.

```powershell
Get-Process | Select-Object Name,ID,@{n='VirtualMemory';e={$PSItem.VM}},@{n='PagedMemory';e={$PSItem.PM}}
```

# The comparison operators

| Operator | Description              |
| -------- | ------------------------ |
| `-eq`    | Equal to                 |
| `-ne`    | Not equal to             |
| `-gt`    | Greater than             |
| `-lt`    | Less than                |
| `-le`    | Less than or equal to    |
| `-ge`    | Greater than or equal to |
These operators are case-insensitive when used with strings. This case-insensitivity means that the results are the same whether the letters are capitalized or not. A case-sensitive version of each operator is available and begins with the letter `c`, such as `-ceq` and `-cne`.
PowerShell also contains the `-like` operator and its case-sensitive companion, `-clike`. The `-like` operator resembles `-eq` but supports the use of the question mark (?) and asterisk (\*) wildcard characters in string comparisons.
- The `-in` and `-contains` operators, which test whether an object exists in a collection.
- The `-is` and `-isnot` operators, which test whether an object is an instance of a specified .NET type. 

```powershell
Get-Service | Where Status –eq Running
```

You can also use the `Where` alias or the `?` alias, which is even shorter. You might prefer `$_` because it's more concise. The following commands perform the same task as the previous two commands:
```powershell
Get-Service | Where {$PSItem.Status –eq 'Running'} 
Get-Service | ? {$_.Status –eq 'Running'}
```
## Combining multiple criteria
```powershell
Get-WinEvent -LogName Security -MaxEvents 100 | Where { $PSItem.Id -eq 4672 -and $PSItem.LevelDisplayName -eq 'Information' }
Get-Process | Where { $PSItem.CPU –gt 30 –and VM –lt 10000 }
Get-Service | Where { $PSItem.Status –eq 'Running' –or 'Starting' }
```
## Accessing properties without limitations
```powershell
Get-Service | Where {$PSItem.Name.Length –gt 8}
```

# Write pipeline data to a file
```powershell
Get-Service | Sort-Object –Property Status, Name | Select-Object –Property DisplayName,Status | Out-File –FilePath ServiceList.csv
```

# Convert pipeline objects to other forms of data representation

```powershell
Get-Service | ConvertTo-Csv | Out-File Services.csv
```

A command that uses `Export`, such as `Export-Csv`, performs two operations: it converts the data and then writes the data to external storage, such as a file on disk.
```powershell
Get-Service | Export-Csv Services.csv
```
##### Converting output to XML: 
PowerShell provides two XML-related commands: `ConvertTo-Xml` and `Export-Clixml`
##### Converting output to JSON: 
In PowerShell, you create JSON‑formatted data by using the `ConvertTo-Json` command. As with the other `ConvertTo` commands, no output file is created. Unlike XML and CSV, however, JSON doesn't have an `Export` command for converting the data and creating an output file. Therefore, you must use `Out-File` or one of the text redirection operators to send the JSON data to a file.
##### Converting output to HTML:
Sometimes, you need to display your PowerShell output in a web browser or send the HTML output to another process. PowerShell supports this through the `ConvertTo-Html` command.

# Utils

```powershell
Get-Command *command*
Get-Variable
```
