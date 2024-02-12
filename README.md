Always Be Conferencing
======================

Automatically join friends to your Conference Bridge before they even answer the phone!

Compatible with ASTERISK versions 13+, using AEL-based dial plan configuration file,
that you can access from your existing extensions.conf dial plan with minimal fuss.

The latest version is v21n released 12 February 2024, tested most on ASTERISK version 20.

Please see the AEL file for full README, mini-HOWTOs, and Examples -- yes, all in one file!


Quick Start mini-HOWTO demo for first time ABC users on FreePBX
===============================================================
Up and running in 15 minutes!

This mini-HOWTO assumes all phones are using same channel technology.
This could be all chan_PJSIP (recommended), or all chan_SIP,
or all chan_DAHDI, or all chan_IAX, or others - depends on you.

After completing these steps, you should be able to dial several
new extensions from your desk phone.

1. Start out by test dialing these numbers from your desk phone:

        123*222*0
        123*222*1
        123*222*2
        123*222
        123#222
        123222 (that's "123 ABC" on the phone keypad)
        922
        933

   You'll probably hear some sort of error message on most of
   them. This is different for each install. Use your judgement.
   You may need to disable some of the routes in your FreePBX
   if they conflict with these numbers. If that is not what you want,
   then please stop now and try one of the more advanced options
   for FreePBX integration (see Example #5 elsewhere in this file.)

2. SSH into your PBX:

        ssh muhpbx

3. Download copy of AEL file you are currently reading from GitHub to your PBX:

        wget https://raw.githubusercontent.com/chrsmj/always-be-conferencing/master/extensions-pngnpbx-abc-v21n.ael2

4. Copy in the new ABC AEL file:

        sudo cp extensions-pngnpbx-abc-v21n.ael2 /etc/asterisk/

5. Check if you already got some other AEL files active in your existing Asterisk install:

        ls -last /etc/asterisk/*.ael*

   If you only see this ABC AEL file, then jump ahead to Step 7A.

6. If you see other exisiting AEL files, then check the hashes:

        md5sum /etc/asterisk/extensions*.ael*

   The Asterisk sample AEL file "extensions.ael" MD5 hash value
   from versions ~1.8 to 18+ is:

        9e8a0e48880f732345eac9ed889b6115

   If your hash matches that, great, you've got a default AEL file.
   Proceed to Step 7A.

   If your hash is not a match, then your AEL file is non-standard.
   Either you or someone before changed that file. Possibly it was
   configured to load an older version of ABC. You must figure it out
   a little more before proceeding.

7. Allow Asterisk to load the new ABC file -- either replace or append
   to the "extensions.ael" file, using 7A or 7B (but not both steps):

    7A. Overwrite method

        echo '#include "extensions-pngnpbx-abc-v21n.ael2"' | sudo tee /etc/asterisk/extensions.ael

    7B. Append method

        echo '#include "extensions-pngnpbx-abc-v21n.ael2"' | sudo tee -a /etc/asterisk/extensions.ael

8. Choose your integration method with FreePBX -- any or all of the following 8A-8G:

    8A. GPS and other geolocation integration

        echo -e '[from-internal-custom]\ninclude => pngnpbx-abc-easy-include-gps' | sudo tee -a /etc/asterisk/extensions_custom.conf

    8B. Emergency Outbound Calling - 922 testing - TONGA WARNING: Do not enable this 922 just yet in TONGA and maybe more countries.

        echo -e '[from-internal-custom]\nexten => 922,1,Goto(pngnpbx-abc-easy-goto-eoc,922,simple)' | sudo tee -a /etc/asterisk/extensions_custom.conf

    8C. Emergency Outbound Calling - 933 more testing

        echo -e '[from-internal-custom]\nexten => 933,1,Goto(pngnpbx-abc-easy-goto-eoc,933,tincan)' | sudo tee -a /etc/asterisk/extensions_custom.conf

    8D. Emergency Outbound Calling - parallel notifications - US AND CANADA AND OTHERS WARNING: DO NOT CHANGE 911 ROUTING
        UNTIL AFTER YOU COMPLETE GOOD TESTS WITH 922 AND 933. ONLY THEN DO YOUR 911 CALL TEST AT OFF-PEAK PSAP HOURS.
        JUST BE NICE - DO NOT HANG UP ON THE TEST - TELL THEM IT IS A TEST AND CHECK YOUR LOCATION AND CALLER ID INFO!

        echo -e '[from-internal-custom]\nexten => 911,1,Goto(pngnpbx-abc-easy-goto-eoc,911,parallel)' | sudo tee -a /etc/asterisk/extensions_custom.conf

    8E. Allow full demo integration with FreePBX managed internal phones:

        echo -e '[from-internal-custom]\ninclude => pngnpbx-abc-easy-include-demo' | sudo tee -a /etc/asterisk/extensions_custom.conf

    8F. Allow video door bell demo:

        echo -e '[from-internal-custom]\ninclude => pngnpbx-abc-easy-include-doorbell' | sudo tee -a /etc/asterisk/extensions_custom.conf

    8G. Allow Insta Teams Mode:

        echo -e '[from-internal-custom]\ninclude => pngnpbx-abc-easy-include-itm' | sudo tee -a /etc/asterisk/extensions_custom.conf

9. Double-check if permissions and just plain existence of files seems reasonable:

        ls -last /etc/asterisk/extensions*.{conf,ael}*

10. Press "Apply Changes" button in FreePBX GUI.
    If you don't see the button, then make a small change elsewhere in the GUI,
    such as changing the name on a test extension, then save it and look again
    for the "Apply Changes" button.

11. Go ahead and try dialing the numbers in Step 1 again.
    All the numbers starting with 123 should do something.

    The 922 and 933 (and 911) will only work if you configure
    some additional settings in the "/etc/asterisk/abc.conf" file.

    To silence the ring-back you hear from friend's phones, but not the
    Remote End, you can run the following command on the ASTERISK CLI: 

        sirius*CLI> indication add us silent 0/15000

    (Or you can make silent ring a permanent modification in indications_custom.conf)

    To hear your GPS/PLUSCODE location, any participant can press * at any time
    during the call. To stop the bot playback, any participant can press #.
    See additional conference bridge menu options below - there are also
    different options available, based on the nature of the call.


Thus ends the mini-HOWTO for integration with FreePBX.
