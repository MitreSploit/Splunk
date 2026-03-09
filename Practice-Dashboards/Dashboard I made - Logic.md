1. Eval to define Logon Types to human readable
2. `bin` to set bucket time to 1 minute.
3. `streamstats`, with `eval` to count by failed logon events. 
4. `streamstats` with `eval` and `if` to search for privileged processes, then return a name if it matches the event ID.
5. `stats` -- Multiple to follow:
6. `values` with `eval` and `if` to say if the failed logons are greater than 5, return true.
7. `fillnull`

