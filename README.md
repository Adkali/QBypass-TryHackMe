# QBypass-TryHackMe

Upon deploying the machine, we immediately can navigate to the provided IP address, where we are greeted with a login page asking for credentials we don't currently have. Attempts with the commonly-used 'admin:admin' credentials were unsuccessful. when seeing the source of the 'invalid credentials', a comment inside it reveals a sensitive imformation about the query in the back-end.

Next, we opted for a directory brute-force approach, employing gobuster and tapping into the built-in wordlists found at ( /usr/share/wordlist ). This led us to a directory named "notes." This directory gave us another hint about at a vulnerability within the login page that demanded swift rectification.

Rather than attempting to brute-force the login page without any known username/password combinations, the note offered a clue about a potential flaw in the login mechanism. Leveraging this information, an SQL injection was attempted.
Using the known and classic SQLi payload one ( 'OR 1=1 -- -' ), we did not gain access to the admin area. Anyway, This injection works on the premise that the statement "1 equals 1" is always true, which can leading to us being authenticated as the user with ID number 1. When trying ' || username='admin' -- -#, we gained access to the admin area. '||' is another operator equals to OR which was cought as a suspicious attempt and we managed to bypassing it. When this is inserted into a SQL query, it can change the logic of the query to return all rows, regardless of other conditions.

After gaining access, seeing the source page reveals a base64 comment which is double-encoded.
Running nmap confirmed that port 22, typically associated with SSH, was open. Initial attempts to access using the "admin" username and the decoded message proved unsuccessful. However, a subsequent attempt using the username "administrator" paired with the doubly-decoded base64 message successfully granted me access to the machine.<br>
<b>Note:</b> In a real World-Scenrios, this kind of situation requires from you to try and locate/brute-force the username/email. using 'Hydra' or other OSINT methods with the encoded message and a usernames wordlist - it is possible.

Upon gaining access, we located the user.txt file within the 'reminders' directory on the 'administrator's root folder. Anyway, this directory also contained another note titled Important.txt. This note conveyed a stern warning, seemingly chastising the administrator for the reckless habit of storing notes in unsecured locations. Further exploration focused on identifying programsrunning with SUID. To our surprise, we discovered that the base64 command was operating with suid permissions.

Turning to GTFOBins, it was evident that the misconfiguration with the base64 command having suid permissions could be exploited. we aimed to retrieve the root file from /root/root.txt using this vulnerability, but to no avail. It raised the question: could the root be housed in an alternative location? Given the elevated permissions associated with base64, it seemed feasible to retrieve the system's shadow file. Armed with this, the objective was to brute-force the root hash. To cut to the chase, success was achieved, and the root password was compromised.

With the root password in hand, retrieving the flag was a breeze.<br>
<b>Remember:</b> this is just one avenue of exploitation for this machine. Are there alternative methods? Maybe!
I hope this walkthrough provided valuable insights and that you found it engaging. Enjoy your hacking journey!
