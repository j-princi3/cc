| Method                    | Used For            | MFA Support | Best For                          |
| ------------------------- | ------------------- | ----------- | --------------------------------- |
| Access Keys               | Programmatic access | ❌           | CLI/SDK                           |
| IAM User + Password       | Console login       | ✅           | Admins, limited human users       |
| IAM Role                  | AWS services        | N/A         | EC2, Lambda, ECS, etc.            |
| MFA (TOTP/FIDO2)          | Extra login factor  | ✅           | Secure IAM logins                 |
| Passkeys (WebAuthn)       | MFA / Passwordless  | ✅           | Phishing-resistant authentication |
| AWS SSO / Identity Center | Central login       | ✅           | Organizations with many users     |
