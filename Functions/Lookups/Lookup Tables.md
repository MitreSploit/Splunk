# Create the lookup
##### Output Lookup Table
Output a lookup table with **`outputlookup`**.
```shell
index=firewall earliest=-7d
| stats latest(host) AS hostname latest(user) AS owner BY src_ip
| rename src_ip AS IP
| outputlookup ip_asset_lookup.csv
```



## Important Definitions

```shell
inputlookup = Bring rows FROM a lookup into the search as events

lookup = Add fields FROM a lookup to existing events
```

1. **inputlookup** = below we use an example of `join` to basically do the same thing. 

2. **lookup** = Use lookup kind of like a phonebook - in our case, we say use a specific field in the lookup & for any other matching events, output this other field name if it matches.
```bash
# use the 'user' filter and output matching events 'role'
| lookup privileged_user.csv user OUTPUT role
```


### Calling the Lookup With JOIN -- Fred's Method (inputlookup)
We can use **`join`** to call our lookup that we created like this:
```bash

| join <Field-Were-Joining> [ | inputlookup <Lookup-Name> | rename <FieldName-In-Lookup as <Field-Were-Joining> | fields FieldYouWant SecondField]

# Table like normal with the field.
```


### Calling the Lookup -- Second Method (lookup) - Works better for me:
This method is visually simpler and just uses **`lookup`**.
In the example below, I'm asking for the value **`id.resp_h`** but I'm renaming the value to **`DestinationIP`**.

I'm also grabbing the field **`hostname`**. I could then do anything to follow with those fields. In the example I just do a simple table. 
```shell
index=firewall
| lookup hostList.csv id.resp_h AS DestinationIP OUTPUT hostname
| table DestinationIP hostname
```



# APPEND A LOOKUP TABLE - Manual Row
In this example, I have pulled a lookup table and I'm looking tabling the results. 
Following this, I am making an append to the table. This is a manual append.

The append will add a value for **`src_ip`** and **`hostname`**.
```bash
index=firewall
| lookup ip_to_host ip AS src_ip OUTPUT hostname
| table src_ip hostname

| append [
    | makeresults
    | eval src_ip="10.10.10.10", hostname="MY MANUAL ENTRY"
    | table src_ip hostname
]
```


