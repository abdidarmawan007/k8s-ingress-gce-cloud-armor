# GKE Ingress GCE + WAF Cloud Armor
Ingress GCE with WAF Cloud Armor: SQL injection,Cross-Site Scripting (XSS),Local file inclusion (LFI),Remote Code Execution (RCE),Remote File Inclusion (RFI)


![alt text](https://i.imgur.com/Kw2mxrs.png)


### Create Cloud Armor WAF
```
gcloud compute --project=zeus-007 security-policies create production-frontend-waf --description="production frontend waf"


gcloud compute --project=zeus-007 security-policies rules create 1000 --action "deny-403" --security-policy=production-frontend-waf --description="SQL injection" --expression=evaluatePreconfiguredExpr\(\'sqli-stable\',\ \[\'owasp-crs-v030001-id942251-sqli\',$'\n'\ \'owasp-crs-v030001-id942420-sqli\',$'\n'\ \'owasp-crs-v030001-id942431-sqli\',$'\n'\ \'owasp-crs-v030001-id942460-sqli\',$'\n'\ \'owasp-crs-v030001-id942421-sqli\',$'\n'\ \'owasp-crs-v030001-id942200-sqli\',$'\n'\ \'owasp-crs-v030001-id942260-sqli\',$'\n'\ \'owasp-crs-v030001-id942432-sqli\'\]$'\n'\)

gcloud compute --project=zeus-007 security-policies rules create 1001 --action "deny-403" --security-policy=production-frontend-waf --description=Cross-Site\ Scripting\ \(XSS\) --expression=evaluatePreconfiguredExpr\(\'xss-stable\',\ \[\'owasp-crs-v030001-id941150-xss\',$'\n'\ \'owasp-crs-v030001-id941320-xss\',$'\n'\ \'owasp-crs-v030001-id941330-xss\',$'\n'\ \'owasp-crs-v030001-id941340-xss\'\]\)

gcloud compute --project=zeus-007 security-policies rules create 1002 --action "deny-403" --security-policy=production-frontend-waf --description=Local\ file\ inclusion\ \(LFI\) --expression=evaluatePreconfiguredExpr\(\'lfi-stable\',\ \[\'owasp-crs-v030001-id930120-lfi\'\]\)

gcloud compute --project=zeus-007 security-policies rules create 1003 --action "deny-403" --security-policy=production-frontend-waf --description=Remote\ Code\ Execution\ \(RCE\) --expression=evaluatePreconfiguredExpr\(\'rce-stable\'\)

gcloud compute --project=zeus-007 security-policies rules create 1004 --action "deny-403" --security-policy=production-frontend-waf --description=Remote\ File\ Inclusion\ \(RFI\) --expression=evaluatePreconfiguredExpr\(\'rfi-stable\',\ \[\'owasp-crs-v030001-id931130-rfi\'\]\)
```

### Create IP public for ingress gce and ssl managed by google cloud
```
gcloud beta compute --project=zeus-007 addresses create production-frontend-zeus --global --network-tier=PREMIUM
gcloud beta compute ssl-certificates create production-frontend-zeus --domains www.yourdomain.com --global
```

### Create GKE deployment,service,configmap,hpa,ingress gce
```
kubectl apply -f deployment.yaml
```

### Test SQL Injection
```
curl -I https://www.yourdomain.com/?id=1%20or%201=1
403 Forbidden
```
### Test Local File Inclusion (LFI)
```
curl -I http://abdi.lemonilo.com/?file=../../../../etc/passwd
403 Forbidden
```
```
curl -I http://abdi.lemonilo.com/.git/HEAD
403 Forbidden
```
```
curl -I http://abdi.lemonilo.com/.env
403 Forbidden
```

```
python waf-test.py http://www.yourdomain.com

## WAF Testing on http://www.yourdomain.com ##
  Total requests:  1,019 requests
Success requests:  1,019 requests
 Failed requests:      0 requests
Allowed requests:    249 requests
Blocked requests:    770 requests
```
#### tools for testing waf
```
https://github.com/page2me/waf-test
```
