# Create the Lookup table in BOTs - DNS Exfiltration - Like this:
```PowerShell
index=botsv2 AND sourcetype="stream:dns" AND query=* AND answer=* AND query_type="A"
| rename answer AS IP_Addr

| stats values(query) as Hostname by IP_Addr
| sort IP_Addr
| outputlookup dns_resolution.csv
```


## Splunk BOTs Full XML:
```PowerShell
{
	"dataSources": {
		"ds_search_1_new_new": {
			"type": "ds.search",
			"options": {
				"query": "| table Subnet_Name, Expected_IP_Count, Actual_IP_Count\n\n| append [\n    | inputlookup dns_resolution.csv\n    | eval Subnet_Name=case(\n        cidrmatch(\"1.160.0.0/16\", IP_Addr), \"Dev Subnet\",\n        cidrmatch(\"40.97.0.0/16\", IP_Addr), \"Marketing Subnet\",\n        cidrmatch(\"13.0.0.0/8\", IP_Addr), \"IT Subnet\"\n    )\n    | where isnotnull(Subnet_Name)\n    | stats dc(IP_Addr) as Actual_IP_Count by Subnet_Name\n\n    | append [\n        | makeresults count=3\n        | streamstats count as row\n        | eval Subnet_Name=case(\n            row=1, \"Dev Subnet\",\n            row=2, \"Marketing Subnet\",\n            row=3, \"IT Subnet\"\n        )\n        | eval Expected_IP_Count=case(\n            row=1, 4,\n            row=2, 8,\n            row=3, 3\n        )\n        | table Subnet_Name, Expected_IP_Count\n    ]\n\n    | stats max(Expected_IP_Count) as Expected_IP_Count max(Actual_IP_Count) as Actual_IP_Count by Subnet_Name\n    | table Subnet_Name Expected_IP_Count Actual_IP_Count\n]",
				"queryParameters": {
					"earliest": "0",
					"latest": "now"
				}
			}
		},
		"ds_search_1_new": {
			"type": "ds.search",
			"options": {
				"query": "| table Subnet_Name, Subnet_Range\n\n| append [\n    | makeresults count=3\n    | streamstats count as row\n    | eval Subnet_Name=case(\n        row=1, \"Dev Subnet\",\n        row=2, \"Marketing Subnet\",\n        row=3, \"IT Subnet\"\n    )\n    | eval Subnet_Range=case(\n        row=1, \"1.160.0.0/16\",\n        row=2, \"40.97.0.0/16\",\n        row=3, \"13.0.0.0/8\"\n    )\n    | table Subnet_Name, Subnet_Range\n]",
				"queryParameters": {
					"earliest": "0",
					"latest": "now"
				}
			}
		},
		"ds_search_1": {
			"type": "ds.search",
			"options": {
				"query": "| inputlookup dns_resolution.csv\n|  search IP_Addr=$IP$ AND Hostname=$HOSTNAME$\n\n| table IP_Addr, Hostname\n",
				"queryParameters": {
					"earliest": "1503990000",
					"latest": "1504076400"
				}
			}
		},
		"ds_8x5ExMjE": {
			"type": "ds.search",
			"options": {
				"query": "| inputlookup dns_resolution.csv\n|  where cidrmatch(\"1.160.0.0/16\",IP_Addr)\n\n| table IP_Addr, Hostname"
			},
			"name": "Search_1"
		},
		"ds_KQUdDfX9": {
			"type": "ds.search",
			"options": {
				"query": "| inputlookup dns_resolution.csv\n|  where cidrmatch(\"40.97.0.0/16\",IP_Addr)\n\n| table IP_Addr, Hostname"
			},
			"name": "Search_2"
		},
		"ds_eiaX2fSP": {
			"type": "ds.search",
			"options": {
				"query": "| inputlookup dns_resolution.csv\n| stats \ncount(eval(cidrmatch(\"1.160.0.0/16\",IP_Addr))) as Dev_Subnet\ncount(eval(cidrmatch(\"40.97.0.0/16\",IP_Addr))) as Marketing_Subnet\ncount(eval(cidrmatch(\"13.0.0.0/8\",IP_Addr))) as IT_Subnet\n\n\n| transpose\n| rename column AS Category\n| rename \"row 1\" AS Value\n| table Category Value"
			},
			"name": "Search_3"
		}
	},
	"visualizations": {
		"viz_table_1_new_new": {
			"type": "splunk.table",
			"options": {
				"count": 20,
				"dataOverlayMode": "none",
				"drilldown": "none",
				"percentagesRow": false,
				"rowNumbers": false,
				"totalsRow": false,
				"wrap": true,
				"backgroundColor": "#294e70",
				"tableFormat": {
					"rowBackgroundColors": "> table | seriesByIndex(0) | pick(tableAltRowBackgroundColorsByBackgroundColor)",
					"headerBackgroundColor": "> backgroundColor | setColorChannel(tableHeaderBackgroundColorConfig)",
					"rowColors": "> rowBackgroundColors | maxContrast(tableRowColorMaxContrast)",
					"headerColor": "> headerBackgroundColor | maxContrast(tableRowColorMaxContrast)"
				}
			},
			"dataSources": {
				"primary": "ds_search_1_new_new"
			},
			"title": "Expected Subnet IP Counter"
		},
		"viz_table_1_new": {
			"type": "splunk.table",
			"options": {
				"count": 20,
				"dataOverlayMode": "none",
				"drilldown": "none",
				"percentagesRow": false,
				"rowNumbers": false,
				"totalsRow": false,
				"wrap": true,
				"backgroundColor": "#294e70",
				"tableFormat": {
					"rowBackgroundColors": "> table | seriesByIndex(0) | pick(tableAltRowBackgroundColorsByBackgroundColor)",
					"headerBackgroundColor": "> backgroundColor | setColorChannel(tableHeaderBackgroundColorConfig)",
					"rowColors": "> rowBackgroundColors | maxContrast(tableRowColorMaxContrast)",
					"headerColor": "> headerBackgroundColor | maxContrast(tableRowColorMaxContrast)"
				}
			},
			"dataSources": {
				"primary": "ds_search_1_new"
			},
			"title": "Subnet Ranges",
			"description": "Expected Subnets"
		},
		"viz_table_1": {
			"type": "splunk.table",
			"options": {
				"count": 20,
				"dataOverlayMode": "none",
				"drilldown": "none",
				"percentagesRow": false,
				"rowNumbers": false,
				"totalsRow": false,
				"wrap": true,
				"backgroundColor": "#294e70",
				"tableFormat": {
					"rowBackgroundColors": "> table | seriesByIndex(0) | pick(tableAltRowBackgroundColorsByBackgroundColor)",
					"headerBackgroundColor": "> backgroundColor | setColorChannel(tableHeaderBackgroundColorConfig)",
					"rowColors": "> rowBackgroundColors | maxContrast(tableRowColorMaxContrast)",
					"headerColor": "> headerBackgroundColor | maxContrast(tableRowColorMaxContrast)"
				},
				"showRowNumbers": true
			},
			"dataSources": {
				"primary": "ds_search_1"
			},
			"title": "All IP Addresses",
			"description": "All IP Addresses from the DNS Records."
		},
		"viz_XFlkMnM3": {
			"type": "splunk.table",
			"title": "Dev Subnet",
			"dataSources": {
				"primary": "ds_8x5ExMjE"
			},
			"options": {
				"backgroundColor": "#294e70",
				"tableFormat": {
					"rowBackgroundColors": "> table | seriesByIndex(0) | pick(tableAltRowBackgroundColorsByBackgroundColor)",
					"headerBackgroundColor": "> backgroundColor | setColorChannel(tableHeaderBackgroundColorConfig)",
					"rowColors": "> rowBackgroundColors | maxContrast(tableRowColorMaxContrast)",
					"headerColor": "> headerBackgroundColor | maxContrast(tableRowColorMaxContrast)"
				}
			}
		},
		"viz_HHVsz6NQ": {
			"type": "splunk.table",
			"title": "Marketing Subnet",
			"dataSources": {
				"primary": "ds_KQUdDfX9"
			},
			"options": {
				"backgroundColor": "#294e70",
				"tableFormat": {
					"rowBackgroundColors": "> table | seriesByIndex(0) | pick(tableAltRowBackgroundColorsByBackgroundColor)",
					"headerBackgroundColor": "> backgroundColor | setColorChannel(tableHeaderBackgroundColorConfig)",
					"rowColors": "> rowBackgroundColors | maxContrast(tableRowColorMaxContrast)",
					"headerColor": "> headerBackgroundColor | maxContrast(tableRowColorMaxContrast)"
				}
			}
		},
		"viz_lm2Nzxl9": {
			"type": "viz.column",
			"title": "All Subnets Count",
			"dataSources": {
				"primary": "ds_eiaX2fSP"
			},
			"options": {
				"chart.showDataLabels": "all"
			}
		}
	},
	"inputs": {
		"input_global_trp": {
			"type": "input.timerange",
			"options": {
				"token": "global_time",
				"defaultValue": "-4h@m,now"
			},
			"title": "Global Time Range"
		},
		"input_iSdpsz3b": {
			"options": {
				"defaultValue": "*",
				"token": "IP"
			},
			"title": "IP Select",
			"type": "input.text"
		},
		"input_uCz0K6R9": {
			"options": {
				"defaultValue": "*",
				"token": "HOSTNAME"
			},
			"title": "Hostname Select",
			"type": "input.text"
		}
	},
	"layout": {
		"type": "absolute",
		"options": {
			"height": 1460,
			"width": 1600,
			"backgroundColor": "#000000"
		},
		"structure": [
			{
				"item": "viz_table_1",
				"type": "block",
				"position": {
					"x": 30,
					"y": 410,
					"w": 1540,
					"h": 270
				}
			},
			{
				"item": "viz_XFlkMnM3",
				"type": "block",
				"position": {
					"x": 30,
					"y": 700,
					"w": 530,
					"h": 340
				}
			},
			{
				"item": "viz_HHVsz6NQ",
				"type": "block",
				"position": {
					"x": 580,
					"y": 700,
					"w": 560,
					"h": 340
				}
			},
			{
				"item": "viz_lm2Nzxl9",
				"type": "block",
				"position": {
					"x": 1140,
					"y": 30,
					"w": 430,
					"h": 360
				}
			},
			{
				"item": "viz_table_1_new",
				"type": "block",
				"position": {
					"x": 30,
					"y": 30,
					"w": 530,
					"h": 360
				}
			},
			{
				"item": "viz_table_1_new_new",
				"type": "block",
				"position": {
					"x": 580,
					"y": 30,
					"w": 540,
					"h": 360
				}
			}
		],
		"globalInputs": [
			"input_global_trp",
			"input_iSdpsz3b",
			"input_uCz0K6R9"
		]
	},
	"title": "Network Bible",
	"description": "",
	"defaults": {
		"dataSources": {
			"ds.search": {
				"options": {
					"queryParameters": {
						"latest": "$global_time.latest$",
						"earliest": "$global_time.earliest$"
					}
				}
			}
		}
	}
}
```
