Example of how to use Base queries in XML. 
- Useful for 

```BASH
<dashboard>
  <label>Base Search with Time Picker</label>

  <!-- Base Search -->
  <search id="base_search">
    <query>
      index=dragos EventCode=$event_code$
      | rex field=_raw "(?s)&lt;Message&gt;\s*(?&lt;msgblock&gt;.*?)\s*&lt;/Message&gt;"
      | extract field=msgblock pairdelim="\n" kvdelim=":"
    </query>
    <earliest>$time_range.earliest$</earliest>
    <latest>$time_range.latest$</latest>
  </search>

  <row>
    <panel>
      <title>Raw Events</title>
      <table>
        <search base="base_search" />
        <option name="count">10</option>
      </table>
    </panel>
  </row>

  <row>
    <panel>
      <title>Events by Account</title>
      <table>
        <search base="base_search">
          <query>
            | stats count by Account_Name
          </query>
        </search>
        <option name="count">10</option>
      </table>
    </panel>

    <panel>
      <title>Events by Host</title>
      <table>
        <search base="base_search">
          <query>
            | stats count by host
          </query>
        </search>
        <option name="count">10</option>
      </table>
    </panel>
  </row>
</dashboard>

```

