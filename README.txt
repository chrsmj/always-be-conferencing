Always Be Conferencing
**********************
Automatically join friends to your Conference Bridge before they even answer the phone
via AEL-based configuration for Asterisk.

The latest version is v16l released 21 August 2020.
Earlier versions are kept for historical reference but are not recommended for new deployments.

Please see full Examples near the top of the AEL file. Here is a sample:

;
; Example #13: Really Fast Parallel Dial Method that generates Voicemail
; ----------------------------------------------------------------------
; Easiest integration choice with most existing ASTERISK 13, 16 or 17.
; Sets up ABC using Dial() application by adding outbound Local channel.
; Change #YOUR-TRUNK# as appropriate per your PJSIP configuration.
; (Should also work with other channel techs eg. SIP, DAHDI, IAX2, OOH323, etc.)
; Note the 'r' option to dial for ring-back -- otherwise it will be silent ring.
; Also you must pre-configure the notify-ees' voicemail boxes in abc.conf file,
; but you can probably re-use existing entries (or make new ones -- paying
; attention to 'emailsubject' and 'emailbody' options to clarify the email.)
; Finally, the Parallel VM Mode doesn't actually do any conferencing -- sad ;( --
; but some users want this method of notification -- and ABC can make it easier,
; while integrating other interesting things like GPS/PLUSCODE location info!

[from-internal-custom-abc-example-13]

exten => 922,1,Dial(PJSIP/${EXTEN}@#YOUR-TRUNK#&Local/${EXTEN}@pngnpbx-abc-parallel-vm-notify,,r)
