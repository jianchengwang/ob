---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
Redis 数据类型根底层数据结构实现 ^RKpH2xKk

String ^cbupCrN8

List ^kCjpUGog

Hash ^KIWvxa80

Sorted Set ^VO2rBEGF

Set ^oY6kyMFJ

简单动态字符串 ^B4xMpIBU

双向链表 ^PSB5i9f1

压缩列表 ^qTCzwnoT

哈希表 ^hBIsdJYE

跳表 ^acPT71A4

整数数组 ^3Xa2hlKZ

%%
# Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://excalidraw.com",
	"elements": [
		{
			"id": "b0t1E6VWX7QHLMvOcCOUm",
			"type": "rectangle",
			"x": -687.25,
			"y": -340.2421875,
			"width": 1042,
			"height": 339,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 483243047,
			"version": 99,
			"versionNonce": 1087903623,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076223269,
			"link": null,
			"locked": false
		},
		{
			"id": "RKpH2xKk",
			"type": "text",
			"x": -413.25,
			"y": -320.2421875,
			"width": 318,
			"height": 22,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 707576713,
			"version": 101,
			"versionNonce": 1058152135,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671074868210,
			"link": null,
			"locked": false,
			"text": "Redis 数据类型根底层数据结构实现",
			"rawText": "Redis 数据类型根底层数据结构实现",
			"fontSize": 20,
			"fontFamily": 4,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Redis 数据类型根底层数据结构实现"
		},
		{
			"id": "odZSW6UjtkgTmvYs6cJvr",
			"type": "rectangle",
			"x": -667.25,
			"y": -252.2421875,
			"width": 170,
			"height": 39,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1342621705,
			"version": 41,
			"versionNonce": 1674892457,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "cbupCrN8"
				},
				{
					"id": "2fh1GjILwNHnA2rYsVExu",
					"type": "arrow"
				}
			],
			"updated": 1671076417280,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 55,
			"versionNonce": 1275489865,
			"isDeleted": false,
			"id": "xsLhruF6CNQYgAhvEEr33",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -466.25,
			"y": -255.7421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 170,
			"height": 35,
			"seed": 708692263,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "kCjpUGog"
				},
				{
					"id": "-p5b9Msd6bbOYnLeTGE1Q",
					"type": "arrow"
				},
				{
					"id": "ShesxljKNO-lziy2JTYvn",
					"type": "arrow"
				}
			],
			"updated": 1671087419307,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 78,
			"versionNonce": 581088873,
			"isDeleted": false,
			"id": "VJiuJAd-IVmpcwObiNSYi",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -262.25,
			"y": -252.7421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 170,
			"height": 39,
			"seed": 2017198281,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "KIWvxa80"
				},
				{
					"id": "eWM4S9Vlb_9g8DaATPcyb",
					"type": "arrow"
				},
				{
					"id": "fq-TEovn5meWqFyhyrjIA",
					"type": "arrow"
				}
			],
			"updated": 1671076439201,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 78,
			"versionNonce": 1250980359,
			"isDeleted": false,
			"id": "DmEuizZye_cY8eVLgfklY",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -48.25,
			"y": -248.7421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 170,
			"height": 39,
			"seed": 974654535,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "jOy4-r7um2JiUZQzfW-FN",
					"type": "arrow"
				}
			],
			"updated": 1671076454642,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 61,
			"versionNonce": 2143350377,
			"isDeleted": false,
			"id": "D7IqxdHm4vT65mYrjVrAH",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 164.75,
			"y": -248.7421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 170,
			"height": 39,
			"seed": 477298601,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "oY6kyMFJ"
				},
				{
					"id": "q2LhBkRqFnYQ6zuy9F4f2",
					"type": "arrow"
				},
				{
					"id": "ip4XZ8Ku7OHZYYApB0JPy",
					"type": "arrow"
				}
			],
			"updated": 1671076465482,
			"link": null,
			"locked": false
		},
		{
			"id": "cbupCrN8",
			"type": "text",
			"x": -611.25,
			"y": -245.2421875,
			"width": 58,
			"height": 25,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1696613513,
			"version": 9,
			"versionNonce": 1261571815,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076281719,
			"link": null,
			"locked": false,
			"text": "String",
			"rawText": "String",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "odZSW6UjtkgTmvYs6cJvr",
			"originalText": "String"
		},
		{
			"id": "kCjpUGog",
			"type": "text",
			"x": -401.75,
			"y": -250.7421875,
			"width": 41,
			"height": 25,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1426909447,
			"version": 36,
			"versionNonce": 135607687,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671087428519,
			"link": null,
			"locked": false,
			"text": "List",
			"rawText": "List",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "xsLhruF6CNQYgAhvEEr33",
			"originalText": "List"
		},
		{
			"id": "KIWvxa80",
			"type": "text",
			"x": -200.75,
			"y": -245.7421875,
			"width": 47,
			"height": 25,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 435936585,
			"version": 33,
			"versionNonce": 1179378535,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076285669,
			"link": null,
			"locked": false,
			"text": "Hash",
			"rawText": "Hash",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "VJiuJAd-IVmpcwObiNSYi",
			"originalText": "Hash"
		},
		{
			"id": "VO2rBEGF",
			"type": "text",
			"x": -12.25,
			"y": -243.2421875,
			"width": 112,
			"height": 25,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 223829927,
			"version": 49,
			"versionNonce": 730472873,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "6NOetMqeb0cEFbFoMsBKa",
					"type": "arrow"
				}
			],
			"updated": 1671076448372,
			"link": null,
			"locked": false,
			"text": "Sorted Set",
			"rawText": "Sorted Set",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Sorted Set"
		},
		{
			"id": "oY6kyMFJ",
			"type": "text",
			"x": 231.75,
			"y": -241.7421875,
			"width": 36,
			"height": 25,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 190412329,
			"version": 10,
			"versionNonce": 925509831,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076289773,
			"link": null,
			"locked": false,
			"text": "Set",
			"rawText": "Set",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "D7IqxdHm4vT65mYrjVrAH",
			"originalText": "Set"
		},
		{
			"id": "ZApBiF05MDJiiHx-E_FVs",
			"type": "rectangle",
			"x": -643.25,
			"y": -106.2421875,
			"width": 151,
			"height": 42,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 532499945,
			"version": 78,
			"versionNonce": 1162394087,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076231281,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 74,
			"versionNonce": 1990305161,
			"isDeleted": false,
			"id": "5kamph2wFZinFIES64CJ9",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -454.75,
			"y": -108.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 100,
			"height": 42,
			"seed": 1322982087,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "PSB5i9f1"
				},
				{
					"id": "-p5b9Msd6bbOYnLeTGE1Q",
					"type": "arrow"
				}
			],
			"updated": 1671076423805,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 88,
			"versionNonce": 118097033,
			"isDeleted": false,
			"id": "8QPEm2Sp6nlVynatBlnzp",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -297.75,
			"y": -107.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 99,
			"height": 42,
			"seed": 1947081001,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "qTCzwnoT"
				},
				{
					"id": "ShesxljKNO-lziy2JTYvn",
					"type": "arrow"
				},
				{
					"id": "eWM4S9Vlb_9g8DaATPcyb",
					"type": "arrow"
				},
				{
					"id": "6NOetMqeb0cEFbFoMsBKa",
					"type": "arrow"
				}
			],
			"updated": 1671076448372,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 96,
			"versionNonce": 1508377607,
			"isDeleted": false,
			"id": "dLydLA9J_KqAnzgWm6gJL",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -126.75,
			"y": -108.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 99,
			"height": 42,
			"seed": 777660903,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "hBIsdJYE"
				},
				{
					"id": "fq-TEovn5meWqFyhyrjIA",
					"type": "arrow"
				},
				{
					"id": "q2LhBkRqFnYQ6zuy9F4f2",
					"type": "arrow"
				}
			],
			"updated": 1671076462293,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 126,
			"versionNonce": 2001225831,
			"isDeleted": false,
			"id": "HdKcoQr9_suXh5OBF-9yM",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 26.25,
			"y": -108.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 99,
			"height": 42,
			"seed": 455281673,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "acPT71A4"
				},
				{
					"id": "jOy4-r7um2JiUZQzfW-FN",
					"type": "arrow"
				}
			],
			"updated": 1671076467707,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 140,
			"versionNonce": 1304325033,
			"isDeleted": false,
			"id": "uuYw-lcwuW_S6-0E3_CNQ",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 196.25,
			"y": -112.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 127.00000000000003,
			"height": 42,
			"seed": 1701467399,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1671076256503,
			"link": null,
			"locked": false
		},
		{
			"id": "B4xMpIBU",
			"type": "text",
			"x": -635.25,
			"y": -98.2421875,
			"width": 142,
			"height": 22,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1907086311,
			"version": 42,
			"versionNonce": 1770411913,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "2fh1GjILwNHnA2rYsVExu",
					"type": "arrow"
				}
			],
			"updated": 1671076417280,
			"link": null,
			"locked": false,
			"text": "简单动态字符串",
			"rawText": "简单动态字符串",
			"fontSize": 20,
			"fontFamily": 4,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "简单动态字符串"
		},
		{
			"id": "PSB5i9f1",
			"type": "text",
			"x": -445.75,
			"y": -98.2421875,
			"width": 82,
			"height": 22,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1380575783,
			"version": 22,
			"versionNonce": 882339975,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076356546,
			"link": null,
			"locked": false,
			"text": "双向链表",
			"rawText": "双向链表",
			"fontSize": 20,
			"fontFamily": 4,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "5kamph2wFZinFIES64CJ9",
			"originalText": "双向链表"
		},
		{
			"id": "qTCzwnoT",
			"type": "text",
			"x": -289.25,
			"y": -97.2421875,
			"width": 82,
			"height": 22,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1300613031,
			"version": 14,
			"versionNonce": 193011593,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076365901,
			"link": null,
			"locked": false,
			"text": "压缩列表",
			"rawText": "压缩列表",
			"fontSize": 20,
			"fontFamily": 4,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "8QPEm2Sp6nlVynatBlnzp",
			"originalText": "压缩列表"
		},
		{
			"id": "hBIsdJYE",
			"type": "text",
			"x": -108.25,
			"y": -98.2421875,
			"width": 62,
			"height": 22,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1914073255,
			"version": 28,
			"versionNonce": 1290119849,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076377228,
			"link": null,
			"locked": false,
			"text": "哈希表",
			"rawText": "哈希表",
			"fontSize": 20,
			"fontFamily": 4,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "dLydLA9J_KqAnzgWm6gJL",
			"originalText": "哈希表"
		},
		{
			"id": "acPT71A4",
			"type": "text",
			"x": 54.75,
			"y": -98.2421875,
			"width": 42,
			"height": 22,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 560057223,
			"version": 34,
			"versionNonce": 915150217,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076467707,
			"link": null,
			"locked": false,
			"text": "跳表",
			"rawText": "跳表",
			"fontSize": 20,
			"fontFamily": 4,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "HdKcoQr9_suXh5OBF-9yM",
			"originalText": "跳表"
		},
		{
			"id": "3Xa2hlKZ",
			"type": "text",
			"x": 225.75,
			"y": -101.2421875,
			"width": 82,
			"height": 22,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 392607913,
			"version": 24,
			"versionNonce": 1816738121,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "ip4XZ8Ku7OHZYYApB0JPy",
					"type": "arrow"
				}
			],
			"updated": 1671076465482,
			"link": null,
			"locked": false,
			"text": "整数数组",
			"rawText": "整数数组",
			"fontSize": 20,
			"fontFamily": 4,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "整数数组"
		},
		{
			"id": "2fh1GjILwNHnA2rYsVExu",
			"type": "arrow",
			"x": -583.25,
			"y": -207.2421875,
			"width": 2,
			"height": 97,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1880564521,
			"version": 41,
			"versionNonce": 269411943,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076417280,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					2,
					97
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "odZSW6UjtkgTmvYs6cJvr",
				"focus": 0.017865765330758086,
				"gap": 6
			},
			"endBinding": {
				"elementId": "B4xMpIBU",
				"focus": -0.23201621073961498,
				"gap": 12
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "-p5b9Msd6bbOYnLeTGE1Q",
			"type": "arrow",
			"x": -399.99216188736716,
			"y": -216.2421875,
			"width": 10.277446724491256,
			"height": 107,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 130317929,
			"version": 33,
			"versionNonce": 1080874313,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671087429810,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-10.277446724491256,
					107
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "xsLhruF6CNQYgAhvEEr33",
				"gap": 4.5,
				"focus": 0.1918435754189944
			},
			"endBinding": {
				"elementId": "5kamph2wFZinFIES64CJ9",
				"gap": 1,
				"focus": -0.14673507462686566
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "ShesxljKNO-lziy2JTYvn",
			"type": "arrow",
			"x": -375.28283975509066,
			"y": -213.2421875,
			"width": 104.42066511576672,
			"height": 103,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 2044466055,
			"version": 41,
			"versionNonce": 1083524137,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671087429811,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					104.42066511576672,
					103
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "xsLhruF6CNQYgAhvEEr33",
				"gap": 7.5,
				"focus": 0.18858131487889274
			},
			"endBinding": {
				"elementId": "8QPEm2Sp6nlVynatBlnzp",
				"gap": 3,
				"focus": 0.024281150159744413
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "eWM4S9Vlb_9g8DaATPcyb",
			"type": "arrow",
			"x": -179.25,
			"y": -208.2421875,
			"width": 53,
			"height": 91,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1162488327,
			"version": 32,
			"versionNonce": 1311882247,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076436840,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-53,
					91
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "VJiuJAd-IVmpcwObiNSYi",
				"focus": -0.13035296801049212,
				"gap": 5.5
			},
			"endBinding": {
				"elementId": "8QPEm2Sp6nlVynatBlnzp",
				"focus": -0.033288829550511795,
				"gap": 10
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "fq-TEovn5meWqFyhyrjIA",
			"type": "arrow",
			"x": -154.25,
			"y": -206.2421875,
			"width": 79,
			"height": 97,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1691001639,
			"version": 24,
			"versionNonce": 1457062055,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076439202,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					79,
					97
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "VJiuJAd-IVmpcwObiNSYi",
				"focus": -0.010014817842726483,
				"gap": 7.5
			},
			"endBinding": {
				"elementId": "dLydLA9J_KqAnzgWm6gJL",
				"focus": 0.2990480612955654,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "6NOetMqeb0cEFbFoMsBKa",
			"type": "arrow",
			"x": 30.75,
			"y": -207.2421875,
			"width": 245,
			"height": 90,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 761287623,
			"version": 42,
			"versionNonce": 1122036071,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076448372,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-245,
					90
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "VO2rBEGF",
				"focus": -0.56618327676643,
				"gap": 11
			},
			"endBinding": {
				"elementId": "8QPEm2Sp6nlVynatBlnzp",
				"focus": -0.47239583333333335,
				"gap": 10
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "jOy4-r7um2JiUZQzfW-FN",
			"type": "arrow",
			"x": 41.98310011923433,
			"y": -196.2421875,
			"width": 4.84230163028726,
			"height": 87,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1240278151,
			"version": 39,
			"versionNonce": 1768062057,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076467708,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					4.84230163028726,
					87
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "DmEuizZye_cY8eVLgfklY",
				"focus": -0.03571428571428572,
				"gap": 13.5
			},
			"endBinding": {
				"elementId": "HdKcoQr9_suXh5OBF-9yM",
				"focus": -0.5466893039049237,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "q2LhBkRqFnYQ6zuy9F4f2",
			"type": "arrow",
			"x": 240.75,
			"y": -207.2421875,
			"width": 313,
			"height": 93,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1208758473,
			"version": 28,
			"versionNonce": 1243606281,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076462293,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-313,
					93
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "D7IqxdHm4vT65mYrjVrAH",
				"focus": -0.4318092586643823,
				"gap": 2.5
			},
			"endBinding": {
				"elementId": "dLydLA9J_KqAnzgWm6gJL",
				"focus": -0.7145349617501007,
				"gap": 6
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "ip4XZ8Ku7OHZYYApB0JPy",
			"type": "arrow",
			"x": 252.75,
			"y": -203.2421875,
			"width": 1,
			"height": 96,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 462382057,
			"version": 25,
			"versionNonce": 198854823,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1671076465482,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					96
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "D7IqxdHm4vT65mYrjVrAH",
				"focus": -0.03203129775658659,
				"gap": 6.5
			},
			"endBinding": {
				"elementId": "3Xa2hlKZ",
				"focus": -0.311882442361287,
				"gap": 6
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#000000",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "hachure",
		"currentItemStrokeWidth": 1,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 4,
		"currentItemFontSize": 20,
		"currentItemTextAlign": "left",
		"currentItemStrokeSharpness": "sharp",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"currentItemLinearStrokeSharpness": "round",
		"gridSize": null,
		"colorPalette": {}
	},
	"files": {}
}
```
%%