```bash
| rex field=my_field "<REGEX STATEMENT>"
```

# Playing with multiline values:
Using regex to extract values from Windows Event Logs message blocks.

- Multiline AND Single line together with `(?ms)`


```PowerShell
index=botsv1 AND sourcetype="WinEventLog:Security" AND EventCode=4688 
| rex field=Message "(?s)Creator Subject:.*?Security ID:\s*(?<Security_ID>[^\n]+).*?Account Name:\s*(?<Creator_Account>\S+)"

| rex field=Message "(?s)Process Information:.*?\n\s*New Process Name:\s*(?<New_Process_Name>[^\r\n]+)\s*\n\s*Creator Process Name:\s*(?<Creator_Process_Name>[^\r\n]+)\s*\n\s*Process Command Line:\s*(?<Process_Command_Line>[^\r\n]+)"


| fillnull value=unknown
| search NOT Creator_Account=unknown
| table Creator_Account, Security_ID, New_Process_Name, Creator_Process_Name, Process_Command_Line
```

