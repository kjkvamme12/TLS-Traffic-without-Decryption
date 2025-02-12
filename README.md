# Analyzing TLS Traffic without Decryption

## Objective

- Dissect TLS traffic to make an informed judgement on if it is malicious or not, without decryption.
- Find the domain name that is being visited in multiple ways for encrypted TLS.
- ANalyze certificate fields in order to spot anomalous TLS traffic.
- Spot self-signed certificates using the subject and issuer fields from a parsed certificate.
- Use JA3 hashes to fingerprint TLS for malicious connection detection.
- Use Zeek and TShark to perform these tasks at scale and from the command line.
  
### Skills Learned
[Bullet Points - Remove this afterwards]

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used

- Wireshark

## Steps

Even if a connection is not decrypted, we can still have access to certain data to make an informed decision if connection is malicious or not. 

### Finding Domain and Certificate Details in TLS Communication


One way of finding the domain is the field for
#### Server Name Indication 
This field is present in all modern TLS including the new TLS1.3. You can find it in the portion of a TLS handshake that has "Client Hello" in the Info column. This will enable you to see the domain name associated with the connection being established. 

A quick way to see all the domain fields for TLS using the SNI field si to apply the following filter


![Screenshot 2025-02-05 150015](https://github.com/user-attachments/assets/0c8aa84f-3ef5-4357-8e37-53a911425ede)

This filters to Client Hello packets specifically. 

<img width="827" alt="Screenshot 2025-02-05 150310" src="https://github.com/user-attachments/assets/40bb47f3-2b26-4dbc-a962-bb51aa8c6a01" />


Now selecting a packet go into packet details and unfold the layers until we view the Handshake Protocol: Client Hello. This shows us the extensions. 
Find the extension that is server_name , in there you will find the Server Name Indication Extension, which contains the domain. 

<img width="886" alt="Screenshot 2025-02-05 151157" src="https://github.com/user-attachments/assets/ea2e939c-a339-422a-ac93-060710649238" />



![Screenshot 2025-02-05 151229](https://github.com/user-attachments/assets/8cbd7e6d-7f47-4963-b655-0c09675c2b60)


Now by applying as a column we can view all domain names associated with each connection. This will only be available in the Client Hello packets through. If we expand the full TLS list the column will not apply to everything. 


### Analyzing Certificate Fields and Identifying Self-Signed Certificates. 

The following filter will allow to see details for a TLS certificate in Wireshark .

![Screenshot 2025-02-05 151543](https://github.com/user-attachments/assets/3ed99169-c793-4e93-9bf0-41d65e17851b)
![Screenshot 2025-02-05 151635](https://github.com/user-attachments/assets/26de4fb4-435d-4cb5-b1af-fae020512c20)

Now only packets that are part of a TLS handshake where a certificate was transferred will be displayed. 


