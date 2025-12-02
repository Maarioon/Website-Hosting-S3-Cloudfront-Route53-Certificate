  # Website-Hosting-S3-Cloudfront-Route53-Certificate

  
## Step 1: Create an S3 Bucket for Website Hosting

1. Go to **S3** → **Create bucket**
   - Bucket name: Must be exactly the same as your desired domain  
     Example: `www.example.com` (or `example.com` if you don’t use www)
   - Region: Choose **US East (N. Virginia) us-east-1**  
     → Required because ACM certificates for CloudFront must be in us-east-1
   - Uncheck “Block all public access” → Click the acknowledgment → Create bucket

2. Configure the bucket for static website hosting
   - Open the bucket → **Properties** tab → Scroll to **Static website hosting** → Edit
   - Choose **Enable**
   - Hosting type: **Host a static website**
   - Index document: `index.html`
   - Error document: `error.html` (optional)
   - Save changes
   - Note the **Bucket website endpoint** (you’ll test it later)

Note::
This is not compulsory if the bucket will be linked to a Cloudfront distribution

Upload your website filesGo to Objects tab → Upload → Add your files/folders
Important files: index.html (required), error.html (recommended)

(Optional) Test the S3 website endpoint
Open the URL shown in Static website hosting (e.g., http://www.example.com.s3-website-us-east-1.amazonaws.com) → You should see your site (HTTP only for now)

Step 3: Create CloudFront DistributionStill in us-east-1 region.Go to CloudFront → Create distribution
Origin domain: Select your S3 bucket (www.example.com)
→ Do NOT choose the static website endpoint here
Origin access: Choose Origin access control settings (recommended)Click Create control setting → Name it → Save
Then click Copy policy → Go back to your S3 bucket → Bucket policy → Paste and replace the old policy → Save
(This restricts direct S3 access — only CloudFront can read)
<img width="1812" height="841" alt="Screenshot 2025-11-30 173250" src="https://github.com/user-attachments/assets/bbe1ba83-4acf-4e4b-9a6d-360ac486c7f7" />

<img width="1920" height="801" alt="Screenshot 2025-11-30 173320" src="https://github.com/user-attachments/assets/208dc322-53d3-4a96-a7cb-d9c60608f34e" />

<img width="1910" height="832" alt="Screenshot 2025-11-30 173421" src="https://github.com/user-attachments/assets/0c57a137-445d-4af9-91ca-169d66c9c1ff" />

<img width="1905" height="818" alt="Screenshot 2025-11-30 173509" src="https://github.com/user-attachments/assets/3c786767-2bfd-4f6a-96ec-6bd88c321188" />

<img width="1920" height="830" alt="Screenshot 2025-11-30 173717" src="https://github.com/user-attachments/assets/6679fe83-6d51-4882-b5b0-374dabd7011a" />

Under Origin click on  Origin ccess control which is recommended
copy the bucket policy, this will be pasted in the S3bucket policy
<img width="1811" height="832" alt="Screenshot 2025-11-30 175228" src="https://github.com/user-attachments/assets/1fd22a7c-3e09-4705-81e7-004e2886075a" />

<img width="1909" height="811" alt="Screenshot 2025-11-30 175340" src="https://github.com/user-attachments/assets/0b0de604-3ae1-4210-b0f0-0379ae95b609" />

<img width="1920" height="850" alt="Screenshot 2025-11-30 175349" src="https://github.com/user-attachments/assets/cde0339e-c02d-4223-b1c3-b4effb025e54" />

After creating click on the distribution and adjust some settings
under general define the default root opject to index.html
<img width="1920" height="830" alt="Screenshot 2025-11-30 173717" src="https://github.com/user-attachments/assets/ceb17077-c015-49be-88e9-89da2e76f368" />

under behavior select the http service you want, choose https
<img width="1920" height="761" alt="Screenshot 2025-11-30 175030" src="https://github.com/user-attachments/assets/f0189bf2-4e5a-4ea4-960c-536b2b7f1db5" />

<img width="1920" height="820" alt="Screenshot 2025-11-30 175055" src="https://github.com/user-attachments/assets/fc77d21d-79b0-45f6-8a8e-a3cbd9917fb3" />

<img width="1871" height="803" alt="Screenshot 2025-11-30 175529" src="https://github.com/user-attachments/assets/7f3d272e-7e1e-41f1-9958-1eed9f415c2b" />

After everything is configured click on the distribution link and paste in tab to visit link. It will redirect to the website page 

<img width="1913" height="941" alt="Screenshot 2025-11-30 175825" src="https://github.com/user-attachments/assets/e0af6724-49a7-468e-8b27-ce865992b528" />

Viewer protocol policy: Redirect HTTP to HTTPS
Alternate domain name (CNAME): Addwww.example.com
example.com (optional)

Custom SSL certificate: Choose the ACM certificate you created
Default root object: index.html
Price class: Use only selected edge locations (or All for maximum speed)
WAF: Skip (or enable if needed)
Leave everything else default → Create distribution

Wait 5–15 minutes for distribution to deploy (Status: In Progress → Deployed)Note the Distribution domain name (e.g., d1234567890.cloudfront.net)Step 4: Configure Route 53 for Your DomainGo to Route 53 → Hosted zones → Select your domain (or create one)
Create two A records (for naked domain + www)

Record 1: Naked domain (example.com) → CloudFrontClick Create record
Record name: Leave blank (or @)
Record type: A – IPv4 address
Alias: Enable
Route traffic to: Alias to CloudFront distribution
Choose your distribution (from the dropdown)
Save

Record 2: www.example.com → CloudFrontRecord name: www
Record type: A
Alias: Enable
Alias to CloudFront distribution → Choose same distribution
Save

Alternative (if you prefer redirect www → non-www or vice versa):You can create a second S3 bucket + CloudFront for redirect (common pattern), but the above is simpler.

Step 5: Final TestingWait for DNS propagation (up to 48 hours, usually minutes with Route 53)
Open:https://example.com
https://www.example.com

A certificate can be added to the distribution. you can link a certificate you already created or create one through through the redirection link, A certificate has to be added to a domain name, this can be done by creating a domain through Route53 or from another provider and link it to Route53

<img width="1917" height="774" alt="Screenshot 2025-11-30 174400" src="https://github.com/user-attachments/assets/eb9b2635-2f1b-4e8e-95ca-37a91d227da2" />

<img width="1562" height="755" alt="Screenshot 2025-11-30 174433" src="https://github.com/user-attachments/assets/beaa922b-1044-4e9e-83d7-f2b7fc58bfbb" />

<img width="1920" height="709" alt="Screenshot 2025-11-30 174804" src="https://github.com/user-attachments/assets/4ca2b792-7e07-4ddc-9aee-ba66f8c10a9b" />

After that go back to the certificate and create a certificate
<img width="1592" height="416" alt="Screenshot 2025-11-30 174411" src="https://github.com/user-attachments/assets/81778033-7b63-4a54-af9b-6f0b696cbda1" />
