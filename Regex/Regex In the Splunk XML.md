When you're putting a `regex` query into your XML, you're going to encounter difficulties because XML already interprets the `<>` characters that `regex` uses as something else. 

When you're putting your regex query in, instead use the following:
```powershell
# &lt;  and the &gt; are used to substitute the symbols.
| rex field=Message "(?&lt;foobar&gt;Happy-\w+)"
```

