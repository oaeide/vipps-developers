# Vipps SFTP Report Service

## Settlements

Settlements are created every day, but only as long as the balance is positive.
In other words, if the balance for a day is zero (e.g. due to lack of
transaction) or negative (e.g. due to refunds), a settlement will not be created
until the balance becomes positive. This means that a settlement report may in
some cases include transactions spanning several days back in time.

## Reports

Settlement reports should be available by 12:00 on the day after the
transactions were made, as long as a settlement were created (i.e. the balance
were positive). There will never be more than one new file per. sales unit each
day (and there may be none).

Note that the reports are generated on-demand, which is why the file size is
reported as zero (the size is unknown at the time of listing).

See [the general information about settlements and reports](../) for information
about the report formats, availability of personal information about customers,
GDPR, etc.

## SFTP Service

The SFTP report service is used for downloading settlement reports in the following formats:

* [XML](../xml/)
* [CSV](../csv/)
* [PDF](../pdf/)
* [Excel](../xslx) (.xslx)

SFTP-users are created, associated with a public key, and given access to the reports of
one or more merchants. 

More information about SFTP: [SSH File Transfer Protocol](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol).

### How to set up users for connecting to the SFTP server

1. Login to https://portal.vipps.no and click `Bedriftsinfo` under your Bedrift and Organization name.

![Velg profil](images/01_velg_profil.png "Velg profil")

2. Click the link under the information to add users for SFTP-access.

![Lag tilgang](images/02_SFTP_tilgang.png "Lag tilgang")

3. In the next window you can add the public keys of the user(s). We support RSA (minimum 2048-bit), EdDSA and Ed25519 keys in OpenSSH format (and reject DSA keys). After this you should see the newly created user. For help creating SSH keys, the GitHub documentation may be helpful: https://help.github.com/articles/connecting-to-github-with-ssh/

![Bruker opprettet](images/03_bruker_opprettet.png "bruker opprettet")

### Connecting to the SFTP server

The address of the SFTP server is `sftp.vipps.no`.

This is the directory structure:
```
/settlements/[inbox|archive]/[file extension]/[orgnum]/[sales unit serial]/[sales unit serial]-[settlement number].[file extension]
```

### How to use it

Reports under `/settlements/inbox` can be "deleted" (actually hidden) 
in order to keep track of already processed reports. 

Reports are deleted by using the `rm` command in SFTP or the delete function in your SFTP interface. 

Reports under `/settlements/archive` cannot be removed.

#### Example SFTP session

Below is an example of an SFTP session, with line breaks added for readability.

```
$ sftp sftp.vipps.no
Connected to sftp.vipps.no.

sftp> ls
settlements  

sftp> ls settlements
settlements/archive   
settlements/inbox     

sftp> ls settlements/inbox
settlements/inbox/csv        
settlements/inbox/pdf        
settlements/inbox/xlsx       
settlements/inbox/xml        
settlements/inbox/xml.zip    

sftp> ls settlements/inbox/xml
settlements/inbox/xml/998724341   

sftp> ls settlements/inbox/xml/998724341
settlements/inbox/xml/998724341/16655    

sftp> ls settlements/inbox/xml/998724341/16655
settlements/inbox/xml/998724341/16655/16655-2000001.xml             
settlements/inbox/xml/998724341/16655/16655-2000002.xml            
settlements/inbox/xml/998724341/16655/16655-2000003.xml             
settlements/inbox/xml/998724341/16655/16655-2000004.xml             

sftp> mget settlements/inbox/xml/998724341/16655/*
Fetching /settlements/inbox/xml/998724341/16655/16655-2000001.xml to 16655-2000001.xml
Fetching /settlements/inbox/xml/998724341/16655/16655-2000002.xml to 16655-2000002.xml
Fetching /settlements/inbox/xml/998724341/16655/16655-2000003.xml to 16655-2000003.xml
Fetching /settlements/inbox/xml/998724341/16655/16655-2000004.xml to 16655-2000004.xml

sftp> !ls -1
16655-2000001.xml
16655-2000002.xml
16655-2000003.xml
16655-2000004.xml
sftp> quit
```

## Questions?

We're always happy to help with code or other questions you might have!
Please create an [issue](https://github.com/vippsas/vipps-developers/issues),
a [pull request](https://github.com/vippsas/vipps-developers/pulls),
or [contact us](https://github.com/vippsas/vipps-developers/blob/master/contact.md).
