
# Chapter 3. Variables

It is time to combine commands whenever a single PowerShell command can't solve your problem. One way of doing this is by using variables. PowerShell can store results of one command in a variable and then pass the variable to another command. In this chapter, we'll explain what variables are and how you can use them to solve more complex problems.

**Topics Covered:**
## Table of Contents

* [Personal Variables](#personal-variables)
- Selecting Variable Names 
- Assigning and Returning Values 
- Assigning Multiple Variable Values 
- Exchanging the Contents of Variables 
- Assigning Different Values to Several Variables 
- Listing Variables 
- Finding Variables 
- Verify Whether a Variable Exists 
- Deleting Variables 
- Using Special Variable Cmdlets 
- Write-Protecting Variables: Creating Constants 
- Variables with Description 
- "Automatic" PowerShell Variables 
- Environment Variables 
- Reading Environment Variables 
- Searching for Environment Variables 
- Modifying Environment Variables 
- Permanent Modifications of Environment Variables 
- Scope of Variables 
- Automatic Restriction 
- Changing Variable Visibility 
- Setting Scope 
- Variable Type and "Strongly Typing" 
- Strongly Typing 
- The Advantages of Specialized Types 
- Variable Management: Behind the Scenes 
- Modification of Variable Options 
- Write Protecting Variables 
- Examining Strongly Typed Variables 
- Validating Variable Contents 
- Summary 


## Personal Variables

Variables store pieces of information. This way, you can first gather all the information you may need and store them in variables. The following example stores two pieces of information in variables and then calculates a new result:

```powershell
$amount = 120  
$VAT = 0.19
  
$result = $amount * $VAT
  
$result

22.8
  
$text = "Net amount $amount matches gross amount $result"  
$text
```

Net amount 120 matches gross amount 142.8

Of course, you can have hard-coded the numbers you multiplied. However, variables are the prerequisite for reusable code. By assigning your data to variables, you can easily change the information, either by manually assigning different values to your variables or by assigning user-defined values to your variables. By simply replacing the first two lines, your script can interactively ask for the variable content:

```powershell
[Int]$amount = "Enter amount of money"  
[Double]$VAT = "Enter VAT rate"  
```

Note that I strongly-typed the variables in this example. You will hear more about variable typing later in that character , but whenever you use Read-Host or another method that accepts user input, you have to specify the variable data type or else PowerShell will treat your input as simple string. Simple text is something very different from numbers and you cannot calculate with pieces of text.

PowerShell creates new variables automatically so there is no need to specifically "declare" variables. Simply assign data to a variable. The only thing you do need to know is that variable names are always prefixed with a "$" to access the variable content.

You can then output the variable content by entering the variable name or you can merge the variable content into strings. Just make sure to use double-quotes to do that. Single-quoted text will not expand variable values.

### Selecting Variable Names

You are free to call the variable anything you like – as long as the name is not causing misunderstandings. Variable names are always case-insensitive.

There are some special characters that have special meaning to PowerShell. If you used those in your variable names, PowerShell can get confused. So the best thing is to first avoid special characters in your variable names. But if you must use them for any reason, be sure to enclose the variable name in brackets:

```powershell
${#this is a strange variable name} = 12  
${#this is a strange variable name}

12
```

### Assigning and Returning Values

The assignment operator "=" assigns a value to a variable. You can assign almost anything to a variable, even complete command results:

```powershell
$listing = Get-ChildItem c:  
$listing  

Directory: Microsoft.PowerShell.CoreFileSystem::C:

Mode LastWriteTime Length Name  
(...)

  
$result = ipconfig  
$result

Windows IP Configuration

Ethernet adapter LAN Connection:

Media state

. . . . . . . . . . . : Medium disconnected  
Connection-specific DNS Suffix:  
Ethernet adapter LAN Connection 2:

Media state

. . . . . . . . . . . : Medium disconnected  
Connection-specific DNS Suffix:  
Wireless LAN adapter wireless network connection:

Media state

. . . . . . . . . . . : Medium disconnected  
Connection-specific DNS Suffix:
```


### Assigning Multiple Variable Values

If you'd like, you can use the assignment operator to assign values to multiple variables at the same time:

```powershell
$a = $b = $c = 1  
$a

1

$b

1

$c

1```

### Exchanging the Contents of Variables

Now and then you might want to exchange the contents of two variables. In traditional programming languages, that would require several steps:

```powershell
$Value1 = 10  
$Value2 = 20  
$Temp = $Value1  
$Value1 = $Value2  
$Value2 = $Temp
```

With PowerShell, swapping variable content is much easier because you can assign multiple values to multiple variables. Have a look:

```powershell
$Value1 = 10; $Value2 = 20  
$Value1, $Value2 = $Value2, $Value1
```

### Assigning Different Values to Several Variables

When you swap variable content like in the past example, it is possible because of arrays. The comma is used to create arrays, which are basically a list of values. If you assign one list of values to another list of values, PowerShell can assign multiple values at the same time. Have a look:

```powershell
$Value1, $Value2 = 10,20  
$Value1, $Value2 = $Value2, $Value1
```

### Listing Variables

PowerShell keeps a record of all variables, which is accessible via a virtual drive called _variable:_. Here is how you see all currently defined variables:

```powershell
Dir variable:
```

Aside from your own personal variables, you'll see many more. PowerShell also defines variables and calls them "automatic variables." You'll learn more about this soon.

### Finding Variables

Using the `_variable:_` virtual drive can help you find variables. If you'd like to see all the variables containing the word `Maximum` try this:

```powershell
Dir variable:*maximum*

Name                   Value  
\----                   -----  
MaximumErrorCount      256  
MaximumVariableCount   4096  
MaximumFunctionCount   4096  
MaximumAliasCount      4096  
MaximumDriveCount      4096  
MaximumHistoryCount    1000  
```

The solution isn't quite so simple if you'd like to know which variables currently contain the value 20. It consists of several commands piped together.

```powershell
dir variable: | Out-String -stream | Select-String " 20 "

value2 20  
$ 20
```

Here, the output from _Dir_ is passed on to _Out-String_, which converts the results of _Dir_ into string. The parameter _-Stream_ ensures that every variable supplied by _Dir_ is separately output as string. _Select-String_ selects the lines that include the desired value, filtering out the rest. White space is added before and after the number 20 to ensure that only the desired value is found and not other values that contain the number 20 (like 200).

### Verify Whether a Variable Exists

Using the cmdlet `_Test-Path_`, you can verify whether a certain file exists. Similar to files, variables are stored in their own "drive" called _variable:_ and every variable has a path name that you can verify with _Test-Path_. You can use this technique to find out whether you are running PowerShell v1 or v2:

  
Test-Path variable:psversiontable

True

  
If (Test-Path variable:psversiontable) {  
'You are running PowerShell v2'  
} else {  
'You are running PowerShell v1 and should update to v2'  
}

False

### Deleting Variables

PowerShell will keep track of variable use and remove variables that are no longer used so there is no need for you to remove variables manually. If you'd like to delete a variable immediately, again, do exactly as you would in the file system:

```powershell
$test = 1

  
Dir variable:te*

  
del variable:test

  
Dir variable:te*
```

### Using Special Variable Cmdlets

To manage your variables, PowerShell provides you with the five separate cmdlets listed in [Table 3.1][1]. Two of the five cmdlets offer substantially new options:

1. _New-Variable_ enables you to specify options, such as a description or write protection. This makes a variable into a constant. _Set-Variable_ does the same for existing variables.
2. _Get-Variable_ enables you to retrieve the internal PowerShell variables store.

| ----- |
| **Cmdlet** |  **Description** |  **Example** |
| _Clear-Variable_ |  Clears the contents of a variable, but not the variable itself. The subsequent value of the variable is NULL (empty). If a data or object type is specified for the variable, by using _Clear-Variable_ the type of the objected stored in the variable will be preserved.  |  Clear-Variable a  
_same as: $a = $null_ |
| _Get-Variable_ |  Gets the variable object, not the value in which the variable is stored. |  Get-Variable a |
| _New-Variable_ |  Creates a new variable and can set special variable options. |  New-Variable value 12 |
| _Remove-Variable_ |  Deletes the variable, and its contents, as long as the variable is not a constant or is created by the system. |  Remove-Variable a  
_same as: del variable:a_ |
| _Set-Variable_ |  Resets the value of variable or variable options, such as a description and creates a variable if it does not exist. |  Set-Variable a 12  
_same as: $a = 12_ |

**Table 3.1:** Cmdlets for managing variables

### Write-Protecting Variables: Creating Constants

Constants store a constant value that cannot be modified. They work like variables with a write-protection.

PowerShell doesn't distinguish between variables and constants. However, it does offer you the option of write-protecting a variable. In the following example, the write-protected variable _$test_ is created with a fixed value of 100. In addition, a description is attached to the variable.

  
New-Variable test -value 100 -description `  
"test variable with write-protection" -option ReadOnly  
$test

100

  
$test = 200

The variable "test" cannot be overwritten since it is a  
constant or read-only.  
At line:1 char:6  
\+ $test <<<< = 200

The variable is now write-protected and its value may no longer be changed. You'll receive an error message if you try it anyway. Because the variable is write-protected, it behaves like a read-only file. You'll have to specify the parameter _-Force_ to delete it:

del variable:test -force  
$test = 200

As you just saw, a write-protected variable can still be modified by deleting it and creating a new copy of it. If you need stronger protection, you can create a variable with the _Constant_ option. Now, it can neither be modified nor deleted. Only when you quit PowerShell are constants removed. Variables with the _Constant_ option may only be created with _New-Variable_. If a variable already exists, you cannot make it constant anymore because you'll get an error message:

  
New-Variable test -value 100 -description `  
"test variable with copy protection" -option Constant

New-Variable : A variable named "test" already exists.  
At line:1 Char:13  
\+ New-Variable <<<< test -value 100 -description  
"test variable with copy protection" -option Constant

  
del variable:test -force  
New-Variable test -value 100 -description `  
"test variable with copy protection" `  
-option Constant

  
del variable:test -force

Remove-Item : variable "test" may not be removed since it is a  
constant or write-protected. If the variable is write-protected,  
carry out the process with the Force parameter.  
At line:1 Char:4  
\+ del <<<< variable:test -force

You can overwrite an existing variable by using the _-Force_ parameter of _New-Variable_ if the existing variable wasn't created with the _Constant_ option. Variables of the constant type are unchangeable once they have been created and _-Force_ does not change this:

  
New-Variable test -value 100 -description "test variable" -force

New-Variable : variable "test" may not be removed since it is a  
constant or write-protected.  
At line:1 char:13  
\+ New-Variable <<<< test -value 100 -description "test variable"

  
$available = 123  
New-Variable available -value 100 -description "test variable" -force

### Variables with Description

Variables can have an optional description to help you keep track of what the variable was intended for. However, this description appears to be invisible:

  
New-Variable myvariable -value 100 -description "test variable" -force

  
$myvariable

100

  
Dir variable:myvariable

Name       Value  
\----       -----  
myvariable 100

Get-Variable myvariable

Name       Value  
\----       -----  
myvariable 100

## "Automatic" PowerShell Variables

PowerShell also uses variables for internal purposes and calls those "automatic variables." These variables are available right after you start PowerShell since PowerShell has defined them during launch. The drive _variable:_ provides you with an overview of all variables:

Get-Childitem variable:

Name            Value  
\----            -----  
Error           {}  
DebugPreference SilentlyContinue  
PROFILE         C:UsersTobias WeltnerDocumentsWindowsPowerShellMicro...  
HOME            C:UsersTobias Weltner  
(...)

You can show their description to understand the purpose of automatic variables:

Get-Childitem variable: | Sort-Object Name |  
Format-Table Name, Description -AutoSize -Wrap

Use _Get-Help_ to find out more

PowerShell write protects several of its automatic variables. While you can read them, you can't modify them. This makes sense because information, like the process-ID of the PowerShell console or the root directory, must not be modified.

$pid = 12

Cannot overwrite variable "PID" because it is read-only or constant.  
At line:1 char:5  
\+ $pid <<<< = 12

A little later in this chapter, you'll find out more about how write-protection works. You'll then be able to turn write-protection on and off for variables that already exist. However, don't do this for automatic variables because PowerShell may crash. One reason is because PowerShell continually modifies some variables. If you set them to read-only, PowerShell may stop and not respond to any inputs.

## Environment Variables

There is another set of variables maintained by the operating system: environment variables.

Working with environment variables in PowerShell is just as easy as working with internal PowerShell variables. All you need to do is add the prefix to the variable name: _env:_.

### Reading Environment Variables

You can read the location of the Windows folder of the current computer from a Windows environment variable:

$env:windir

C:Windows

By adding _env:_, you've told PowerShell not to look for the variable _windir_ in the default PowerShell variable store, but in Windows environment variables. In other word, the variable behaves just like any other PowerShell variable. For example, you can embed it in some text:

"The Windows folder is here: $env:windir"  
The Windows folder is here: C:Windows

You can just as easily use the variable with commands and switch over temporarily to the Windows folder like this:

  
Push-Location

  
cd $env:windir  
Dir

  
Pop-Location

### Searching for Environment Variables

PowerShell keeps track of Windows environment variables and lists them in the _env:_ virtual drive. So, if you'd like an overview of all existing environment variables, you can list the contents of the _env:_ drive:

Get-Childitem env:

Name            Value  
\----            -----  
Path            C:Windowssystem32;C:Windows;C:WindowsSystem32Wbem;C:  
TEMP            C:UsersTOBIAS~1AppDataLocalTemp  
ProgramData     C:ProgramData  
PATHEXT         .COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC;.4mm  
ALLUSERSPROFILE C:ProgramData  
PUBLIC          C:UsersPublic  
OS              Windows_NT  
USERPROFILE     C:UsersTobias Weltner  
HOMEDRIVE       C:  
(...)

You'll be able to retrieve the information it contains when you've located the appropriate environment variable and you know its name:

$env:userprofile

C:UsersTobias Weltner

### Modifying Environment Variables

You can modify environment variables by simply assigning new variables to them. Modifying environment variables can be useful to change the way your machine acts. For example, all programs and scripts located in a folder that is listed in the "PATH" environment variable can be launched by simply submitting the file name. You no longer need to specify the complete path or a file extension.

The next example shows how you can create a new folder and add it to the PATH environment variable. Any script you place into that folder will then be accessible simply by entering its name:

  
md c:myTools

  
" 'Hello!' " > c:myToolssayHello.ps1

  
C:myToolssayHello.ps1  
Hello!

  
$env:path += ";C:myTools"

  
sayHello  
Hello!

### Permanent Modifications of Environment Variables

By default, PowerShell works with the so-called "process" set of environment variables. They are just a copy and only valid inside your current PowerShell session (and any programs you launch from it). Changes to these environment variables will not persist and are discarded once you close your PowerShell session.

You have two choices if you need to make permanent changes to your environment variables. You can either make the changes in one of your profile scripts, which get executed each time you launch PowerShell (then your changes are effective in any PowerShell session but not outside) or you can use sophisticated .NET methods directly to change the underlying original environment variables (in which case the environment variable change is visible to anyone, not just PowerShell sessions). This code adds a path to the Path environment variable and the change is permanent.

$oldValue = [environment]::GetEnvironmentvariable("Path", "User")  
$newValue = ";c:myTools"  
[environment]::SetEnvironmentvariable("Path", $newValue, "User")

Access to commands of the .NET Framework as shown in this example will be described in depth in [Chapter 6][2]

When you close and restart PowerShell, the _Path_ environment variable will now retain the changed value. You can easily check this:

$env:Path

The permanent change you just made applies only to you, the logged-on user. If you'd like this change to be in effect for all computer users, you can replace the "User" argument by "Machine." You will need full administrator privileges to do that.

You should only change environment variables permanently when there is no other way. For most purposes, it is completely sufficient to change the temporary process set from within PowerShell. You can assign it the value of _$null_ to remove a value.

## Scope of Variables

PowerShell variables can have a "scope," which determines where a variable is available. PowerShell supports four special variable scopes: _global_, _local, private_, and _script_. These scopes allow you to restrict variable visibility in functions or scripts.

### Automatic Restriction

Typically, a script will use its own variable scope and isolate all of its variables from the console. So when you run a script to do some task, it will not leave behind any variables or functions defined by that script once the script is done.

### Changing Variable Visibility

You can change this default behavior in two different ways. One is to call the script "dot-sourced": type in a dot, then a space, and then the path to the script. Now, the script's own scope is merged into the console scope. Every top-level variables and functions defined in the script will behave as if they had been defined right in the console. So when the script is done, it will leave behind all such variables and functions.

Dot-sourcing is used when you want to (a) debug a script and examine its variables and functions after the script ran, and (b) for library scripts whose purpose is to define functions and variables for later use. The profile script, which launches automatically when PowerShell starts, is an example of a script that always runs dot-sourced. Any function you define in any of your profile scripts will be accessible in your entire PowerShell session – even though the profile script is no longer running.

### Setting Scope

While the user of a script can somewhat control scope by using dot-sourcing, a script developer has even more control over scope by prefixing variable and function names. Let's use the scope modifiers _private_, _local_, _script_, and _global_.

| ----- |
| **Scope allocation** |  **Description** |
| __$private:test = 1__ |  The variable exists only in the current scope. It cannot be accessed in any other scope. |
| _$local:test = 1_ |  Variables will be created only in the local scope. That's the default for variables that are specified without a scope. Local variables can be read from scopes originating from the current scope, but they cannot be modified. |
| _$script:test = 1_ |  This scope represents the top-level scope in a script. All functions and parts of a script can share variables by addressing this scope. |
| _$global:test = 1_ |  This scope represents the scope of the PowerShell console. So if a variable is defined in this scope, it will still exist even when the script that is defining it is no longer running. |

**Table 3.3:** Variable scopes and validity of variables

Script blocks represent scopes in which variables and functions can live. The PowerShell console is the basic scope (global scope). Each script launched from the console creates its own scope (script scope) unless the script is launched "dot-sourced." In this case, the script scope will merge with the caller's scope.

Functions again create their own scope and functions defined inside of other functions create additional sub-scopes.

Here is a little walk-through. Inside the console, all scopes are the same, so prefixing a variable will not make much difference:

$test = 1  
$local:test

1

$script:test = 12  
$global:test

12

$private:test

12

Differences become evident only once you create additional scopes, such as by defining a function:

  
Function test { "variable = $a"; $a = 1000 }

  
$a = 12  
Test

variable = 12

  
$a

12

When you don't use any special scope prefix, a child scope can read the variables of the parent scope, but not change them. If the child scope modifies a variable that was present in the parent scope, as in the example above, then the child scope actually creates a completely new variable in its own scope, and the parent scope's variable remains unchanged.

There are exceptions to this rule. If a parent scope declares a variable as "private," then it is accessible only in that scope and child scopes will not see the variable.

  
Function test { "variable = $a"; $a = 1000 }

  
**$private:a** = 12  
Test

variable =

  
$a

12

Only when you create a completely new variable by using _$private:_ is it in fact private. If the variable already existed, PowerShell will not reset the scope. To change scope of an existing variable, you will need to first remove it and then recreate it: _Remove-Variable_ a would remove the variable $a. Or, you can manually change the variable options: _(Get-Variable a).Options = "Private."_ You can change a variable scope back to the initial default "local" by assigning _(Get-Variable a).Options = "None."_

## Variable Types and "Strongly Typing"

Variables by default are not restricted to a specific data type. Instead, when you store data in a variable, PowerShell will automatically pick a suitable data type for you. To find out what data types really are, you can explore data types. Call the method _GetType()_. It will tell you the data type PowerShell has picked to represent the data:

(12).GetType().Name

Int32

(1000000000000).GetType().Name

Int64

(12.5).GetType().Name

Double

(12d).GetType().Name

Decimal

("H").GetType().Name

String

(Get-Date).GetType().Name

DateTime

PowerShell will by default use primitive data types to store information. If a number is too large for a 32-bit integer, it switches to 64-bit integer. If it's a decimal number, then the _Double_ data type best represents the data. For text information, PowerShell uses the _String_ data type. Date and time values are stored in _DateTime_ objects.

This process of automatic selection is called "weak typing," and while easy, it's also often restrictive or risky. Weakly typed variables will happily accept anything, even wrong pieces of information. You can guarantee that the variable gets the information you expected by strongly typing a variable — or else will throw an exception that can alarm you.

Also, PowerShell will not always pick the best data type. Whenever you specify text, PowerShell will stick to the generic string type. If the text you specified was really a date or an IP address, then there are better data types that will much better represent dates or IP addresses.

So, in practice, there are two important reasons for you to choose the data type yourself:

* **Type safety:** If you have assigned a type to a variable yourself, then the type will be preserved no matter what and will never be automatically changed to another data type. You can be _absolutely_ sure that a value of the correct type is stored in the variable. If someone later on wants to mistakenly assign a value to the variable that doesn't match the originally chosen type, this will cause an exception.
* **Special variable types:** When automatically assigning a variable type, PowerShell will choose from generic variable types like _Int32_ or _String_. Often, it's much better to store values in a specialized and more meaningful variable type like _DateTime_.

### Strongly Typing

You can enclose the type name in square brackets before the variable name to assign a particular type to a variable. For example, if you know that a particular variable will hold only numbers in the range 0 to 255, you can use the _Byte_ type:

[Byte]$flag = 12  
$flag.GetType().Name

Byte

The variable will now store your contents in a single byte, which is not only very memory-efficient, but it will also raise an error if a value outside the range is specified:

$flag = 300

The value "300" cannot be converted to the type "System.Byte".  
Error: "The value for an unsigned byte was too large or too small."  
At line:1 char:6  
\+ $flag <<<< = 300

### The Advantages of Specialized Types

If you store a date as _String_, you'll have no access to special date functions. Only _DateTime_ objects offer all kinds of methods to deal with date and time information. So, if you're working with date and time information, it's better to store it explicitly as _DateTime_:

$date = "November 12, 2004"  
$date

November 12, 2004

If you store a date as _String_, then you'll have no access to special date functions. Only _DateTime_ objects make them available. So, if you're working with date and time indicators, it's better to store them explicitly as _DateTime_:

[datetime]$date = "November 12, 2004"  
$date

Friday, November 12, 2004 00:00:00

Now, since the variable converted the text information into a specific _DateTime_ object, it tells you the day of the week and also enables specific date and time methods. For example, a _DateTime_ object can easily add and subtract days from a given date. This will get you the date 60 days from the date you specified:

$date.AddDays(60)

Tuesday, January 11, 2005 00:00:00

PowerShell supports all.NET data types. XML documents will be much better represented using the _XML_ data type then the standard _String_ data type:

  
$t = "" +  
""  
$t

  


  
[xml]$list = $t  
$list.servers

server  
\------  
{PC1, PC2}

$list.servers.server

name ip  
\---- --  
PC1  10.10.10.10  
PC2  10.10.10.12

  
$list.servers.server[0].ip = "10.10.10.11"  
$list.servers

name ip  
\---- --  
PC1  10.10.10.11  
PC2  10.10.10.12

  
$list.get_InnerXML()

  


| ----- |
| **Variable type** |  **Description** |  **Example** |
| [array] |  An array |   |
| [bool] |  Yes-no value |  [boolean]$flag = $true |
| [byte] |  Unsigned 8-bit integer, 0...255 |  [byte]$value = 12 |
| [char] |  Individual unicode character |  [char]$a = "t" |
| [datetime] |  Date and time indications |  [datetime]$date = "12.Nov 2004 12:30" |
| [decimal] |  Decimal number |  [decimal]$a = 12  
$a = 12d |
| [double] |  Double-precision floating point decimal |  $amount = 12.45 |
| [guid] |  Globally unambiguous 32-byte identification number |  [guid]$id = [System.Guid]::NewGuid()  
$id.toString() |
| [hashtable] |  Hash table |   |
| [int16] |  16-bit integer with characters |  [int16]$value = 1000 |
| [int32], [int] |  32-bit integers with characters |  [int32]$value = 5000 |
| [int64], [long] |  64-bit integers with characters |  [int64]$value = 4GB |
| [nullable] |  Widens another data type to include the ability to contain null values. It can be used, among others, to implement optional parameters |  [Nullable``1[[System.DateTime]]]$test = Get-Date  
$test = $null |
| [psobject] |  PowerShell object |   |
| [regex] |  Regular expression |  $text = "Hello World"  
[regex]::split($text, "lo") |
| [sbyte] |  8-bit integers with characters |  [sbyte]$value = -12 |
| [scriptblock] |  PowerShell scriptblock |   |
| [single], [float] |  Single-precision floating point number |  [single]$amount = 44.67 |
| [string] |  String |  [string]$text = "Hello" |
| [switch] |  PowerShell switch parameter |   |
| [timespan] |  Time interval |  [timespan]$t = New-TimeSpan $(Get-Date) "1.Sep 07" |
| [type] |  Type |   |
| [uint16] |  Unsigned 16-bit integer |  [uint16]$value = 1000 |
| [uint32] |  Unsigned 32-bit integer |  [uint32]$value = 5000 |
| [uint64] |  Unsigned 64-bit integer |  [uint64]$value = 4GB |
| [xml] |  XML document |   |

**Table 3.5:** Commonly used .NET data types

## Variable Management: Behind the Scenes

Whenever you create a new variable in PowerShell, it is stored in a _PSVariable_ object. This object contains not just the value of the variable, but also other information, such as the description that you assigned to the variable or additional options like write-protection.

If you retrieve a variable in PowerShell, PowerShell will return only the variable value. If you'd like to see the remaining information that was assigned to the variable, you'll need the underlying _PSVariable_ object. _Get-Variable_ will get it for you:

$testvariable = "Hello"  
$psvariable = Get-Variable testvariable

You can now display all the information about _$testvariable_ by outputting _$psvariable_. Pipe the output to the cmdlet _Select-Object_ to see all object properties and not just the default properties:

$psvariable | Select-Object

Name : testvariable  
Description :  
Value : Hello  
Options : None  
Attributes : {}

* **Description:** The description you specified for the variable.
* **Value:** The value assigned currently to the variable (i.e. its contents).
* **Options:** Options that have been set, such as write-protection or _AllScope_.
* **Attributes:** Additional features, such as permitted data type of a variable for strongly typed variables. The brackets behind _Attributes_ indicate that this is an array, which can consist of several values that can be combined with each other.

### Modification of Variables Options

One reason for dealing with the _PSVariable_ object of a variable is to modify the variable's settings. Use either the cmdlet _Set-Variable_ or directly modify the _PSVariable_ object. For example, if you'd like to change the description of a variable, you can get the appropriate _PSVariable_ object and modify its _Description_ property:

  
$test = "New variable"

  
$psvariable = Get-Variable test

  
$psvariable.Description = "Subsequently added description"  
Dir variable:test | Format-Table name, description

Name Description  
\---- -----------  
test Subsequently added description

  
(Get-Variable test).Description =  
"An additional modification of the description."  
Dir variable:test | Format-Table name, description

Name Description  
\---- -----------  
test An additional modification of the description.

  
Set-Variable test -description "Another modification"  
Dir variable:test | Format-Table name, description

Name Description  
\---- -----------  
test Another modification

As you can see in the example above, you do not need to store the _PSVariable_ object in its own variable to access its _Description_ property. Instead, you can use a sub-expression, i.e. a statement in parentheses. PowerShell will then evaluate the contents of the sub-expression separately. The expression directly returns the required _PSVariable_ object so you can then call the _Description_ property directly from the result of the sub-expression. You could have done the same thing by using _Set-Variable_. Reading the settings works only with the _PSVariable_ object:

(Get-Variable test).Description

An additional modification of the description.

### Write-Protecting Variables

For example, you can add the ReadOnly option to a variable if you'd like to write-protect it:

$Example = 10

  
(Get-Variable Example).Options = "ReadOnly"

  
Set-Variable Example -option "None" -force

  
$Example = 20

The _Constant_ option must be set when a variable is created because you may not convert an existing variable into a constant.

  
$constant = 12345  
(Get-Variable constant).Options = "Constant"

Exception in setting "Options": "The existing variable "constant"  
may not be set as a constant. Variables may only be set as  
constants when they are created."  
At line:1 char:26  
\+ (Get-Variable constant).O <<<< options = "Constant"

| ----- |
| **Option** |  **Description** |
| _"None"_ |  NO option (default) |
| _"ReadOnly"_ |  Variable contents may only be modified by means of the _-force_ parameter |
| _"Constant"_ |  Variable contents can't be modified at all. This option must already be specified when the variable is created. Once specified this option cannot be changed. |
| _"Private"_ |  The variable is visible only in a particular context (local variable). |
| _"AllScope"_ |  The variable is automatically copied in a new variable scope. |

**Table 3.6:** Options of a PowerShell variable

### Examining Strongly Typed Variables

Once you assign a specific data type to a variable as shown above, PowerShell will add this information to the variable attributes. .

If you delete the _Attributes_ property, the variable will be unspecific again so in essence you remove the strong type again:

  
(Get-Variable a).Attributes

TypeId  
\------  
System.Management.Automation.ArgumentTypeConverterAttribute

  
(Get-Variable a).Attributes.Clear()

  
$a = "Test"

### Validating Variable Contents

The _Attributes_ property of a _PSVariable_ object can include additional conditions, such as the maximum length of a variable. In the following example, a valid length from two to eight characters is assigned to a variable. An error will be generated if you try to store text that is shorter than two characters or longer than eight characters:

$a = "Hello"  
$aa = Get-Variable a  
$aa.Attributes.Add($(New-Object `  
System.Management.Automation.ValidateLengthAttribute `  
-argumentList 2,8))  
$a = "Permitted"  
$a = "This is prohibited because its length is not from 2 to 8 characters"

Because of an invalid value verification (Prohibited because  
its length is not from 2 to 8 characters) may not be carried out for  
the variable "a".  
At line:1 char:3  
\+ $a <<<< = "Prohibited because its length is not from 2 to 8

In the above example _Add()_ method added a new .NET object to the _attributes_ with _New-Object_. You'll learn more about _New-Object_ in [Chapter 6][2]. Along with _ValidateLengthAttribute_, there are additional restrictions that you can place on variables.

| ----- |
| **Restriction** |  **Category** |
| Variable may not be zero |  _ValidateNotNullAttribute_ |
| Variable may not be zero or empty |  _ValidateNotNullOrEmptyAttribute_ |
| Variable must match a Regular Expression |  _ValidatePatternAttribute_ |
| Variable must match a particular number range |  _ValidateRangeAttribute_ |
| Variable may have only a particular set value |  _ValidateSetAttribute_ |

**Table 3.7:** Available variable validation classes

In the following example, the variable must contain a valid e-mail address or all values not matching an e-mail address will generate an error. The e-mail address is defined by what is called a Regular Expression. You'll learn more about Regular Expressions in [Chapter 13][3].

$email = "tobias.weltner@powershell.com"  
$v = Get-Variable email  
$pattern = "b[A-Z0-9._%+-]+@[A-Z0-9.-]+.[A-Z]{2,4}b"  
$v.Attributes.Add($(New-Object `  
System.Management.Automation.ValidatePatternAttribute `  
-argumentList $pattern))  
$email = "valid@email.de"  
$email = "invalid@email"

Because of an invalid value verification (invalid@email) may not  
be carried out for the variable "email".  
At line:1 char:7  

```powershell
\+ $email <<<< = "invalid@email"
```

If you want to assign a set number range to a variable, use _ValidateRangeAttribute_. The variable _$age_ accepts only numbers from 5 to 100:

```powershell
$age = 18  
$v = Get-Variable age  
$v.Attributes.Add($(New-Object `  
System.Management.Automation.ValidateRangeAttribute `  
-argumentList 5,100))  
$age = 30  
$age = 110
```

Because of an invalid value verification (110) may not be  
carried out for the variable "age".  
At line:1 char:7  

```powershell
\+ $age <<<< = 110
```

If you would like to limit a variable to special key values, `_ValidateSetAttribute_` is the right option. The variable `_$option_` accepts only the contents `_yes_, _no_, or _perhaps_`:

```powershell
$option = "yes"  
$v = Get-Variable option  
$v.Attributes.Add($(New-Object `  
System.Management.Automation.ValidateSetAttribute `  
-argumentList "yes", "no", "perhaps"))  
$option = "no"  
$option = "perhaps"  
$option = "don't know"
```

Verification cannot be performed because of an invalid value  
(don't know) for the variable "option".  

```powershell
At line:1 char:8  
\+ $option <<<< = "don't know"
```


## Summary

Variables store information. Variables are by default not bound to a specific data type, and once you assign a value to a variable, PowerShell will automatically pick a suitable data type. By strongly-typing variables, you can restrict a variable to a specific data type of your choice. You strongly-type a variable by specifying the data type before the variable name:

```poweshell
[Int]$a = 1
```

You can prefix the variable name with "$" to access a variable. The variable name can consist of numbers, characters, and special characters, such as the underline character "_". Variables are not case-sensitive. If you'd like to use characters in variable names with special meaning to PowerShell (like parenthesis), the variable name must be enclosed in brackets. PowerShell doesn't require that variables be specifically created or declared before use.

There are pre-defined variables that PowerShell will create automatically. They are called "automatic variables." These variables tell you information about the PowerShell configuration. For example, beginning with PowerShell 2.0, the variable $psversiontable will dump the current PowerShell version and versions of its dependencies:

```powershell
PS > $PSVersionTable

Name                      Value  
\----                      -----  
CLRVersion                2.0.50727.4952  
BuildVersion              6.1.7600.16385  
PSVersion                 2.0  
WSManStackVersion         2.0  
PSCompatibleVersions      {1.0, 2.0}  
SerializationVersion      1.1.0.1  
PSRemotingProtocolVersion 2.1
```

You can change the way PowerShell behaves by changing automatic variables. For example, by default PowerShell stores only the last 64 commands you ran (which you can list with _Get-History_ or re-run with _Invoke-History_). To make PowerShell remember more, just adjust the variable _$MaximumHistoryCount_:

```powershell
PS > $MaximumHistoryCount

64

PS > $MaximumHistoryCount = 1000

PS > $MaximumHistoryCount

1000
```

PowerShell will store variables internally in a PSVariable object. It contains settings that write-protect a variable or attach a description to it ([Table 3.6][4]). It's easiest for you to set this special variable options by using the _New-Variable_ or _Set-Variable_ cmdlets ([Table 3.1][1]).

Every variable is created in a scope. When PowerShell starts, an initial variable scope is created, and every script and every function will create their own scope. By default, PowerShell accesses the variable in the current scope, but you can specify other scopes by adding a prefix to the variable name: _local:_, _private:_, _script:_, and _global:_.

* * *

[1]: http://powershell.com/cs/blogs/ebookv2/archive/2012/02/02/chapter-3-variables.aspx#table3.1
[2]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/05/chapter-6-working-with-objects.aspx
[3]: http://powershell.com/cs/blogs/ebookv2/archive/2012/03/06/chapter-13-text-and-regular-expressions.aspx
[4]: http://powershell.com/cs/blogs/ebookv2/archive/2012/02/02/chapter-3-variables.aspx#table3.6
