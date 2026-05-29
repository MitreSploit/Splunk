# What is Case?
Case has a similar use case to when it is used in PowerShell.

By default, searches are case-insensitive. For example, if you search for `Error`, any case of that term is returned, such as `Error`, `error`, and `ERROR`. You can use the CASE directive to perform case-sensitive matches for terms and field values. For example, if you search for `CASE(error)`, your search returns results containing only the specified case of the term, which is `error`.

You can use the CASE directive to search for terms using wildcards. For example, searching for `CASE(%ASA-1*)` returns events matching values such as `%ASA-1-134568` and `%ASA-1-12345`.



# What is Term?
The TERM directive is useful for more efficiently searching for a term that:

- Contains [minor breakers](https://docs.splunk.com/Splexicon:Minorbreak), such as periods or underscores.
- Is bound by [major breakers](https://docs.splunk.com/Splexicon:Majorbreak), such as spaces or commas.
- Does not contain major breakers.