# Testing for Data Storage and Privacy Issues

##Testing for Insecure Storage of Credentials and Keys (OMTG-DATAST-001)

A mobile application should not store any sensitive information like credentials, passwords, usernames or encryption keys. Even by using security controls and mechanisms offered by the OS as a best practice to protect the information, it can be recovered on a rooted device. It should be remembered that the confidentiality of sensitive information stored locally on a device cannot be guaranteed. 
Should business requirements or other factors outside the security domain dictate that certain sensitive information is kept on the device there are several best practices available on the OS level that should be applied to make it harder for attackers to retrieve sensitive information. 


## Android

### White-box Testing

- Check if .db files are available, which are sqlite databases and if they contain sensitive information (usernames, passwords, keys etc.). db files can be access on the command line with sqlite3. Do not create files with permissions of MODE_WORLD_READABLE or MODE_WORLD_WRITABLE unless it is required as any app would be able to read or write the file even though it may be stored in the app’s private data directory.

        egrep -irn "MODE_WORLD_READABLE|MODE_WORLD_WRITABLE" . 

- openFileOutput() will create the file (or replace a file of the same name) and make it private to the application.

        egrep -irn "openFileOutput(" . 
        
- Although the name suggests this is External storage, it is actually a private application folder that could exist on the external sdcard. getExternalFilesDir() will return a path to the application's /data/ folder or internal storage. Check that no confidential information is written to the SDcard/external storage. Instead it should be stored in memory. 

        egrep -irn "getExternalFilesDir(|getExternalFilesDirs(" . 

### Black-box Testing

#### Analyse the app directory 

 1. Use the mobile app extensively so that all functionality is at least triggered once.
  
 2. Download the mobile app data directory from  /data/data/com.example.appname and the global storage directory /sdcard/ . 
 
 3. Look for sensitive data such as credentials, passwords, usernames, encryption keys or any other information that could be classified as sensitive in all downloaded files. 

#### Profile file system calls  



### Remediation

Sensitive information should not be stored on the device. Instead, perform initial authentication using credentials supplied by the user, and then use a short-lived, service-specific authorization token (session token).
If credentials, keys or other sensitive information need to be stored locally use the KeyChain or KeyStore according to your requirements.

- KeyChain: Use the KeyChain API when you want system-wide credentials. When an app requests the use of any credential through the KeyChain API, users get to choose, through a system-provided UI, which of the installed credentials an app can access. This allows several apps to use the same set of credentials with user consent.

- Keystore: Use the Android Keystore provider to let an individual app store its own credentials that only the app itself can access. This provides a way for apps to manage credentials that are usable only by itself while providing the same security benefits that the KeyChain API provides for system-wide credentials. This method requires no user interaction to select the credentials.


### References

[OWASP Mobile TOP 10: Insecure Data Storage](https://www.owasp.org/index.php/Mobile_Top_10_2014-M2)

[Android KeyChain Documentation](http://developer.android.com/reference/android/security/KeyChain.html)

[Android KeyStore System Documentation](http://developer.android.com/training/articles/keystore.html)

