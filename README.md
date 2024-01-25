@echo off
setlocal enabledelayedexpansion

set "folderPath=C:\path\to\your\folder"
set "specifiedString=your_specified_string"
set "replacementA=A"
set "replacementB=B"
set "tempFile=%temp%\tempfile.txt"

for /r "%folderPath%" %%F in (*) do (
    set "foundString="
    set /A count=0

    for /F "usebackq tokens=*" %%L in ("%%F") do (
        set /A count+=1

        if defined foundString (
            rem Check for another specified string within the last 5 lines
            if !count! geq 6 (
                set /A linesBack=count-6
                for /L %%I in (!count! -1 !linesBack!) do (
                    echo %%L | find "!specifiedString2!" >nul
                    if not errorlevel 1 (
                        set "foundString=!replacementA!"
                        goto :replaceContent
                    )
                )
            )
            goto :replaceContent
        )

        echo %%L | find "!specifiedString!" >nul
        if not errorlevel 1 (
            set "foundString=!replacementB!"
        )
    )

    :replaceContent
    if defined foundString (
        echo !foundString! > "!tempFile!"
        type "%%F" >> "!tempFile!"
        move /Y "!tempFile!" "%%F" >nul
    )
)

del /Q "%tempFile%"
endlocal
