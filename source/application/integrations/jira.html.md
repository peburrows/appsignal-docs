---
title: "Jira"
---

[JIRA](https://www.atlassian.com/software/jira)  is the tracker for teams planning and building great products.

## Setup

Before we can link AppSignal to JIRA we need to configure JIRA to accept our OAuth request.
On the "Add-ons" screen, look for the "Application links" page and enter: <code>https://appsignal.com</code> in the field for a new Application Link.

If you receive a "No response was received from the URL you entered" error, click "continue".

### Application details

In the modal, only fill out the Application Name.

![setup](/images/screenshots/jira/setup.png)

After saving the integration, click "Edit" and go to the "Incoming Oauth" tab in the pop-up.

### OAuth setup

In the "Incoming OAuth" tab, setup OAuth with the following values:

* Consumer Key: <code>7668c385bf3f09c0219ec178a50ff736</code>
* Consumer Name: <code>AppSignal</code>
* Public Key:

<pre><code>
-----BEGIN CERTIFICATE-----
MIICsDCCAhmgAwIBAgIJAJGEAsDfksoFMA0GCSqGSIb3DQEBBQUAMEUxCzAJBgNV
BAYTAkFVMRMwEQYDVQQIEwpTb21lLVN0YXRlMSEwHwYDVQQKExhJbnRlcm5ldCBX
aWRnaXRzIFB0eSBMdGQwHhcNMTUwNDAyMTIyMDEwWhcNMTUwNTAyMTIyMDEwWjBF
MQswCQYDVQQGEwJBVTETMBEGA1UECBMKU29tZS1TdGF0ZTEhMB8GA1UEChMYSW50
ZXJuZXQgV2lkZ2l0cyBQdHkgTHRkMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKB
gQCoo3PzC3CIWEqXlLb7NDKRLqxp/M/88RevoOMAJq/UG/q4fJlhOkVoHK86IxV1
eZ+p6m1cqizWmnGp3KSqU1y+jhKj6+XqhPHfyXFOrRAGr//UGcIIXycPf5mKLftr
3PQ8F+Bloq7UGhkahtVP+u3CUcde6TCTGWYvAI47jVdyHwIDAQABo4GnMIGkMB0G
A1UdDgQWBBQYN5ucG+T1xBsPHkDSKKMg8KP1YDB1BgNVHSMEbjBsgBQYN5ucG+T1
xBsPHkDSKKMg8KP1YKFJpEcwRTELMAkGA1UEBhMCQVUxEzARBgNVBAgTClNvbWUt
U3RhdGUxITAfBgNVBAoTGEludGVybmV0IFdpZGdpdHMgUHR5IEx0ZIIJAJGEAsDf
ksoFMAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEFBQADgYEANgUypdXrA41lt2P1
atA+hxT1jtmYeIyUbVrsS2K9HYZpdijHVXZGHdaf2c56x2d9O8uOpwsbQT+09WNR
KD4RHjXJPVCYaY1QFk0FHM+5yooD2H3XwNyzCWgSZZ1nc6ZuLXJKY8jtXH9cs0wZ
utPOCXiudsot4u0pDWHLfsBQr+o=
-----END CERTIFICATE-----

</code></pre>

<p></p>
And save the OAuth configuration.

## Errors

AppSignal requires a number of fields to be present when the integration is activated. If fields are missing, we will show an error message during the setup.

Required fields are:

* Labels
* Environment
* Summary
* Description

In order to fix these messages, you have to add the fields to the JIRA screens by going to the "issues" tab in settings.

Then navigate to "screens".
![screens](/images/screenshots/jira/screens.png)

And click "configure" for the screen that has been selected in AppSignal.

Add the required fields described above.

![fields](/images/screenshots/jira/fields.png)

Getting Jira working can be a bit of a hassle, but it should work if you follow the above steps with care. If you run into any issues, don't hesitate to contact us at <a href="mailto:contact@appsignal.com">contact@appsignal.com</a> if other errors occur.
