# Welcome to my write-up on the SQLMap room on TryHackMe.

1. If you are on Kali Linux, sqlmap is pre-installed. Otherwise, you have to install it for yourself. Let's start with downloading sqlmap - you can do so from here - [sqlmap GitHub Repository](https://github.com/sqlmapproject/sqlmap)

```bash
nare@nare$ sqlmap -h
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.6#stable}
|_ -| . [(]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

Usage: python3 sqlmap [options]

Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

  Target:
    At least one of these options has to be provided to define the
    target(s)

    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
    -g GOOGLEDORK       Process Google dork results as target URLs

  Request:
    These options can be used to specify how to connect to the target URL

    --data=DATA         Data string to be sent through POST (e.g. "id=1")
    --cookie=COOKIE     HTTP Cookie header value (e.g. "PHPSESSID=a8d127e..")
    --random-agent      Use randomly selected HTTP User-Agent header value
    --proxy=PROXY       Use a proxy to connect to the target URL
    --tor               Use Tor anonymity network
    --check-tor         Check to see if Tor is used properly

  Injection:
    These options can be used to specify which parameters to test for,
    provide custom injection payloads and optional tampering scripts

    -p TESTPARAMETER    Testable parameter(s)
    --dbms=DBMS         Force back-end DBMS to provided value

  Detection:
    These options can be used to customize the detection phase

    --level=LEVEL       Level of tests to perform (1-5, default 1)
    --risk=RISK         Risk of tests to perform (1-3, default 1)

  Techniques:
    These options can be used to tweak testing of specific SQL injection
    techniques

    --technique=TECH..  SQL injection techniques to use (default "BEUSTQ")

  Enumeration:
    These options can be used to enumerate the back-end database
    management system information, structure and data contained in the
    tables

    -a, --all           Retrieve everything
    -b, --banner        Retrieve DBMS banner
    --current-user      Retrieve DBMS current user
    --current-db        Retrieve DBMS current database
    --passwords         Enumerate DBMS users password hashes
    --tables            Enumerate DBMS database tables
    --columns           Enumerate DBMS database table columns
    --schema            Enumerate DBMS schema
    --dump              Dump DBMS database table entries
    --dump-all          Dump all DBMS databases tables entries
    -D DB               DBMS database to enumerate
    -T TBL              DBMS database table(s) to enumerate
    -C COL              DBMS database table column(s) to enumerate

  Operating system access:
    These options can be used to access the back-end database management
    system underlying operating system

    --os-shell          Prompt for an interactive operating system shell
    --os-pwn            Prompt for an OOB shell, Meterpreter or VNC

  General:
    These options can be used to set some general working parameters

    --batch             Never ask for user input, use the default behavior
    --flush-session     Flush session files for current target

  Miscellaneous:
    These options do not fit into any other category

    --wizard            Simple wizard interface for beginner users

[!] to see full list of options run with '-hh'
```

# Questions

1. **Which flag or option will allow you to add a URL to the command?**  
   The answer is pretty simple here - the flag **`-u`**

2. **Which flag would you use to add data to a POST request?**  
   We'd use the **`--data`** flag to add data to the POST request.

3. **There are two parameters: username and password. How would you tell sqlmap to use the username parameter for the attack?**  
   **`-p username`**

4. **Which flag would you use to show the advanced help menu?**  
   **`-hh`** (Help Help)

5. **Which flag allows you to retrieve everything?**  
   **`-a`** (stands for "ALL")

6. **Which flag allows you to select the database name?**  
   **`-D`**

7. **Which flag would you use to retrieve database tables?**  
   **`--tables`**

8. **Which flag allows you to retrieve a table’s columns?**  
   **`--columns`**

9. **Which flag allows you to dump all the database table entries?**  
   **`--dump-all`**

10. **Which flag will give you an interactive SQL Shell prompt?**  
    **`--sql-shell`**

11. **You know the current db type is 'MYSQL'. Which flag allows you to enumerate only MySQL databases?**  
    **`--dbms=mysql`**

---

# 2. Task

Started by running the default Nmap scan  
<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*aKqJcjz-S4A2rfKKFLgi_g.png"/>

I found port 80 to be open.  
I then ran the service scan and default script scan  
<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*44gR1pPCmHj0Z9TLOY4h1A.png"/>

I accessed the website and found out this  
<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*hQD9yyJC1RBVoOYZxtIt_g.png"/>

Next, I ran the Gobuster tool to find any available directories. I found *`/blood`*.  
<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*W_45-5tCkvPP_8DtvRaSug.png"/>

<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*ajBjp9VfEGb-iKZn3d5mTQ.png"/>

It had a login page and registration. I clicked on the login and then used Burp Suite to intercept the login page and save it to a TXT file  
<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*r0uh1MABWoLYj-lBzCXmTQ.png"/>

We now use SQLMap to enumerate the databases for usernames, passwords, and all data that can be found in it.

To enumerate for the current database  
<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*EW05Ww5JElR-3ZSqIfWfvw.png"/>

We find the DB to be *`blood`*.

Now that we know the name of the database, we look for tables available in the DB  
<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*4NDEQwAjMIyDVvbbvX_LrQ.png">

Now that we are interested in the flag table, we run the dump command to find its details  
<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*rfHXnApJd0GdAmzasmdH4w.png">

## Conclusion

Mitigating against exploiting SQL injection flaws and protecting database servers is crucial for maintaining data security and preventing unauthorized access. You need to consider:

- **Implementing Input Validation and Parameterized Queries**: Apply stringent input validation techniques to sanitize user inputs and eliminate potentially malicious SQL statements. Utilize parameterized queries or prepared statements to ensure that user inputs are treated as data rather than executable code.
- **Applying Principle of Least Privilege**: Restrict database user privileges to the minimum required for their respective tasks. Avoid using privileged accounts for routine operations and assign specific access rights based on user roles. This limits the potential damage an attacker can inflict even if they manage to exploit an SQL injection flaw.
- **Regularly Updating and Patching Database Software**: Keep the database management system (DBMS) software up to date with the latest security patches. Stay informed about vulnerabilities and exploits related to the specific DBMS and promptly apply relevant patches to protect against known attack vectors.
- **Employ Web Application Firewalls (WAFs)**: Implement a WAF that is capable of detecting and blocking SQL injection attempts. WAFs can analyze incoming requests, identify suspicious SQL statements, and prevent them from reaching the database server. This adds an additional layer of defense against SQL injection attacks.
- **Conducting Regular Security Audits and Penetration Testing**: Perform comprehensive security audits and penetration tests on your application and database infrastructure. Engage with experienced security professionals to identify and address any existing vulnerabilities or weaknesses. Regular testing helps in proactively identifying SQL injection flaws and taking appropriate mitigation measures.

By following these key points, organizations can significantly reduce the risk of SQL injection attacks and ensure the integrity and security of their database servers. It is essential to adopt a proactive approach to security, keeping up with the latest best practices and staying vigilant against emerging threats.

### Summary Questions

1. **What is the name of the interesting directory?**  
   *`blood`*

2. **Who is the current db user?**  
   *`root`*

3. **What is the final flag?**  
   *`thm{sqlm@p_is_L0ve}`*
