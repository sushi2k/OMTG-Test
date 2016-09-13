# Testing Authentication and Session Management (OMTG-AUTH)

## Testing for User ID Spoofing (OMTG-AUTH-001)
UDIDs (Unique Device IDs) such as Serial Numbers, IMEI, ICCID, and so on might be modified by an attacker. 
See also http://android-developers.blogspot.sg/2011/03/identifying-app-installations.html 
 
## Testing for 2FA Bypass (OMTG-AUTH-002)

Two-factor authentication systems aren’t as foolproof as they seem. An attacker doesn’t actually need your physical authentication token if they can trick your phone company or the secure service itself into letting them in.

Additional authentication is always helpful. Although nothing offers that perfect security we all want, using two-factor authentication puts up more obstacles to attackers who want your stuff.

The two-step authentication systems on many websites work by sending a message to your phone via SMS when someone tries to log in. Even if you use a dedicated app on your phone to generate codes, there’s a good chance your service of choice offers to let people log in by sending an SMS code to your phone. Or, the service may allow you to remove the two-factor authentication protection from your account after confirming you have access to a phone number you configured as a recovery phone number.

This all sounds fine. You have your cell phone, and it has a phone number. It has a physical SIM card inside it that ties it to that phone number with your cell phone provider. It all seems very physical. But, sadly, your phone number isn’t as secure as you think.

If you’ve ever needed to move an existing phone number to a new SIM card after losing your phone or just getting a new one, you’ll know what you can often do it entirely over the phone — or perhaps even online. All an attacker has to do is call your cell phone company’s customer service department and pretend to be you. They’ll need to know what your phone number is and know some personal details about you. These are the kinds of details — for example, credit card number, last four digits of an SSN, and others — that regularly leak in big databases and are used for identity theft. The attacker can try to get your phone number moved to their phone.

There are even easier ways. Or, For example, they can get call forwarding set up on the phone company’s end so that incoming voice calls are forwarded to their phone and don’t reach yours.

Heck, an attacker might not need access to your full phone number. They could gain access to your voice mail, try to log in to websites at 3 a.m., and then grab the verification codes from your voice mailbox. How secure is your phone company’s voice mail system, exactly? How secure is your voice mail PIN — have you even set one? Not everyone has! And, if you have, how much effort would it take for an attacker to get your voice mail PIN reset by calling your phone company?



Your phone number becomes the weak link, allowing your attacker to remove two-step verification from your account — or receive two-step verification codes — via SMS or voice calls. By the time you realize something is wrong, they can have access to those accounts.

This is a problem for practically every service. Online services don’t want people to lose access to their accounts, so they generally allow you to bypass and remove that two-factor authentication with your phone number. This helps if you’ve had to reset your phone or get a new one and you’ve lost your two-factor authentication codes — but you still have your phone number.


## Testing the Logout Functionality (OMTG-AUTH-003)

Overview

Session termination is an important part of the session lifecycle. Reducing the lifetime of the session tokens to a minimum decreases the likelihood of a successful session hijacking attack. 

The scope for this test case is to validate that the application has a logout functionality and it effectively terminates the session on client and server side.

```
How to Test
To verify the correct implementation of a logout functionality, dynamic analysis should be applied by using an interception proxy. This technique can be applied to both, Android and iOS platform.  
Android/iOS
Static Analysis
If server side code is available, it should be reviewed to validate that the session is being terminated as part of the logout functionality.
The check needed here will be different depending on the technology used. Here are different examples on how a session can be terminated in order to implement a proper logout on server side:
Spring (Java) - http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/web/authentication/logout/SecurityContextLogoutHandler.html 
Ruby on Rails -  http://guides.rubyonrails.org/security.html 
PHP - http://php.net/manual/en/function.session-destroy.php 
JSF - http://jsfcentral.com/listings/A20158?link 
ASP.Net - https://msdn.microsoft.com/en-us/library/ms524798(v=vs.90).aspx 
Amazon AWS - http://docs.aws.amazon.com/appstream/latest/developerguide/rest-api-session-terminate.html 
```

Dynamic Analysis
For a dynamic analysis of the application an interception proxy should be used. Please see section XXX on how to set it up. 
The following steps can be applied to check if the logout is implemented properly.  
Log into the application. 
Do a couple of operations that require authentication inside the application.
Perform a logout operation.
Resend one of the operations detailed in step 2 using an interception proxy. For example, with Burp Repeater. The purpose of this is to send to the server a request with the token that has been invalidated in step 3.

If the session is correctly terminated on the server side, either an error message or redirect to the login page will be sent back to the client. On the other hand, if you have the same response you had in step 2, then, this session is still valid and has not been correctly terminated on the server side.
A detailed explanation with more test cases, can also be found in the OWASP Web Testing Guide (OTG-SESS-006) [1].
Remediation

One of the most common errors done by developers to a logout functionality is simply not destroying the session object in the server side. This leads to a state where the session is still alive even though the user logs out of the application. The session remains alive, and if an attacker get’s in possession of a valid session he can still use it and a user cannot even protect himself by logging out or if there are no session timeout controls in place.

To mitigate it, the logout function on the server side must invalidate this session identifier immediately after logging out to prevent it to be reused by an attacker that could have intercepted it. 

Related to this, it must be checked that after calling an operation with an expired token, the application does not generate another valid token. This could lead to another authentication bypass.

Many Apps do not automatically logout a user, because of customer convenience. The user logs in once, afterwards a token is generated on server side and stored within the applications internal storage and used for authentication when the application starts instead of asking again for user credentials. There should still be a logout function available within the application and this should work according to best practices by also destroying the session on server side. 

Tools
OWASP ZAP - https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project
Burp - https://portswigger.net/burp/

References

[1] OWASP web application test guide - https://www.owasp.org/index.php/Testing_for_logout_functionality_(OTG-SESS-006)
[2] OWASP Session management cheatsheet - https://www.owasp.org/index.php/Session_Management_Cheat_Sheet


