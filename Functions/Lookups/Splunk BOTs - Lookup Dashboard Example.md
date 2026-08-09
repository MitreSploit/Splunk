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

    "dataSources": {

        "ds_search_1": {

            "type": "ds.search",

            "options": {

                "query": "| inputlookup dns_resolution.csv\n|  search IP_Addr=$IP$ AND Hostname=$HOSTNAME$\n\n| table IP_Addr, Hostname\n",

                "queryParameters": {

                    "earliest": "1503990000",

                    "latest": "1504076400"

                }

            }

        },

        "ds_8x5ExMjE": {

            "type": "ds.search",

            "options": {

                "query": "| inputlookup dns_resolution.csv\n|  where cidrmatch(\"17.167.0.0/16\",IP_Addr)\n\n| table IP_Addr, Hostname"

            },

            "name": "Search_1"

        },

        "ds_KQUdDfX9": {

            "type": "ds.search",

            "options": {

                "query": "| inputlookup dns_resolution.csv\n|  where cidrmatch(\"17.248.0.0/16\",IP_Addr)\n\n| table IP_Addr, Hostname"

            },

            "name": "Search_2"

        },

        "ds_eiaX2fSP": {

            "type": "ds.search",

            "options": {

                "query": "| inputlookup dns_resolution.csv\n| stats \ncount(eval(cidrmatch(\"17.167.0.0/16\",IP_Addr))) as Dev_Subnet\ncount(eval(cidrmatch(\"17.248.0.0/16\",IP_Addr))) as Marketing_Subnet\ncount(eval(cidrmatch(\"13.0.0.0/8\",IP_Addr))) as IT_Subnet\n\n\n| transpose\n| rename column AS Category\n| rename \"row 1\" AS Value\n| table Category Value"

            },

            "name": "Search_3"

        }

    },

    "visualizations": {

        "viz_table_1": {

            "type": "splunk.table",

            "options": {

                "count": 20,

                "dataOverlayMode": "none",

                "drilldown": "none",

                "percentagesRow": false,

                "rowNumbers": false,

                "totalsRow": false,

                "wrap": true,

                "backgroundColor": "> themes.defaultBackgroundColor",

                "tableFormat": {

                    "rowBackgroundColors": "> table | seriesByIndex(0) | pick(tableAltRowBackgroundColorsByTheme)"

                }

            },

            "dataSources": {

                "primary": "ds_search_1"

            },

            "title": "All Out"

        },

        "viz_XFlkMnM3": {

            "type": "splunk.table",

            "title": "Dev Subnet",

            "dataSources": {

                "primary": "ds_8x5ExMjE"

            },

            "options": {

                "backgroundColor": "> themes.defaultBackgroundColor",

                "tableFormat": {

                    "rowBackgroundColors": "> table | seriesByIndex(0) | pick(tableAltRowBackgroundColorsByTheme)"

                }

            }

        },

        "viz_HHVsz6NQ": {

            "type": "splunk.table",

            "title": "Marketing Subnet",

            "dataSources": {

                "primary": "ds_KQUdDfX9"

            },

            "options": {

                "backgroundColor": "> themes.defaultBackgroundColor",

                "tableFormat": {

                    "rowBackgroundColors": "> table | seriesByIndex(0) | pick(tableAltRowBackgroundColorsByTheme)"

                }

            }

        },

        "viz_lm2Nzxl9": {

            "type": "viz.column",

            "title": "All Subnets Count",

            "dataSources": {

                "primary": "ds_eiaX2fSP"

            }

        }

    },

    "inputs": {

        "input_global_trp": {

            "type": "input.timerange",

            "options": {

                "token": "global_time",

                "defaultValue": "-4h@m,now"

            },

            "title": "Global Time Range"

        },

        "input_iSdpsz3b": {

            "options": {

                "defaultValue": "*",

                "token": "IP"

            },

            "title": "IP Select",

            "type": "input.text"

        },

        "input_uCz0K6R9": {

            "options": {

                "defaultValue": "*",

                "token": "HOSTNAME"

            },

            "title": "Hostname Select",

            "type": "input.text"

        }

    },

    "layout": {

        "type": "absolute",

        "options": {

            "display": "auto-scale"

        },

        "structure": [

            {

                "item": "viz_table_1",

                "type": "block",

                "position": {

                    "x": 0,

                    "y": 0,

                    "w": 650,

                    "h": 430

                }

            },

            {

                "item": "viz_XFlkMnM3",

                "type": "block",

                "position": {

                    "x": 0,

                    "y": 440,

                    "w": 630,

                    "h": 410

                }

            },

            {

                "item": "viz_HHVsz6NQ",

                "type": "block",

                "position": {

                    "x": 630,

                    "y": 440,

                    "w": 570,

                    "h": 410

                }

            },

            {

                "item": "viz_lm2Nzxl9",

                "type": "block",

                "position": {

                    "x": 640,

                    "y": 0,

                    "w": 560,

                    "h": 430

                }

            }

        ],

        "globalInputs": [

            "input_global_trp",

            "input_iSdpsz3b",

            "input_uCz0K6R9"

        ]

    },

    "title": "TestBoard",

    "description": "",

    "defaults": {

        "dataSources": {

            "ds.search": {

                "options": {

                    "queryParameters": {

                        "latest": "$global_time.latest$",

                        "earliest": "$global_time.earliest$"

                    }

                }

            }

        }

    }

}
```
