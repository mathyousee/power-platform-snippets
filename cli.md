# Power Platform CLI

References I want to remember for later. 

FWIW I keep most of these in a PowerShell file in VSCode and run them from the integrated console. There are definitely other ways to do it but that's what works for me (as a basic PowerShell / CLI user). In case you want that file, I'm linking it here: 

## Install and update to latest version

I installed from VS Code by going to the Extensions marketplace and installing **Power Platform Tools** (the name has changed from the docs below) from the Microsoft publisher.

After installing, I still needed to run the following command to get the latest and greatest

```
pac install latest
```

Official documentation: https://docs.microsoft.com/en-us/powerapps/developer/data-platform/powerapps-cli#install-microsoft-power-platform-cli

##  Authenticate and connect to Dataverse organization

This will get you started by connecting to a specific Environment. It will also add the environment to your `auth list`

Format: 

`pac auth create --kind DATAVERSE --url https://url  --name displayname`

Here's an example (not a real URL):

```
pac auth create --kind DATAVERSE --url https://org12345.crm.dynamics.com/ --name FancyEnvName
```

## Confirm the environment what I'm currently connected to

Use this to validate the environment before you interact with the API

```
pac org who
```

Which will return something like (though these are fake IDs and URLs):

```
Connected to...Development
Organization Information
  Org ID:                     741af709-54fc-4470-887b-2cd73552f83a
  Unique Name:                unq12345243wsdfgwq23tg22v2v2v2
  Friendly Name:              Development
  Org URL:                    https://org11111.crm.dynamics.com/
  User ID:                    matt@example.com (f123456c-311c-ec11-b3e4-001d3a9d24n4)
  Environment ID:             11f8c533-c9ee-4e61-be7b-d04c3126b1c2
```

## Clone a solution definition to the current folder

This will drop the solution components into a subfolder structure which can be used to track metadata changes to Dataverse, Model-Driven Power Apps, and other "solution aware" components in source control systems.

```
pac solution clone --name MyFancySolution
```

## Export a solution from the current environment

I use this to pull Solution file packages (.zip) from Dev environments.

Unmanaged solution:

```
pac solution export --path c:\mypath\solutionName.zip --name solutionName
```

Managed solution:

```
pac solution export --path c:\mypath\solutionName_managed.zip --name solutionName --managed true
```

Note: This will only work for Unmanaged solutions, since Managed solutions are protected from direct export.

## Export Managed and Unmanaged solutions at the current point in time

As I hit key milestones in my builds, I like to export a physical copy of both my managed and unmanaged solutions. To do so I use the following PowerShell script to get it all in one fell swoop. Before running the script, I enter the variable values, then I execute the script.


``` powershell
# Set these before running the script, I put the solution files in the solution folder

$solutionDir = "c:\mypath\solutions\"
$solutionVersion = "1.0.0.0"
$solutionName = "myFancySolution"

# The following commands execute the export using the variables above

pac solution export --path ${solutionDir}${solutionName}-${solutionVersion}.zip --name $solutionName --managed false
pac solution export --path ${solutionDir}${solutionName}-${solutionVersion}_managed.zip --name $solutionName --managed true
```

Regarding the `version` variable: All of the version validation is stored inside of the package...this is just a label.

 I'm sure there's a more elegant way to set the version number based on the solution version in Dataverse...I'm welcome to that feedback via a GitHub issue or Pull Request :) 

## Links

[Power Plaform CLI Documentation](https://docs.microsoft.com/en-us/powerapps/developer/data-platform/powerapps-cli)
