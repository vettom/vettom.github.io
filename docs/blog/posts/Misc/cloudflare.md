---
draft: false 
date: 2024-08-28
authors: ["vettom"]
categories:
  - Cloudflare
  - terraform
  - cdn
---

# CloudFlare: Partial zone signup not allowed (1104) terraform 

If you are enterprise customer, you can make use of CloudFlare Partial domain setup maintain your own DNS servers. Though name suggests `partial DNS` it is creaion of a proxy host and you assign CNAME pointint to it. 

When you try to create partial Zone using terraform provider you may end up with below error message. 

```bash
cloudflare_zone.testhellorayozone: Creating...
 Error: error creating zone "vettom.online": Partial zone signup not allowed (1104)

   with cloudflare_zone.testhellorayozone,
   on main.tf line 19, in resource "cloudflare_zone" "testhellorayozone":
   19: resource "cloudflare_zone" "testhellorayozone" {
``` 

### Solution
This is an error from CloudFlare provider. It seems they have to enable/allow terraform partial Zone creation using Terraform

- Raise support ticket with Cloudflare to allow terrafor Partial Zone creation
- Ensure you are using `Global API Key` for authentication