# mstk-tracer
Contact Tracing (specifically for MakerSpace Toolkit)

# Usage
From ./tracer -h
```
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
```

# Description
Though this script can be used in a general purpose fashion with any log file that conforms to the format found in test.log (found in this repo), it was more specifically created to be used for contact tracing for the FatCat Fab Lab environment in the case an individual contracts a pandemic level illness.

You must provide the name of the person that tested positive (as it would be found in the log) and the date that they would have started being contageous (in mm/dd/yyyy format). You could also provide your own logfile (it is hardcoded for the FatCat log otherwise) and you can also set your own custom incubation period.

# Normal Scenario
It may be easier to walk through a scenario to get a feel for how this works. This example will use test.log, which is a simplified log file designed to help illustrate the functionality (This is why the 'names' are named the way they are).

We are saying that 'Patient Zero' is the infected person, lets say they are contageous on 04-21-2018 (even if this isn't a day they came into the space). You can see that there are 'Before Person's that were in the space before hand (so not exposed). There are people named 'Patient...' as these are people we would expect to get infected. There are also some 'After Person's; people that weren't there on the day exposed people were, but could still be exposed by contact surfaces.

At its most basic for this scenario, this is the command I would run:
```
./tracer --patientzero 'Patient Zero' --datepositive '04/21/2018' --logfile test.log
```

```
These are the results that we would get:
Potential Social Infections (Timeline):
Patient Zero Exposed Before Person 3 on 04/23/2018
Patient Zero Exposed Patient Two A on 04/23/2018
Patient Zero Exposed Patient Two B on 04/23/2018
Patient Zero Exposed Patient Two C on 04/23/2018
Patient Two B Exposed Another Person Three on 04/29/2018
Patient Two B Exposed Patient Three A on 04/29/2018
Patient Two B Exposed Patient Three B on 04/29/2018
Patient Three A Exposed Patient Four on 05/02/2018
Patient Two C Exposed Patient Four The 2nd on 05/05/2018

People potentially directly exposed by Patient Zero:
Patient Zero
Before Person 3
Patient Two A
Patient Two B
Patient Two C

People potentially indirectly exposed:
Another Person Three
Patient Three A
Patient Three B
Patient Four
Patient Four The 2nd

Potential Contact Surface Infections
After Person One Could have possibly been exposed
After Person Two Could have possibly been exposed
After Person One Could have possibly been exposed
```

The timeline is not a hard fact as far as the indirect exposures; there are many paths possible to getting exposed, this just lists a way that these people COULD have been exposed. what's not up for interpretation is that these people likely were at risk of exposure, regardless of by who.

The next section lists the names of the people directly exposed by patient zero.
The next section lists the people indirectly exposed by patient zero, meaning patient zero exposed someone that exposed someone else, even though that someone else may not have directly had contact with patient zero.
Finally, there is a list of the remaining people that could have been indirectly exposed by contact surfaces. This means that these people did not have any contact with patient zero or any of the direct or indirect exposures, but they DID happen to go to the lab on a day after patient zero was there.

#Incubation Scenario
Same scenario as scenario I actually. But lets say that the CDC says that a new variant has a more aggressive incubation time; once you are exposed to the new variant, you become contageous within 15 minutes.

By default, the incubation time is set to 2 days, which is somewhat aggressive. What this means is that if Patient Zero exposes someone, and that someone goes to the space the next day, they wouldn't be considered as exposing other people there that day, but would be considered the next day. We can change this behavior with --incubation.

In the case of our logs, not much would change with the results, as the days are spaced every 3 days anyway. However, if we were to assume a minimum 4 day incubation period and run:
```
./tracer --patientzero 'Patient Zero' --datepositive '04/20/2018' --incubation 4 --logfile test.log
```

'Patient Four' was no longer socially exposed, but could be exposed due to contact surfaces still. Originally, Patient Zero exposed Patient Two B on 04/23/2018, who then exposed Patient Three A on 04/29/2018, who then exposed Patient Four on 05/02/2018. Note that previously, Patient Three A was exposed on 04/29/2018 and took two days to become contageous (which would be by 05/01/2018), so is able to expose Patient Four the next day on 05/02/2018. But now that we set the incubation to 4 days, Patient Three A wouldn't be able to exose people until 05/03/2018, which is the day after they came into contact with Patient Four.

# Oversights
As you can see, test.log is fairly simple, it's really only complicated enough to try and run the script through enough of the edge cases to test that it is indeed running the way it should. When faced with real data with a lot of people, this kind of analysis could get complicated if using the same process. One would just find it easier to through their hands in the air and tell EVERYONE in the space after the patient zero was there that they should quarantine and get tested. Not like there's anything wrong with getting tested, but no need to quarantine if it's unlikely you were exposed (subjective I guess).

That said, there are some things this tool doesn't consider. If non-ill person enters the lab in the morning and leaves at noon, and then a patient zero comes in during the evening, this script will still lump in that first person as potientially exposed (a false positive). This script takes things a day at a time. Same issue goes for if someone else comes in after midnight that same day and patient zero doesn't come in that 'next' day. Though, that after-mightnight person would still show up in the contact-surfaces part of the log, so it's not a complete false negative. So far things lean on prioritizing false postives over negatives (as it should).

This script also does not take into account a time-frame when an infected person eventually becomes non-contageous (to where the script would no longer evaluate who this person exposes). I didn't consider this to be an important enough issue to write logic around, but documenting it just so it's known. The main reason this shouldn't be too much of an issue is that it's unlikely you'd run this script to look at several months of data at once.
