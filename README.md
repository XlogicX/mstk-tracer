# mstk-tracer
Contact Tracing (specifically for MakerSpace Toolkit)

# From ./tracer -h
usage: tracer [-h] --patientzero PATIENTZERO --datepositive DATEPOSITIVE [--incubation INCUBATION] [--logfile LOGFILE]

Example Usage:
--------------------------------
tracer --patientzero 'Persons Name' --datepositive '04/20/2020'

optional arguments:
  -h, --help            show this help message and exit
  --patientzero PATIENTZERO
                        The persons name (as logged in log file) that has tested positive
  --datepositive DATEPOSITIVE
                        Date you expect person to be contageous, format like 04/20/2020
  --incubation INCUBATION
                        Incubation period (in days); how long it takes a person to become contageous
  --logfile LOGFILE     specify a logfile other than default, like test.log
  
# Description
Though this script can be used in a general purpose fasshion with any log file that conforms to the format found in test.log (found in this repo), it was more specifically created to be used for contact tracing for the FatCat Fab Lab environment in the case an individual contracts a pandemic level illness.

You must provide the name of the person that tested positive (as it would be found in the log) and the date that they would have started being contageous (in mm/dd/yyyy format). You could also provide your own logfile (it is hardcoded for the FatCat log otherwise) and you can also set your own custom incubation period.

# Scenario Description
It may be easier to walk through a scenario to get a feel for how this works. This example will use test.log, which is a simplified log file designed to help illustrate the functionality (This is why the 'names' are named the way they are).

We are saying that 'Patient Zero' is the infected person. You can see that there are 'Before Person's that were in the space before hand (so not exposed). There are people named 'Patient...' as these are people we would expect to get infected. There are also some 'After Person's; people that weren't there on the day exposed people were, but could still be exposed by contact surfaces.

...
