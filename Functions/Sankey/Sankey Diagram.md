# Sankey ChatGPT -- Based on Splunk BOTS
Works quite well, but remember that sometimes you need `''` around your fields if they have characters like a `.`

```bash
index=botsv2 sourcetype=pan:traffic src_user=*  
| fillnull value=unknown src_user  
| eval user_ip = src_user . " - " . src_ip  
  
| stats count by user_ip dest_port dest_ip  
  
| eval stage1_source = user_ip  
| eval stage1_target = dest_port  
  
| eval stage2_source = dest_port  
| eval stage2_target = dest_ip  
  
| appendpipe [  
stats sum(count) as count by stage1_source stage1_target  
| rename stage1_source as source stage1_target as target  
]  
  
| appendpipe [  
stats sum(count) as count by stage2_source stage2_target  
| rename stage2_source as source stage2_target as target  
]  
  
| stats sum(count) as count by source target
```

