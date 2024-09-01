<h1>Email Authentication Methods (Hands On)</h1>

<h2>Sender Policy Framework (SPF)</h2>

Sender Policy Framework (SPF) is an email authentication protocol that helps prevent email spoofing by allowing domain owners to specify which mail servers are authorized to send emails on behalf of their domain.

<b>How SPF Works:</b>

1. <b>DNS Record:</b> The domain owner publishes an SPF record in the DNS, listing authorized IP addresses or hostnames for sending emails.
2. <b>Verification:</b> When an email is received, the receiving server checks the SPF record to verify if the sender's IP address is authorized.
3. <b>Evaluation:</b> The SPF check can result in:

- <b>Pass:</b> The IP is authorized
- <b>Fail:</b> The IP is not authorized
- <b>SoftFail:</b> The IP is not authorized, but the email is not necessarily malicious.
- <b>Neautrol</b> or <b>None:</b> The SPF record does not provide clear authorization.

4. <b>Action:</b> Based on the result, the receiving server decides to accept, reject, or flag the email.

<b>Example SPF Record:</b>

Using the command "<b>nslookup -type=txt shodan.io | grep -i spf</b>" in our terminal shows the following

<img src="https://i.imgur.com/bvD19Fm.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

- "<b>v=spf1</b>" indicates the SPF version.
- "<b>ip4</b>" specifies authorized IP ranges.
- "<b>include</b>" allows emails from servers listed in "<b>_spf.google.com</b>".
- "<b>-all</b>" denotes that all other servers are unauthorized.

<b>Benefits and Limitations:</b>

- <b>Benefits:</b> Prevents spoofing, reduces spam, and improves email deliverability.
- <b>Limitations:</b> Does not validate email content, can fail with email forwarding, and requires regular maintenance.

<img src="https://i.imgur.com/aDaRq4D.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

<h2>DomainKeys Identified Mail (DKIM)</h2>

DomainKeys Identified Mail (DKIM) is an email authentication method that allows the receiver to verify that an email was sent by an authorized sender and that the email content has not been altered during transit. DKIM uses digital signatures to ensure the integrity and authenticity of emails.

<b>How DKIM Works:</b> 

1. <b>Private Key Signing:</b> The sender’s mail server uses a private cryptographic key to create a unique digital signature for each outgoing email. This signature is added to the email's header.
2. <b>DNS Public Key:</b> The sender publishes a corresponding public key in the Domain Name System (DNS) as a TXT record. This public key allows receiving servers to verify the email's signature.
3. <b>Signature Verification:</b> When an email is received, the recipient’s mail server retrieves the sender’s public key from the DNS. It uses this key to verify the digital signature in the email header.
4. <b>Validation:</b> If the signature matches and verifies the email content has not been altered, the email is considered authentic. If the signature does not match or is missing, the email may be flagged, rejected, or marked as suspicious.

<b>Benefits of DKIM:</b>

- <b>Prevents Email Tampering:</b> Ensures that the email content has not been modified in transit.
- <b>Improves Email Deliverability:</b> Emails signed with DKIM are less likely to be marked as spam.
- <b>Enchances Trust:</b> Validates the sender’s identity, enhancing trust in the email.

<b>Limitations of DKIM:</b> 

- <b>Does Not Prevent Spoofing Alone:</b> DKIM does not verify the "From" header address; it only ensures the email content's integrity and authenticity.
- <b>Requires COrrect Configuration:</b> Both sending and receiving servers need proper configuration for DKIM to be effective.
- <b>Dependent on DNS:</b> Public keys must be maintained and accessible via DNS, making it dependent on reliable DNS management.

<img src="https://i.imgur.com/Tt4cVpU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

<h2>Domain-based Message Authentication, Reporting, and Conformance (DMARC)</h2>

Domain-based Message Authentication, Reporting, and Conformance (DMARC) is an email authentication protocol that builds on SPF (Sender Policy Framework) and DKIM (DomainKeys Identified Mail). DMARC helps domain owners protect their domains from unauthorized use, such as phishing and email spoofing, by providing a mechanism to instruct receiving mail servers on how to handle emails that fail SPF or DKIM checks.

<b>How DMARC Works:</b> The domain owner publishes a DMARC policy in the DNS as a TXT record. This policy specifies:

1. <b>Policy Definition:</b> The domain owner publishes a DMARC policy in the DNS as a TXT record. This policy specifies:

- How to handle emails that fail SPF or DKIM checks ('<b>none</b>', '<b>quarantine</b>', or '<b>reject</b>').
- The percentage of emails to which the policy should be applied.
- An email address where aggregate and forensic reports should be sent.

2. <b>Email Evaluation:</b> When an email is received, the recipient's mail server checks the DMARC policy of the sender's domain. It evaluates the email based on SPF and DKIM results and determines if they align with the "From" domain.
3. <b>Alignment Check:</b> DMARC requires that the domain in the "From" header matches the domain in either the SPF or DKIM results. This is called "alignment." If there is no alignment, the email is considered to have failed the DMARC check.
4. <b>Policy Enforcement:</b> Based on the DMARC policy, the receiving server decides what action to take for emails that fail the DMARC check:

- <b>None:</b> No action is taken; the email is delivered as usual.
- <b>Quarantine:</b> The email is marked as spam or moved to the junk folder.
- <b>Rejected:</b> The email is rejected outright and not delivered to the recipient.

5. <b>Reporting:</b> DMARC provides domain owners with reports on how their emails are being processed. These reports include information on emails that pass or fail DMARC checks, helping domain owners monitor their email authentication and detect potential abuse.

<b>Example of a DMARC Record:</b>

"v=DMARC1; p=reject; rua=mailto:dmarc-reports@example.com; ruf=mailto:forensics@example.com; adkim=s; aspf=s; pct=100;"

- "<b>v=DMARC1</b>" specifies the DMARC version.
- "<b>p=reject</b>" sets the policy to reject emails that fail DMARC checks.
- "<b>rua</b>" specifies the email address for receiving aggregate reports.
- "<b>ruf</b>" specifies the email address for receiving forensic reports.
- "<b>adkim=s</b>" and "<b>aspf=s</b>" enforce strict alignment for DKIM and SPF, respectively.
- "<b>pct=100</b>" applies the policy to 100% of the emails.

<b>Benefits of DMARC:</b>

- <b>Prevents Email Spoofing:</b> Protects against phishing and spoofing attacks by ensuring that only legitimate emails are delivered.
- <b>Provides Visibility:</b> Gives domain owners insights into who is sending emails on their behalf and how their domain is being used.
- <b>Enchances Security:</b> When used with SPF and DKIM, DMARC strengthens the overall security posture of email communications.

<b>Limitations of DMARC:</b>

- <b>Complex Configuration:</b> Requires proper configuration of SPF and DKIM, along with DNS records.
- <b>Potential Delivery Issues:</b> Strict DMARC policies can lead to legitimate emails being rejected if not configured correctly.
- <b>Needs Regular Monitoring"</b> Domain owners must regularly review DMARC reports to ensure ongoing protection and address any issues.

<h1>Applying SPF, DKIM, and DMARC</h1>

<h2>Email Inspection</h2>

We can see here we have an email that claims to be from Namecheap Renewals, and Namecheap is the domain registrar. It looks like we're getting some sense of urgancy here because we only have 7 days left to renew our domain. So let's open this up in Sublime Text.

<img src="https://i.imgur.com/DDJdIo2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br> 

<h2>Sublime Text</h2>


The IP address that we can Identify here is 149.72.142.11, so this is the "Recieved" header that tells us when the message left the sender's hands, or who the sender claimed to be, and into our truted infrastructure, in this case Google's mail servers. So we can genuinely trust that this IP address relates to our sender because it can't be spoofed. There was a genuine TCP connection that was made between these two servers.

<img src="https://i.imgur.com/7sJY6Iw.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br> 

<h2>Domain Tools (https://whois.domaintools.com/)</h2>

If we perform a lookup on this IP address, we can see that it actually returns as "<b>SENDGRID</b>", which is a very common email marketing solution, which in theory cloud be abused, but we can also see that the reverse DNS of this mail server which does in fact return as "<b>Namecheap.com</b>". So now that we know the original sender and mail server, lets look at the authentication checks

<img src="https://i.imgur.com/aJ5pzUe.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br> 

<h2>Authentication Checks</h2>

1. <b>Received-SPF</b>

We can manually check the SPF records ourselves and mimic what Google did here on the back end!

<img src="https://i.imgur.com/Rawyh1C.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br> 

Using the command "<b>nslookup -type=txt mailserviceemailout1.namecheap.com | grep -i spf</b>" in our terminal shows the following

<img src="https://i.imgur.com/8VakRm7.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br> 

If we look at all the records designated by "<b>sendgrid.net</b>". 

Using the command "<b>nslookup -type=txt sendgrid.net | grep -i spf</b>" in our terminal shows the following

<img src="https://i.imgur.com/5GzV5nF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br> 

Well we can see a number of IP address ranges published as authorized senders in their SPF record. Specifically, we can see the block here of "<b>149.72.0.0/16</b>" subnet. This is a CIDR block containing all the IP addresses in the range of 149.72.0.0 all the way to 149.72.255.255. And of course, the IP address of the original sender that we identified earlier and the one that our email server used to check SPF is within this range, meaning it passed the check.

2. <b>DKIM-Signiture</b>

We can see we have two DKIM-Signiture headers here, one for "<b>d=namecheap.com</b>" and the other for "<b>d=sendgrid</b>". Both signitures seem to be using the same body hash and including the same set of header fields, but they will come from different domains and selectors. Since we're looking for integrity with DKIM, well let's focuse on the signiture closest to the source, or the Namecheap one, and break down its components.

<img src="https://i.imgur.com/IoKAsch.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br> 

- <b>v= (Version):</b> Indicates the version of the DKIM protocol (usually <b>v=1</b>)
- <b>a= (Algorithm):</b> Specifies the signing algorithm used. Commonly, <b>rsa-sha256</b>.
- <b>c= (Canonicalization):</b> Describes the canonicalization algorithms for the header and body. <b>relaxed/relaxed</b> means that whitespace and header field names are normalized to make the signature more robust.
- <b>d= (Domain):</b> The domain of the signer (<b>namecheap.com</b>).
- <b>s= (Selector):</b> Identifies the selector used for the DNS query to retrieve the public key.
- <b>nh= (Body Hash):</b> The hash of the canonicalized body content, allowing detection of body modifications.
- <b>b = (Signature Data):</b> The actual base64-encoded digital signature.

So now that we know the domain and the selector, we can actually manually check the public key oursleves through DNS.

To do this, the syntax will be the following "<b>nslookup -type=txt s1._domainkey.namecheap.com</b>"

<img src="https://i.imgur.com/mpiqnjq.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br> 

We can see there is canonical name set to this "<b>sendgrid.net</b>" domain, but we can also see the contents of the public key (<b>p=</b>) itself.

<h2>Mx Toolbox (mxtoolbox.com/dkim.aspx)</h2>

If we remember, we had the domain name which was <b>namecheap.com</b>, and we had the selector or <b>s1</b>. 

<img src="https://i.imgur.com/KbsL8OT.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br> 

And if we do a DKIM lookup, well we can get the same results

<img src="https://i.imgur.com/t8i7Nzn.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

Now if we go to the <b>Analyze Headers</b> section and we copy all the contents of our email in Sublime Text and paste it in, we can analyze the header which will perform some DKIM checks for us.

<img src="https://i.imgur.com/FIsFTXX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br> 

<img src="https://i.imgur.com/zpZQSD1.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br>

<img src="https://i.imgur.com/RAdGgZu.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br>

If we scroll down to <b>dkim:namecheap.com:s1</b>, well this was the DKIM signature that we were analyzing manually.

<img src="https://i.imgur.com/pdWY1Fs.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br>

If we click on "<b>Show</b>", we can see all of the checks that were performed.

<img src="https://i.imgur.com/mhAj96O.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br>

We can see that the DKIM record was found and that the record was valid. We can see that the public key was present, which of course we verified manually, but that the signature was also valid. We can see that the signature domain matched and there was alignment with the signature in the domain. So alignment is when DKIM signing domain matches the header from domian. In this case, it was <b>namecheap.com</b>, so it does check out. We notice that the body hash did not verify. This is something we want to make note of because it means something in the contents of the email message were changed, obviously breaking integrity. However, in this case it's actually expected here because for the course, they had to manually redact some identifiable information like the original recipient. So we don't have to worry about the body hash check for this example.

<h2>Final Analysis</h2>

So after all the analysis we done, was this email real or was it a phishing attempt?

<img src="https://i.imgur.com/DDJdIo2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br>

Well, if not obvious already with all our checks, it was in fact a legitimate email.

I will stress again that these email authentication checks are not a silver bullet for determining the legitimacy of an email. They simply confirm that the email has passed through certain authentication protocols like SPF and DKIM. While passing these checks is a positive sign, it never guarentees that the email is completely safe or genuine.

<b>THIS WAS FOR EDUCATIONAL PURPOSES ONLY</b>
