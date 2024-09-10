# Creating and Troubleshooting Custom R Project Files from the Terminal

This tutorial provides step-by-step instructions on how to create custom R project files (`*.Rproj`) using PowerShell and how to troubleshoot common issues that might arise when trying to open these files in RStudio.

By the end of this tutorial, you should be able to type `Create-RProject -projectName "MyNewProject"` into the terminal and voila. A new project created there and then


## Table of Contents
- [Introduction](#introduction)
- [Create a Template for your `.Rproj`](#create-a-template-for-your-rproj)
- [Add Rstudio Path to the System Environment Variable](#add-rstudio-path-to-the-system-environment-variable)
- [Creating a Custom `.Rproj` File](#creating-a-custom-rproj-file)
- [Troubleshooting Common Issues](#troubleshooting-common-issues)
- [Skills Learned](#skills-learned)

## Introduction

The conveniance of summoning a VScode project from the commandline with a simple `. code` inspired me to create this function.

RStudio uses `.Rproj` files to manage project settings and workspace configurations.
They are a great alternative to setting up a new work directory in scripts with gross functions like `setwd()` and dealing with lengthy, mistake-prone path names.

## Create a Template for your `.Rproj`

R project files contain a small amount of neccesary metadata that is accessed by Rstudio.
This contains things like the version it was created in, how to save the workspace, updating, encoding etc.
You can open any `.Rproj` file in notepad to see the information included in your existing projects by right clicking and selecting 'Edit in notepad'.

For PowerShell to create a working `Rproj` file you need to supply this metadata as a template.

1. Create a folder to store the template in. E.g., "C:\templates\"
2. In this folder, save a .Rproj file with the desired metadata. Save as "template.Rproj" (or whatever suits you)
```txt
Version: 1.0
RestoreWorkspace: Default
SaveWorkspace: Default
AlwaysSaveHistory: Default
EnableCodeIndexing: Yes
UseSpacesForTab: Yes
NumSpacesForTab: 2
Encoding: UTF-8
RnwWeave: Sweave
LaTeX: pdfLaTeX
```
## Add Rstudio Path to the System Environment Variable
To allow your shell to understand what Rstudio even is. You'll need to add a path to the program file in your system settings. This can be done in PowerShell, but can be a bit hectic, so let's do it the old fashioned way.
### Step 1: Locate the RStudio Executable
- Navigate to the directory where RStudio is installed, which is often `C:\Program Files\RStudio` or `C:\Program Files (x86)\RStudio`.
- Look for `rstudio.exe` in this directory. This is likely on the Rstudio directory but could also be in a `bin` directory.
- Click on the file and press `Ctrl+Shift+C` to copy the path.

### Step 2: Add the path to System Environment Variable Via System Properties:
- Open the Start Search, type System Properties and select “Edit the system environment variables” or "Advanced system settings".
- In the System Properties window, click on the “Environment Variables” button.
- In the Environment Variables window, scroll down in the “System variables” section and select the PatH variable, then click on “Edit…”.
- In the Edit Environment Variable window, click “New” and paste the path to your RStudio’s directory, e.g., `C:\Program Files\RStudio`.
- Click OK on all dialogs to close them and apply your changes.

### Step 3: Verify the Path Addition
Open a new PowerShell window (after closing any existing ones) and type `rstudio` to see if it launches RStudio. This will confirm that the path has been correctly added and recognized.

## Creating a Custom `.Rproj` File

### Step 1: Set Up Your PowerShell Profile

Add a function to your PowerShell profile to automate the creation of `.Rproj` files with predefined settings.

1. Open PowerShell and type the following command to edit your profile:
   ```powerShell
   notepad $PROFILE
   ```
2. In the .ps1 notepad that opens up (create a new one if you have to), use paste the following function so that it can be used at any time from the terminal:
    ```powerShell
    function Create-RProject {
    param (
        [string]$projectName,
        [string]$directoryPath = (Get-Location).Path
    )
    $templatePath = "C:\\Path\\To\\template.Rproj"
    $newProjectPath = Join-Path -Path $directoryPath -ChildPath "$projectName.Rproj"
    Copy-Item -Path $templatePath -Destination $newProjectPath
    Write-Host "Project $projectName created at $directoryPath"
    }
    ```
    *Note* the path to the template needs to be changed to the correct location. e.g., `"C:\templates\template.Rproj"`.

3. Save the file and reload your profile. You'll need set your execution policy to allow .ps1 files to be read *See troubleshooting*. To reload your profile, type `. $PROFILE` into the terminal.

### Step 2: Create `.Rproj` File Using PowerShell

To create a new project, use the newly created function from any directory. To open a terminal from any directory, right click in the empty space of a folder and click `Open in Terminal`.

Now you can use your fancy new function to create a new Rproject.

```powerShell
Create-RProject -projectName "MyNewProject"
```
The above example assumes you want to create the project within the directory of the terminal. The function we've created also allows you to create it at a specified location. e.g.:

```powerShell
Create-RProject -projectName "MyNewProject" -directoryPath "C:\MyProjects"
```
### Setup the Environment Path

### Step 3: Open the project from the terminal
Just type:
```powerShell
rstudio dataLocator.Rproj
```
Or whatever your project is called.

## Troubleshooting Common Issues

### Execution Policy
For PowerShell to run scripts, it is neccesary to allow this. Don't worry, this is totally safe. By following these steps, the only way someone could hurt your system is if you let the criminal create a script (physically) from your computer and then execute it from your computer. By that point, it's 100% your fault.

1. Open up PowerShell as administrator
   To do this, right click on the Windows icon and select `Terminal (Admin)`
2. Check the current execution policy by typing `Get-ExecutionPolicy` (use Tab to autocomplete).
3. **Not recommended:** To temporarily change it to unrestricted, type `Set-ExecutionPolicy Unrestricted` and then to change it back with, you guessed it, `Set-ExecutionPolicy Restricted`.
4. A better way (step 5), is to allow trusted authors only, i.e., Yourself and anyone (e.g., Microsoft) you also want to allow. The allow list is empty from the get go so don't worry about presupposed permission. Microsoft can't hurt you. To do this, we use a `RemoteSigned` execution policy. You can read about it [here](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.4).
5. Type the following into the Admin Terminal:
   ```powerShell
   Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
   ```
## Skills Learned
- **PowerShell Scripting**
- **System Environment Variables**
- **The anatomy of a `.Rproj`**
- **PowerShell Execution Policy**

   

