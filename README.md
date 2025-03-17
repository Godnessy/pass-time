# pass-time
A simple script that will check for you if there are available appointments to your local Norwegian police station - This is mainly for developers but anyone with a bit of knowhow and some will to try should 
be able to use this.

Note: This script should help you find appointments to any service offered by the police website that uses the qmaticwebbooking API.


How to use (windows) - Note that I am over explaining to make this possible for none technical people to use it as well, for developers you should be able to skip a lot of this:
1. Go To the Norwegian police website: https://pass-og-id.politiet.no/timebestilling/index.html#/
2. Pick the service you want to book an appointment for
3. Pick the station you wish to go to --- !! DO NOT CLICK NEXT YET !!
4. Right click on the page and click Inspect (In chrome and firefox)
5. Navigate to the Network tab of the developer tools console (The new window that opened)
6. To make it easy click on the little circle with a diagonal line going through it on the right side of the console (next to the red stop button), this will clear the current network logs making it easier to find the next one.
7. Now click next to go to the Velg Tidspunk stage, it might say there are no available appointments, that's ok
8. In the Network window you will now see 1 or more lines appear, pick the one first one and go to the Headers tab in the right side window (still inside the network tab in the developer console)
9. Select the value next to the Request URL header, copy the whole link that should look like this:
https://pass-og-id.politiet.no/qmaticwebbooking/rest/schedule/branches/1da3d407da965ec0f39af67d36275f4b7106c7a5ea5b386209b9ec843ec7ac5e/dates;servicePublicId=af8dff2199e23e84b3c0172c4b04b45dfcf5e266e5b489cdca32c6fe88f603ef;customSlotLength=5

Note: DONT copy the part that says ;customSlotLength=, only copy upto the ; like I did in this pic:

![image](https://github.com/user-attachments/assets/62f2838e-9a42-4c62-92eb-f0d1a3b32c3e)

10. Now where ever you want on your PC create a new TXT file (you can use notepad for this)
11. Inside this file you paste this script (note that I have made a line for you to paste the URL inside, remove the exclamation marks):
~~~~~~~~~~~~~~~~~~~~~~~ Copy Only Under this Line ~~~~~~~~~~~~~~~~~~~~~~~~
@echo off
setlocal EnableDelayedExpansion
:: Set API URL
set API_URL=https://pass-og-id.politiet.no/qmaticwebbooking/rest/schedule/branches/8014a727faa2b1779bf9f458ae2614f4560b776a18a5549aace8fa46ee589803/dates;servicePublicId=9c9f4070899d6d3f85e2df162aae8e981fb79a81bc7b3f6bf6fb575542a3c4a4

echo Checking API endpoint...
echo URL: %API_URL%
echo.
echo Please wait while processing...
:: Make the API request and save the full response
echo Sending request...
curl -s -f "%API_URL%" > response_temp.txt 2>error_temp.txt
set CURL_ERROR=%ERRORLEVEL%
set /p RESPONSE=<response_temp.txt
echo Request completed.
echo.
echo Analyzing response...

if %CURL_ERROR% NEQ 0 (
    echo RESULT: URL ERROR DETECTED!
    echo.
    echo *** WARNING: INVALID URL OR SERVER ERROR! ***
    echo.
    echo Error details: The link you pasted in is wrong or the Politi server isn't working, go back to the guide and re do the link copying steps.
    echo --------------------
    type error_temp.txt
    echo --------------------
) else if "!RESPONSE!"=="[]" (
    echo RESULT: API returned an empty array
    echo Details: There are no available dates right now, try again tomorrow!
) else (
    echo RESULT: API returned data!
    echo.
    echo *** WARNING: NON-EMPTY RESPONSE DETECTED! - There are available appointments -  GO TO POLITI WEBSITE TO BOOK AN APPOINTMENT ***
    echo.
    echo Response content:
    echo --------------------
    type response_temp.txt
    echo -------------------

    echo.
    echo.
    echo There are available appointments -  GO TO POLITI WEBSITE TO BOOK AN APPOINTMENT

    echo Copy this address and paste it in your browser: https://www.politiet.no/tjenester/pass-og-id-kort/bestille-endre-eller-avbestille-time-til-pass-og-id-kort/
)

echo.
:: Clean up temporary files
del response_temp.txt
if exist error_temp.txt del error_temp.txt
:: Keep the window open
echo ======================================
echo Process finished. Press any key to exit...
pause > nul
endlocal

~~~~~~~~~~~~~~~~~~~~~~ Stop copying over this line ~~~~~~~~~~~~~~~~~~~~~~

12. After you have copied the script in and pasted the URL correctly , Click File (on the menu bar) SAVE AS (not just save) - Give the script a name and after the name add .bat (This creates a windows executable file) - Click Save
13. Now a new file has been created, you can run this file by double clicking on it, the script will check if the URL is valid and working, if not it will give you an error that says *** WARNING: INVALID URL OR SERVER ERROR *** - This means that you copied the wrong URL or you copied it incorrectly, repeat steps 7-9  in the TXT file, You can also edit the BAT file directly by right clicking on it and then clicking Edit in Notepad.


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ End of How to, now for some info: ~~~~~~~~~~~~~~~~~~~~~
If you did everything right you should get 1 of 2 possible responses for a valid link:
1. If there are available dates you will get this:
RESULT: API returned data

*** WARNING: NON-EMPTY RESPONSE DETECTED - There are available appointments -  GO TO POLITI WEBSITE TO BOOK AN APPOINTMENT ***

Response content:
--------------------
~~~ Here you will see a list of dates ~~~~

2. If there are no dates available you will see this response:

RESULT: API returned an empty array
Details: There are no available dates right now, try again tomorrow



IF the link you pasted is not valid OR if the Politi servers are down you will get this error:
RESULT: URL ERROR DETECTED

*** WARNING: INVALID URL OR SERVER ERROR ***

Error details: The link you pasted in is wrong or the Politi server isn't working, go back to the guide and re do the link copying steps.
--------------------
--------------------




