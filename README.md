# CBT519
Converted to GitHub via [cbt2git](https://github.com/wizardofzos/cbt2git)

This is still a work in progress. 
Due to amazing work by Alison Zhang and Jake Choi repos are no longer deleted.

```
//***FILE 519 is from Sam Golob, and contains a system to compare   *   FILE 519
//*           the status of tapes in CA-1 (TMS - Tape Management    *   FILE 519
//*           System) with the status of the corresponding volume   *   FILE 519
//*           names (numbers) in VTS (IBM's Virtual Tape System),   *   FILE 519
//*           which looks, to MVS, like a 3494 Tape Library.        *   FILE 519
//*                                                                 *   FILE 519
//*           This, essentially, is an audit system between TMS     *   FILE 519
//*           (CA-1) and the VTS.                                   *   FILE 519
//*                                                                 *   FILE 519
//*           There is some installation-dependent code in the      *   FILE 519
//*           last program, which is the actual audit program       *   FILE 519
//*           that compares the TMS data with the VTS data.  All    *   FILE 519
//*           the other programs (I think) can run anywhere.  I     *   FILE 519
//*           have included our alphanumeric volser information,    *   FILE 519
//*           our TMSUX2E and TMSUX2U exits, as well as a TMSBLDUE  *   FILE 519
//*           report to show our actual tape ranges.  Installation  *   FILE 519
//*           dependencies are roughly marked in the TMLIBAUD code. *   FILE 519
//*                                                                 *   FILE 519
//*           If you have questions, please feel free to contact    *   FILE 519
//*           Sam Golob:   sbgolob@cbttape.org                      *   FILE 519
//*                                                                 *   FILE 519
//*     PROGRAMS:                                                   *   FILE 519
//*                                                                 *   FILE 519
//*       TMLIBA01 - Reads your TMC (from TMS) and produces a       *   FILE 519
//*                  complete volume list, representing all         *   FILE 519
//*                  volumes (even the ones in DELETE status)       *   FILE 519
//*                  defined to TMS.                                *   FILE 519
//*                                                                 *   FILE 519
//*       TMLIBA02 - Reads a LISTCAT of your VOLCAT (either the     *   FILE 519
//*                  short version or the long version) and         *   FILE 519
//*                  produces a list of all volumes defined to      *   FILE 519
//*                  the VTS.                                       *   FILE 519
//*                                                                 *   FILE 519
//*       TMLIBA03 - Match-merge program to compare the TMC         *   FILE 519
//*                  volume list, to the VTS volume list.           *   FILE 519
//*                  Three outputs are produced:                    *   FILE 519
//*                                                                 *   FILE 519
//*                  TNOV  -  Tape volumes in TMS and not in VTS    *   FILE 519
//*                  VNOT  -  Tape volumes in VTS and not in TMS    *   FILE 519
//*                  BOTH  -  Tape volumes in both places           *   FILE 519
//*                                                                 *   FILE 519
//*       TMLIBA04 - Programs to find information about the         *   FILE 519
//*       TMLIBA05   status of volumes in the VTS.  We run them     *   FILE 519
//*                  against the BOTH and VNOT lists concatenated   *   FILE 519
//*                  together, and sorted by volser (colums 1-6).   *   FILE 519
//*                                                                 *   FILE 519
//*                  TMLIBA04, which does an inquiry to the Library *   FILE 519
//*                  Management System for each volser, is too      *   FILE 519
//*                  slow, therefore TMLIBA05 was written to run    *   FILE 519
//*                  against the LISTCAT for the library catalog,   *   FILE 519
//*                  to pull equivalent information.                *   FILE 519
//*                                                                 *   FILE 519
//*                  Input (INDD) is FB-80 and consists of the      *   FILE 519
//*                  volume lists.                                  *   FILE 519
//*                                                                 *   FILE 519
//*                  Output (OUTDD) is FB-385, and is a file we     *   FILE 519
//*                  create, containing output from the IBM macro   *   FILE 519
//*                  CBRXLCS, as below, and other relevant VTS      *   FILE 519
//*                  volume information that is available to the    *   FILE 519
//*                  program.  This file is input to the TMLIBAUD   *   FILE 519
//*                  audit program.  Macro call is as follows:      *   FILE 519
//*                                                                 *   FILE 519
//*                  CBRXLCS  TYPE=TAPE,                            *   FILE 519
//*                        FUNC=QVR,                                *   FILE 519
//*                        VOLUME=TRANVOL,                          *   FILE 519
//*                        VOLINFO=YES,                             *   FILE 519
//*                        MF=(E,LCSLIST),                          *   FILE 519
//*                        SUBPOOL=0                                *   FILE 519
//*                                                                 *   FILE 519
//*                  It is essential to code VOLINFO=YES, and       *   FILE 519
//*                  (the book informs me), that also allows the    *   FILE 519
//*                  program to obtain information from the         *   FILE 519
//*                  Library Manager Inventory, as well as from     *   FILE 519
//*                  the TCDB (the VOLCAT).                         *   FILE 519
//*                                                                 *   FILE 519
//*       TMLIBTM  - This is an EARL program, that produces a       *   FILE 519
//*                  report containing essential data about active  *   FILE 519
//*                  TMS volumes.  Input to the TMLIBAUD program    *   FILE 519
//*                  from the TMS side, comes from here.  JCL to    *   FILE 519
//*                  run this program, and all the programs in      *   FILE 519
//*                  the entire collection, is in member TMLIBRUN.  *   FILE 519
//*                                                                 *   FILE 519
//*       TMLIBAUD - This is the actual compare program which we    *   FILE 519
//*                  use, to identify "inconsistencies" (as we      *   FILE 519
//*                  define them) between TMS and the VTS.  In our  *   FILE 519
//*                  implementation, four error types are found.    *   FILE 519
//*                  Reports on these are sent, both to separate    *   FILE 519
//*                  outputs, and to one (total and summary)        *   FILE 519
//*                  summation output.                              *   FILE 519
//*                                                                 *   FILE 519
//*                  You can re-code this program to detect any     *   FILE 519
//*                  other conditions between the TMS and the VTS   *   FILE 519
//*                  which you feel you need to know about.  This   *   FILE 519
//*                  program is really the only one in the set,     *   FILE 519
//*                  that contains installation dependencies.       *   FILE 519
//*                                                                 *   FILE 519
//*       TMLIBRUN - The job stream to run the entire package.      *   FILE 519
//*                                                                 *   FILE 519
//*       CBRTST   - Program (also found on File 516) to query      *   FILE 519
//*                  the status of volumes in TMS.  This program    *   FILE 519
//*                  uses a call to the TMS CTSQSTS facility, and   *   FILE 519
//*                  may be run against the output of TMLIBA01,     *   FILE 519
//*                  which is why I am also including it here.      *   FILE 519
//*                  This program should be run from a non-APF      *   FILE 519
//*                  library (non-authorized library).              *   FILE 519
//*                                                                 *   FILE 519
```
