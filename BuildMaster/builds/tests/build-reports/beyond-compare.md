---
title: Generating a Diff Report with Beyond Compare
sequence: 200
keywords: buildmaster, build-reports, beyond-compare
---

### Pre-requisites: 
 - Beyond Compare command line utility installed

[Beyond Compare](https://www.scootersoftware.com/) is a multi-platform utility that combines directory compare and file compare functions in one package. While typically used as a GUI tool, it also ships with a [command line utility](https://www.scootersoftware.com/v4help/index.html?command_line_reference.html) that can be used to generate diff reports from an automated system like BuildMaster.

Example plan:

```
# Run Beyond Compare and capture output into a build report
{
    # set variables here for example simplicity

    set $PrevDirectory = C:\tmp\reports\prev; 
    set $CurrDirectory = C:\tmp\reports\curr;

    set $ScriptPath = $PathCombine($WorkingDirectory, script.txt);
    set $ReportPath = $PathCombine($WorkingDirectory, report.html);

    Create-File $ScriptPath
    (
        Text: >>load "%1" "%2"
expand all
folder-report layout:side-by-side &
options:display-all &
output-to:%3 output-options:html-color>>
    );

    Exec '"C:\Program Files (x86)\Beyond Compare 3\BCompare.exe" "`@$ScriptPath" "$PrevDirectory" "$CurrDirectory" "$ReportPath" /silent';

    Capture-FileReport $ReportPath
    (
        Name: Beyond Compare Report
    );
}
```

This generates the following output when viewed on the BuildMaster build overview page:

![](/resources/documentation/buildmaster/6/report-example.png)