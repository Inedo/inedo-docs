---
title: "Python (.py) Scripting"
order: 6
---

As of v3.0.19, Otter can seamlessly integrate with Python, whether that means running your existing scripts across dozens of servers, leveraging scripts built by the community, or a custom combination of both.

## Prerequisites

The only prerequisite is to have Python3 installed on a server. Windows and Linux are both supported, and Otter will usually be able to detect its location automatically.

:::(Info) (NOTE:)
If Python could not be automatically located, you will have to create a server-scoped variable named `$PythonPath` in Otter that contains the full path to python (or python.exe on Windows).
:::

## Uploading Python Scripts to Otter

The easiest way to execute a Python script in Otter is to start by uploading an existing script. Just click the Add Script button on the Scripts page and select Upload. Your script will then be available to use either directly or from OtterScript using the `PYCall` operation.


## Executing Inline

If you just want to execute a short Python script, or if you want to use OtterScript variable replacement to build out your script, you can use the `PYExec` operation:

```OtterScript
PYExec >>print('Hello world from Otter!')>>;
```

## Drift Detection & Remediation

Use `PYVerify` to enable detecting invalid server configuration with a Python script.

### Example: Python Script to Verify that a File Exists

```python
'''
Verifies that a file exists.

AhParameters:
    filePath (text): full path of file to verify

AhConfigKey:
    $filePath

AhDesiredValue:
    True

AhCurrentValue:
    $fileExists

'''

import os;
import os.path;

print(f'Checking to see if {filePath} exists...')
fileExists = os.path.exists(filePath)
if(fileExists):
    print(f'{filePath} exists.')
else:
    print(f'{filePath} does not exist.')
```

You can use `PYEnsure` to perform verification as with `PYVerify` but also add the ability to correct any detected configuration problems.

### Example: Python Script to Ensure that a File Exists

```python
'''
Ensures that a file exists.

AhParameters:
    filePath (text): full path of the file to ensure

AhExecMode:
    execMode

AhConfigKey:
    $filePath

AhDesiredValue:
    True

AhCurrentValue:
    $fileExists

'''

import os;
import os.path;

if(execMode == 'Collect'):
    print(f'Collect mode; checking to see if {filePath} exists...')
    fileExists = os.path.exists(filePath)
    if(fileExists):
        print(f'{filePath} exists.')
    else:
        print(f'{filePath} does not exist.')
else:
    print(f'Creating {filePath}...')
    os.makedirs(os.path.dirname(filePath), exist_ok=True)
    with open(filePath, 'w') as f:
        f.write('hello dears')
    fileExists = True
    print(f'{filePath} created.')
```

## Technical Details

Otter uses the standard Python3 interpreter to execute your scripts. The script is executed using Python's `exec`, allowing it to access variables declared as inputs in the `AhParameters` section of the docstring, and Otter also hooks into Python's logging system, allowing `logging.info/warning/etc` calls in Python to be captured with the appropriate log levels in Otter. Output values are read after `exec` returns.
