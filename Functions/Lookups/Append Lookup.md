# Append a previously created lookup - Keeping Multivalue Formats

When a previous lookup table has been created, but you are required to add the results from a new search to this table, you can do the following.

I needed to do things this way due to the way that a CSV file is saved as a 1 TO 1 value pair. This made things very difficult because multivalues become placed on the same line.

## 1. When the lookup is first created
```
index=<> sourcetype=zeek:dns.log AND query=* AND answers=*

| rename query as Hostname, answers as IP_Addr

| stats values(Hostname) by IP_Addr

| sort IP_Addr
| outputlookup dns_IP_lookup.csv
```

## 2. Append the results, allowing multi values:
```
index=botsv2 AND sourcetype="stream:dns" AND query=* AND answer=* AND query_type="A"
``` Change the field etc. ```

| rename query as Hostname, answers as IP_Addr

| append [
    |  inputlookup dns_resolution.csv
    |  makemv delim=" " Hostname
]

| dedup IP_Addr
| sort IP_Addr

```Optional – Redo the table```
| outputlookup dns_resolution.csv
```
