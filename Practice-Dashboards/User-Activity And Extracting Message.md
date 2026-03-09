- Using Splunk BOTS and **`botsv1`** as the lab. 
- Testing Running Regex
- Testing Dynamic Dropdowns
- Testing Sankey Diagrams (pending)
# Playing with Dynamic Pickers
In the following example, I'm using a dynamic picker to allow for different host machine selection

```PowerShell
<form version="1.1" theme="dark">
  <label>User-Activity</label>
  <search id="base">
    <query>
      index=botsv1 AND sourcetype="WinEventLog:Security" AND EventCode=4688 
      AND host=$Host$

      | rex field=Message "(?s)Creator Subject:.*?Security ID:\s*(?&lt;Security_ID&gt;[^\n]+).*?Account Name:\s*(?&lt;Creator_Account&gt;\S+)"
      | rex field=Message "(?s)Process Information:.*?\n\s*New Process Name:\s*(?&lt;New_Process_Name&gt;[^\r\n]+)\s*\n\s*Creator Process Name:\s*(?&lt;Creator_Process_Name&gt;[^\r\n]+)\s*\n\s*Process Command Line:\s*(?&lt;Process_Command_Line&gt;[^\r\n]+)"


      | fillnull value=unknown
      | search NOT Creator_Account=unknown

      | fields host EventCode Message Security_ID Creator_Account New_Process_Name Creator_Process_Name Process_Command_Line
    </query>
    <earliest>$Time.earliest$</earliest>
    <latest>$Time.latest$</latest>
  </search>
  <description>Assorted Test Searches</description>
  <fieldset submitButton="false" autoRun="false">
    <input type="time" token="Time">
      <label>Time</label>
      <default>
        <earliest>-24h@h</earliest>
        <latest>now</latest>
      </default>
    </input>
    <input type="dropdown" token="Host">
      <search base="base">
        <query>
          | stats values(host) AS host
          | mvexpand host
          | sort host
        </query>
      </search>
      <fieldForLabel>host</fieldForLabel>
      <fieldForValue>host</fieldForValue>
      <label>Host Machine</label>
      <choice value="*">Any</choice>
      <default>*</default>
      <initialValue>*</initialValue>
    </input>
  </fieldset>
  <row>
    <panel>
      <title>Testing Regex Extraction</title>
      <table>
        <search base="base">
          <query>| search NOT Creator_Process_Name="*SplunkUniversalForwarder*"
| table Creator_Account, Security_ID, New_Process_Name, Creator_Process_Name, Process_Command_Line</query>
        </search>
        <option name="count">20</option>
        <option name="dataOverlayMode">none</option>
        <option name="drilldown">none</option>
        <option name="percentagesRow">false</option>
        <option name="refresh.display">progressbar</option>
        <option name="rowNumbers">false</option>
        <option name="totalsRow">false</option>
        <option name="wrap">true</option>
      </table>
    </panel>
  </row>
</form>
```