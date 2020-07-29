# GKE Ingress GCE + WAF Cloud Armor
Ingress GCE with WAF Cloud Armor: SQL injection,Cross-Site Scripting (XSS),Local file inclusion (LFI),Remote Code Execution (RCE),Remote File Inclusion (RFI)


![alt text](https://i.imgur.com/Kw2mxrs.png)


### Create Cloud Armor WAF
```
gcloud compute --project=zeus-007 security-policies create production-waf --description="production waf for gke"

gcloud compute --project=zeus-007 security-policies rules create 1000 --action "deny-403" --security-policy=production-waf --description="XSS Sensitivity Level 2/3/4" --expression=evaluatePreconfiguredExpr\(\'xss-stable\'\)

gcloud compute --project=zeus-007 security-policies rules create 1002 --action "deny-403" --security-policy=production-waf --description="SQLi Sensitivity Level 4" --expression=evaluatePreconfiguredExpr\(\'sqli-stable\'\)

gcloud compute --project=zeus-007 security-policies rules create 1003 --action "deny-403" --security-policy=production-waf --description="LFI Sensitivity Levels 1/2/3/4" --expression=evaluatePreconfiguredExpr\(\'lfi-canary\'\)

gcloud compute --project=zeus-007 security-policies rules create 1004 --action "deny-403" --security-policy=production-waf --description="RCE Sensitivity Levels 1/2/3/4" --expression=evaluatePreconfiguredExpr\(\'rce-canary\'\)

gcloud compute --project=zeus-007 security-policies rules create 1005 --action "deny-403" --security-policy=production-waf --description="RFI Sensitivity Level 2/3/4" --expression=evaluatePreconfiguredExpr\(\'rfi-canary\'\)
```
![alt text](https://i.imgur.com/mWume3H.png)


### Create IP public for ingress gce and ssl managed by google cloud
```
gcloud beta compute --project=zeus-007 addresses create production-frontend-zeus --global --network-tier=PREMIUM
gcloud beta compute ssl-certificates create production-frontend-zeus --domains www.yourdomain.com --global
```
