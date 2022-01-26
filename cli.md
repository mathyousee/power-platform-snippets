# Power Platform CLI

The CLI is not a typical Citizen Developer topic, nor do they have a direct need for it. That said, I am right on the line of Citizen Dev and Pro Dev (Pro Dev Light? Deputized Citizen Dev?) and have found ways to make the Power Platform CLI useful for my builds.

Typically, I use this to grab point-in-time updates of the configuration I do via the Power Apps maker portal, so they can be part of a larger process and provide better visibility for if/when IT wants to take over (or at least have more oversight of what I'm doing).

FWIW I keep most of these in a PowerShell file in VSCode and run them from the integrated console. There are definitely other ways to do it but that's what works for me (as a basic PowerShell / CLI user). In case you want that file, let me know via a GitHub issue and I'll upload it ;) 

## Install Power Platform CLI and update to latest version

I installed from VS Code by going to the Extensions marketplace and installing **Power Platform Tools** (the name has changed from the docs below) from the Microsoft publisher.

After installing, I still needed to run the following command to get the latest and greatest

```
pac install latest
```

Official documentation: https://docs.microsoft.com/en-us/powerapps/developer/data-platform/powerapps-cli#install-microsoft-power-platform-cli

##  Authenticate and connect to Dataverse organization

This will get you started by connecting to a specific Environment. An Office 365 login window will be displayed, which allows login with a username/password and two-factor if needed. 


Format: 

`pac auth create --kind DATAVERSE --url https://url  --name displayname`

Here's an example (not a real URL):

```
pac auth create --kind DATAVERSE --url https://org12345.crm.dynamics.com/ --name FancyEnvName
```

The environment will be added to your to your list of connection profiles, which you can view with the command

```
pac auth list
```

this will return something like this:

```
Profiles (* indicates active):
   [1] * ADMIN                                    https://service.powerapps.com/           : matt@example.org                      Public
   [2] * DATAVERSE AnchorheadDev                  https://orge1111111.crm.dynamics.com/    : matt@example.org                      Public
```

In the above example, the number in brackets `[1]` is the *ID* for the profile.

If you have more than one connection profile, you can use this to validate the environment before you interact with the API

```
pac org who
```

Which will return something like this (though these are fake IDs and URLs):

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

To change to another connection profile, use the `pac auth list` command to view your list, then change by using:

```
pac auth select --index 1
```

The above example will change to the connection profile with the ID of 1

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

## Export solution and unpack so it is usable in source control

The .zip file format of the solution is useful for citizen developers to import packages to other enviornments, however from a source control perspective it's more valuable to unpack that .zip file so individual changes can be reviewed/understood later. CLI offers us this capability. I'll start with my script, then describe the different steps.

``` powershell

$srcDir = "C:\mypath\"
$solutionName = "myFancySolution"

pac solution export --path ${srcDir}tmp\${solutionName}.zip --name $solutionName --managed false
pac solution unpack --zipfile ${srcDir}tmp\${solutionName}.zip --folder ${srcDir}\${solutionName} --allowWrite true

Get-ChildItem -Path ${srcDir}${solutionName}\CanvasApps\ -Filter *.msapp |

Foreach-Object {

    $msappFile = $_.Name
    pac canvas unpack --msapp ${srcDir}${solutionName}\CanvasApps\${msappFile} --sources ${srcDir}\${solutionName}-UnpackedCanvas\$msappFile


}

Remove-Item -path ${srcDir}tmp\${solutionName}.zip

```

Before running, I set the srcDir and solutionName variables based on what I'm going to export.

The first chunk exports the solution, then unpacks the contents of the .zip to a /source sub-folder named after the solution.

```powershell
pac solution export --path ${srcDir}tmp\${solutionName}.zip --name $solutionName --managed false
pac solution unpack --zipfile ${srcDir}tmp\${solutionName}.zip --folder ${srcDir}\${solutionName} --allowWrite true
```

This next bit looks for any canvas apps or canvas pages (.msapp) and unpacks those to the /source folder, with each component having its own subfolder.

```powershell
Get-ChildItem -Path ${srcDir}${solutionName}\CanvasApps\ -Filter *.msapp |

Foreach-Object {

    $msappFile = $_.Name
    pac canvas unpack --msapp ${srcDir}${solutionName}\CanvasApps\${msappFile} --sources ${srcDir}\${solutionName}-UnpackedCanvas\$msappFile


}
```

Then I remove the .zip file that I unpacked (it's nice to tidy up after yourself).

In the future, to take advantage of change tracking, it's necessary to delete the items in the source folder before unpacking/overwriting. For this, I use the following:

```powershell
$srcDir = "C:\mypath\"
$solutionName = "myFancySolution"

pac solution export --path ${srcDir}tmp\${solutionName}.zip --name $solutionName --managed false
Remove-Item -path ${srcDir}\${solutionName} -Recurse
pac solution unpack --zipfile ${srcDir}tmp\${solutionName}.zip --folder ${srcDir}\${solutionName} --allowWrite true
Remove-Item -path ${srcDir}tmp\${solutionName}.zip

Get-ChildItem -Path ${srcDir}${solutionName}\CanvasApps\ -Filter *.msapp |

Foreach-Object {

    $msappFile = $_.Name
    Remove-Item -path ${srcDir}${solutionName}-UnpackedCanvas\${msappFile} -Recurse
    pac canvas unpack --msapp ${srcDir}${solutionName}\CanvasApps\${msappFile} --sources ${srcDir}\${solutionName}-UnpackedCanvas\$msappFile


}
```

Ideally this script should be enhanced to remove any canvas source. I have an idea for this but haven't had the time to implement/test it, but when I do you can expect an update.

## Links

[Power Plaform CLI Documentation](https://docs.microsoft.com/en-us/powerapps/developer/data-platform/powerapps-cli)
