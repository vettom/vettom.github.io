---
draft: false 
date: 2024-08-28
authors: ["vettom"]
categories:
  - Cloudflare
  - terraform
  - cdn
---

# CloudFlare: Partial zone sign-up not allowed (1104) terraform 

If you are enterprise customer, you can make use of CloudFlare Partial domain setup maintain your own DNS servers. Though name suggests `partial DNS` it is creation of a proxy host and you assign CNAME pointint to it. 

When you try to create partial Zone using terraform provider you may end up with below error message. 

```bash
cloudflare_zone.testvettomzone: Creating...
 Error: error creating zone "vettom.online": Partial zone signup not allowed (1104)

   with cloudflare_zone.testvettomzone,
   on main.tf line 19, in resource "cloudflare_zone" "testvettomzone":
   19: resource "cloudflare_zone" "testvettomzone" {
``` 

### Solution
This is an error from CloudFlare provider. It seems they have to enable/allow terraform partial Zone creation using Terraform

- Raise support ticket with Cloudflare to allow terraform Partial Zone creation
- Ensure you are using `Global API Key` for authentication
- Must have `Enterprise` plan, not available for other plans

```terraform
resource "cloudflare_zone" "zone" {
  account_id = var.accountid
  zone       = var.domain
  type       = "partial"
  plan       = "enterprise"
}
```