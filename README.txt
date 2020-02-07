Always Be Conferencing
**********************
Automatically join friends to your Conference Bridge before they even answer the phone via AEL-based configuration for Asterisk.

;
; Example #5: More advanced FreePBX integration
; ---------------------------------------------
; * Copy this context into your /etc/asterisk/extensions_custom.conf file.
; * Integrates with FreePBX per-device emergency_cid numbers.
; * Just fill in the #XYZ# variables once for your entire system.
; * Then, create a new Custom Destination in FreePBX to point to:
;       from-internal-pngnpbx-abc,s,1
; * Finally, create a new Misc Application in FreePBX to point an
;   extension number to the new Custom Destination eg. 933
; * This example uses legacy chan_SIP instead of newer chan_PJSIP
;

[from-internal-pngnpbx-abc]

exten => s,1,NoOp()
exten => s,n,NoOp(placeholder)
exten => s,n,Log(VERBOSE,Always Be Conferencing - PenguinPBX.com)
exten => s,n,Set(callback=${DB(DEVICE/${cidnum}/emergency_cid)})
exten => s,n,Set(ABCTO=${CALLERID(DNID)})
exten => s,n,Set(path1=SIP/${ABCTO}@#SIPTRUNK-1#)
exten => s,n,Set(path2=SIP/${ABCTO}@#SIPTRUNK-2#)
exten => s,n,Set(path3=SIP/${ABCTO}@#SIPTRUNK-3#)
exten => s,n,Set(friend1=SIP/#SECURITY-EXTENSION#)
exten => s,n,Set(friend2=SIP/#ERDOCTOR-EXTENSION#)
exten => s,n,Set(vmail1=#FRONTDESK-EXTENSION#@default)
exten => s,n,Set(vmail2=#ITDIRECTOR-EXTENSION#@default)
exten => s,n,Set(abctypename=amp)
exten => s,n,Set(ringtime=120)
exten => s,n,Set(vmsecs=3)
exten => s,n,AELSub(pngnpbx-abc-tincan,${abctypename})
exten => s,n,AELSub(pngnpbx-abc-path,1,${ringtime},${path1})
exten => s,n,AELSub(pngnpbx-abc-path,2,${ringtime},${path2})
exten => s,n,AELSub(pngnpbx-abc-path,3,${ringtime},${path3})
exten => s,n,AELSub(pngnpbx-abc-remote,${callback},static)
exten => s,n,AELSub(pngnpbx-abc-friend,1,${friend})
exten => s,n,AELSub(pngnpbx-abc-friend,2,${friend})
exten => s,n,AELSub(pngnpbx-abc-vmail,${vmail1},${vmsecs});
exten => s,n,AELSub(pngnpbx-abc-vmail,${vmail2},${vmsecs});
exten => s,n,AELSub(pngnpbx-abc-howdy-yall)

