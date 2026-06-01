1. **Start by pulling the regex from a specific Event Code**

- In our case we will use: 4648 - Logon with explicit credentials. 

```PowerShell
index=botsv1 AND EventCode=4648

| rex field=Message "(?ms)Account Whose Credentials Were Used:\n\s*Account Name:\s*(?<Credential_Account>\s*(.*?)$)"

| search Credential_Account!="*$" 

| stats count by Credential_Account
```