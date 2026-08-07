```PowerShell
{
	"dataSources": {
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
				"query": "| inputlookup dns_resolution.csv\n|  where cidrmatch(\"17.167.0.0/16\",IP_Addr)\n\n| table IP_Addr, Hostname"
			},
			"name": "Search_1"
		},
		"ds_KQUdDfX9": {
			"type": "ds.search",
			"options": {
				"query": "| inputlookup dns_resolution.csv\n|  where cidrmatch(\"17.248.0.0/16\",IP_Addr)\n\n| table IP_Addr, Hostname"
			},
			"name": "Search_2"
		}
	},
	"visualizations": {
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
				"backgroundColor": "#af575a",
				"tableFormat": {
					"rowBackgroundColors": "> table | seriesByIndex(0) | pick(tableAltRowBackgroundColorsByBackgroundColor)",
					"headerBackgroundColor": "> backgroundColor | setColorChannel(tableHeaderBackgroundColorConfig)",
					"rowColors": "> rowBackgroundColors | maxContrast(tableRowColorMaxContrast)",
					"headerColor": "> headerBackgroundColor | maxContrast(tableRowColorMaxContrast)"
				}
			},
			"dataSources": {
				"primary": "ds_search_1"
			},
			"title": "All Out"
		},
		"viz_XFlkMnM3": {
			"type": "splunk.table",
			"title": "Dev Subnet",
			"dataSources": {
				"primary": "ds_8x5ExMjE"
			},
			"options": {
				"backgroundColor": "#0877a6",
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
				"backgroundColor": "#4fa484",
				"tableFormat": {
					"rowBackgroundColors": "> table | seriesByIndex(0) | pick(tableAltRowBackgroundColorsByBackgroundColor)",
					"headerBackgroundColor": "> backgroundColor | setColorChannel(tableHeaderBackgroundColorConfig)",
					"rowColors": "> rowBackgroundColors | maxContrast(tableRowColorMaxContrast)",
					"headerColor": "> headerBackgroundColor | maxContrast(tableRowColorMaxContrast)"
				}
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
			"display": "auto-scale"
		},
		"structure": [
			{
				"item": "viz_table_1",
				"type": "block",
				"position": {
					"x": 0,
					"y": 0,
					"w": 1200,
					"h": 380
				}
			},
			{
				"item": "viz_XFlkMnM3",
				"type": "block",
				"position": {
					"x": 0,
					"y": 390,
					"w": 420,
					"h": 310
				}
			},
			{
				"item": "viz_HHVsz6NQ",
				"type": "block",
				"position": {
					"x": 420,
					"y": 390,
					"w": 420,
					"h": 310
				}
			}
		],
		"globalInputs": [
			"input_global_trp",
			"input_iSdpsz3b",
			"input_uCz0K6R9"
		]
	},
	"title": "TestBoard",
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