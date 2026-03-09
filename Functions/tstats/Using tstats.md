# Quick Use Case
1. You know the name of the host & you want to know which indexes are available to search through:
- This will return a quick list of available indexes. 
```bash
| tstats count WHERE index=* host="foo" BY index
```

