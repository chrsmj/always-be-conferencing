Always Be Conferencing
**********************
Automatically join friends to your Conference Bridge before they even answer the phone!

Compatible with ASTERISK versions 13+, using AEL-based dial plan configuration file,
that you can access from your existing extensions.conf dial plan with minimal fuss.

The latest version is v21n released 12 February 2024, tested most on ASTERISK version 20.

Please see the AEL file for full README, mini-HOWTOs, and Examples -- yes, all in one file!

Here are some snippets/highlights:



abc.conf
========

This optional file can go into your /etc/asterisk/ directory:

[general]                   ; Always Be Conferencing by Penguin PBX Solutions.
                            ;
demomode=nope               ; Demo mode uses extension numbers 7001-7008.
                            ; There are also some named (not numbered) extensions
                            ; that you might want to try eg. ABCtheOG.
                            ;
[eoc]                       ; Emergency Outbound Calling
                            ;
paravm=ABCfriend1           ; Who gets notified of EOC calls (in Parallel mode.)
paravm=ABCfriend2           ; May be specified up to 10 times.
paravm=ABCtroll1            ; Be sure to include @context if not using 'default'.
paravm=ABCtroll2            ; These entries must exist in voicemail.conf file.
                            ; Consider making new voicemail users in a new
                            ; voicemail context just for EOC -- then you can
                            ; specify a better 'emailsubject' and 'emailbody'
                            ; to clearly represent that these are emergencies.
                            ;
parapg=PJSIP/123            ; Instead of notifying with VoiceMail, send a Page.
parapg=PJSIP/456            ; Each of these phones will hear call meta data,
parapg=PJSIP/789            ; not the actual audio stream.
                            ;
                            ;
[itm]                       ; Insta Teams Mode (WFH scenarios)
                            ;
conferenceminutes=120       ; Maximum length for one conference.
monthlyminutes=600          ; Allowed cumulative conference minutes for the month.
vmcontext=default           ; Voicemail context used to authenticate users.
numberstyle=us              ; Default is 'international' for robo number speech.



;
; Example #13: Really Fast Parallel Dial Method that generates Voicemail
; ----------------------------------------------------------------------
; Easiest integration choice with most existing ASTERISK 13-20.
; Sets up ABC using Dial() application by adding outbound Local channel.
; Change #YOUR-TRUNK# as appropriate per your PJSIP configuration.
; (Should also work with other channel techs eg. SIP, DAHDI, IAX2, OOH323, etc.)
; Note the 'r' option to dial for ring-back -- otherwise it will be silent ring.
; --> Also you must pre-configure the notify-ees' voicemail boxes in abc.conf file,
; but you can probably re-use existing entries (or make new ones -- paying
; attention to 'emailsubject' and 'emailbody' options to clarify the email.)
; Finally, the Parallel VM Mode doesn't actually do any conferencing -- sad ;( --
; but some users want this method of notification -- and ABC can make it easier,
; while integrating other interesting things like GPS/PLUSCODE location info!

[from-internal-custom-abc-example-13]

exten => 922,1,Dial(PJSIP/${EXTEN}@#YOUR-TRUNK#&Local/${EXTEN}@pngnpbx-abc-parallel-vm-notify,,r)

;
; Example #14: Really Fast Parallel Dial Method that generates Pages
; ------------------------------------------------------------------
; Slightly more involved than Example #13 but not by much.
; Allows for near-immediate nofications with minimal impact to your dial plan.
; Plays back A-B-C notes then call meta data info in the Page audio stream
; eg. A-B-C (notes) then "emergency 9 2 2 call from 7 7 7 dash A D A M"
; Change #YOUR-TRUNK# as appropriate per your PJSIP configuration.
; Only tested with PJSIP (as most things in ABC are.)
; --> Remember to add the parapg lines into abc.conf to specify Page-ees.

[from-internal-custom-abc-example-14]

exten => 922,1,Dial(PJSIP/${EXTEN}@#YOUR-TRUNK#&Local/${EXTEN}@pngnpbx-abc-parallel-pg-notify,,r)

;
; Example #15: Really Fast Parallel Dial Method that generates both Voicemails and Pages
; --------------------------------------------------------------------------------------
; Combination of Examples #13 and #14. The best of both worlds.
; Shows different handling based on Caller ID so eg. extension 555 won't Page itself!
; --> Remember to add both paravm and parapg lines into abc.conf for this to work.

[from-internal-custom-abc-example-15]

exten => 922/555,1,Dial(PJSIP/${EXTEN}@#YOUR-TRUNK#&Local/${EXTEN}@pngnpbx-abc-parallel-vm-notify)
exten => 922,1,Dial(PJSIP/${EXTEN}@#YOUR-TRUNK#&Local/${EXTEN}@pngnpbx-abc-parallel-vm-notify&Local/${EXTEN}@pngnpbx-abc-parallel-pg-notify,,r)

; same effect - thin and tall version

[from-internal-custom-abc-example-15-thin-and-tall]

exten => 922,1,Set(abc15vm=&Local/${EXTEN}@pngnpbx-abc-parallel-vm-notify)
exten => 922,2,Set(abc15pg=&Local/${EXTEN}@pngnpbx-abc-parallel-pg-notify)
exten => 922/555,2,NoOp(skip page for users with Caller ID 555)
exten => 922,3,Dial(PJSIP/${EXTEN}@#YOUR-TRUNK#${abc15vm}${abc15pg},,r)
