# Installing Immunity Debugger

Installing [Immunity Debugger](https://www.immunityinc.com/products/debugger/) requires the 32bit MSI installer 
for Python 2.7.18.

For using `mona`, copy the [mona.py file](https://github.com/Jtw0/Immunity-Debugger-Scripts) into the PyCommands folder 
in the Immunity install.

## Configuration

To configure mona (bottom of mona):

    !mona config -set workingfolder c:\mona\%p

In Immunity, go to `Options -> Preferences -> Events`, and un-tick everything under `Break on`. Now a program will 
only break when crashing on an overflow.
