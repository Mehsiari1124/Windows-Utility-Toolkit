@echo off
setlocal EnableExtensions
title Windows Utility Toolkit
color 0A
mode con: cols=78 lines=32

:MENU
cls
echo.
echo ==============================================================================
echo                         WINDOWS UTILITY TOOLKIT
echo ==============================================================================
echo.
echo    [1] Open Windows Activation Settings
echo    [2] Check Windows Activation Status
echo    [3] Get Windows Installation ID
echo    [4] Open Windows Update
echo    [5] Open System Settings
echo    [6] About Windows
echo    [7] BIOS Information
echo    [8] Download Windows 11 ISO
echo    [9] Office Cscript
echo    [A] Open Smart App Control
echo    [0] Exit
echo.
echo ==============================================================================
echo.

choice /c 123456789A0 /n /m "Select an option: "

if errorlevel 11 goto EXIT
if errorlevel 10 goto SMARTAPP
if errorlevel 9 goto OFFICECSCRIPT
if errorlevel 8 goto WIN11ISO
if errorlevel 7 goto BIOS
if errorlevel 6 goto ABOUT
if errorlevel 5 goto SYSTEM
if errorlevel 4 goto UPDATE
if errorlevel 3 goto INSTALLATIONID
if errorlevel 2 goto STATUS
if errorlevel 1 goto ACTIVATION


:ACTIVATION
cls
echo.
echo ==============================================================================
echo                         WINDOWS ACTIVATION SETTINGS
echo ==============================================================================
echo.
echo Opening Windows Activation Settings...
echo.

start "" "ms-settings:activation"

timeout /t 2 /nobreak >nul
goto MENU


:STATUS
cls
echo.
echo ==============================================================================
echo                         WINDOWS ACTIVATION STATUS
echo ==============================================================================
echo.

cscript.exe //nologo "%windir%\system32\slmgr.vbs" /xpr

echo.
echo ==============================================================================
echo.
pause
goto MENU


:INSTALLATIONID
cls
echo.
echo ==============================================================================
echo                          WINDOWS INSTALLATION ID
echo ==============================================================================
echo.
echo Retrieving Installation ID...
echo.

cscript.exe //nologo "%windir%\system32\slmgr.vbs" /dti

echo.
echo ==============================================================================
echo.
pause
goto MENU


:UPDATE
cls
echo.
echo ==============================================================================
echo                             WINDOWS UPDATE
echo ==============================================================================
echo.
echo Opening Windows Update...
echo.

start "" "ms-settings:windowsupdate"

timeout /t 2 /nobreak >nul
goto MENU


:SYSTEM
cls
echo.
echo ==============================================================================
echo                            SYSTEM SETTINGS
echo ==============================================================================
echo.
echo Opening System Settings...
echo.

start "" "ms-settings:system"

timeout /t 2 /nobreak >nul
goto MENU


:ABOUT
cls
echo.
echo ==============================================================================
echo                             ABOUT WINDOWS
echo ==============================================================================
echo.
echo Opening About Windows...
echo.

start "" "ms-settings:about"

timeout /t 2 /nobreak >nul
goto MENU


:BIOS
cls
echo.
echo ==============================================================================
echo                            BIOS INFORMATION
echo ==============================================================================
echo.
echo Retrieving BIOS information...
echo.

powershell.exe -NoProfile -ExecutionPolicy Bypass -Command ^
"$bios = Get-CimInstance Win32_BIOS; ^
Write-Host ('Manufacturer      : ' + $bios.Manufacturer); ^
Write-Host ('BIOS Version      : ' + $bios.SMBIOSBIOSVersion); ^
Write-Host ('BIOS Release Date : ' + $bios.ReleaseDate); ^
Write-Host ('Serial Number     : ' + $bios.SerialNumber); ^
Write-Host ('SMBIOS Version    : ' + $bios.SMBIOSMajorVersion + '.' + $bios.SMBIOSMinorVersion)"

echo.
echo ==============================================================================
echo.
pause
goto MENU


:WIN11ISO
cls
echo.
echo ==============================================================================
echo                         WINDOWS 11 ISO DOWNLOAD
echo ==============================================================================
echo.
echo Detecting Windows system language...
echo.

for /f "delims=" %%A in ('
    powershell.exe -NoProfile -Command "(Get-WinSystemLocale).Name"
') do set "LANG=%%A"

if not defined LANG set "LANG=en-US"

echo Detected Windows locale: %LANG%
echo.
echo Opening Microsoft's official Windows 11 download page...
echo.
echo NOTE:
echo Microsoft generates temporary ISO download links.
echo The language must be confirmed on Microsoft's website.
echo.

start "" "https://www.microsoft.com/en-us/software-download/windows11"

echo.
echo ==============================================================================
echo.
pause
goto MENU


:OFFICECSCRIPT
cls
echo.
echo ==============================================================================
echo                          MICROSOFT OFFICE CSCRIPT
echo ==============================================================================
echo.
echo Searching for Office licensing script...
echo.

set "OSPP="

REM Microsoft 365 / Click-to-Run - 64-bit
if exist "%ProgramFiles%\Microsoft Office\root\Office16\ospp.vbs" (
    set "OSPP=%ProgramFiles%\Microsoft Office\root\Office16\ospp.vbs"
)

REM Microsoft 365 / Click-to-Run - 32-bit Office on 64-bit Windows
if exist "%ProgramFiles(x86)%\Microsoft Office\root\Office16\ospp.vbs" (
    set "OSPP=%ProgramFiles(x86)%\Microsoft Office\root\Office16\ospp.vbs"
)

REM Older / volume Office installation - 64-bit
if exist "%ProgramFiles%\Microsoft Office\Office16\ospp.vbs" (
    set "OSPP=%ProgramFiles%\Microsoft Office\Office16\ospp.vbs"
)

REM Older / volume Office installation - 32-bit
if exist "%ProgramFiles(x86)%\Microsoft Office\Office16\ospp.vbs" (
    set "OSPP=%ProgramFiles(x86)%\Microsoft Office\Office16\ospp.vbs"
)

if not defined OSPP (
    echo.
    echo ERROR: ospp.vbs was not found.
    echo.
    echo This can happen with Microsoft 365 installations where
    echo the licensing script is not available in the expected path.
    echo.
    pause
    goto MENU
)

echo Office licensing script found:
echo %OSPP%
echo.
echo Running Office license status...
echo.

cscript.exe //nologo "%OSPP%" /dstatus

echo.
echo ==============================================================================
echo.
pause
goto MENU


:SMARTAPP
cls
echo.
echo ==============================================================================
echo                           SMART APP CONTROL
echo ==============================================================================
echo.
echo Opening Smart App Control...
echo.

start "" "windowsdefender://SmartAppControl"

timeout /t 2 /nobreak >nul
goto MENU


:EXIT
cls
echo.
echo ==============================================================================
echo                         WINDOWS UTILITY TOOLKIT
echo ==============================================================================
echo.
echo Exiting...
echo.

timeout /t 1 /nobreak >nul
endlocal
exit

