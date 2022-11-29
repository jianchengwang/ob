---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
Greedy Algorithms  ^3RRmuyYu

Coins: 0.20 0.15 0.01  ^wSmxMo2l

Get 0.30 = 0.20 * 1 + 0.01 * 10  ^v4Ul9txu

it will take 10 coins, 
but the better solution is 0.30 = 0.15 * 2

so Greedy algorithms cant get optimal solution

 ^VBdHkskf

Fibonacci Sequence ^N2E6rSkv

0 1 1 2 3 5 8 13 21 34 55 89 ... ^UssRQz6o

solution1: recursion  ^XWUMAFvD

function fib(n) {
    if n <= 1
        return n
    return fib(n-1) + fib(n-2)
}  ^1fpJjY3B

fib(6) ^1J93oyYS

fib(3) ^VDuBST9q

fib(3) ^YQlAJBbo

fib(4) ^M3MwZBFy

fib(4) ^kPiyQ4v9

fib(5) ^z45Xycdj

fib(2) ^9yaRMCd5

fib(3) ^oo28i4m0

fib(2) ^KQPVLHHH

fib(2) ^ALVlUJSj

fib(1) ^04GqhjyC

fib(1) ^MZCtDHIH

fib(0) ^4Qwj8tcu

fib(1) ^LpVoweTf

fib(0) ^zcVv9KxP

..... ^DARk12Vi

Time: O(2^n) Space: O(1) ^GnXTHe0R

fib(6)
fib(5)
fib(4) fib(4)
fib(3) fib(3) fib(3)
fib(2) fib(2) fib(2) fib(2) fib(2)
fib(1) fib(1) fib(1) fib(1) fib(1) fib(1) fib(1) fib(1)
fib(0) fib(0) fib(0) fib(0) fib(0) ^fVrzmH4u

height
6 ^Zg5P3KtQ

roughly 2 ^6 calls ^DLUgANS8

solution2: repeat ^8hCZ6ivZ

memo = new map{0:0, 1:1}
function fib(n) {
    if memo[n] is null
        memo[n] = fib(n-1) + fib(n-2)
    return memo[n]
}
 ^LZmWB9c6

memo ^ZG5G4Xb4

0 : 0
1 : 1
5 : 5
4 : 3
3 : 2
2 : 1 ^44Rhyrpr

memo[5] = fib(4) + fib(3)
memo[4] = fib(3) + fib(2)
memo[3] = fib(2) + fib(1)
memo[2] = fib(1) + fib(0) 
         = memo[1] + memo[0) ^BwRpJhzV

Time: O(n) Space: O(n) ^2nPulWhz

solution3: dynamic programming(bottom-up) ^hV7hcX0Y

function fib(n) {
    if n <= 1
        return n
    p1 = 1
    p2 = 0
    for i=2; i<=n; i++
        p1, p2 = p2, p1 + p2
    return p2
} ^vr5zd6GS

0 1 1 2 ^63pnsnCJ

p1 ^gqODNbub

p2 ^1YczEoq1

1 + 2 + 4 + 8 + 16 + 32 ^vyH9VdFz

Time: O(n) Space: O(1) ^BJPAp33d

Solution4: math formula  ^rylJ8nmb

Time: O(1) ^LqpUUyd1

function coinChange(coins, amount) {
    a = initialize array with size(amount + 1)
    Fill a with (amount + 1)
    a[0] = 0
    for i=1; i<amount+1; i++
        for j=0; j<length of coins; j++
            if coins[j] <= i
                a[i] = min(a[i], a[i - coins[j]] + 1)
    if a[amount] > amount
        return -1
    return a[amount]
} ^DPlxDNB5

amount(n) ^rbb8eEZM

number of coins(m) ^epDFZwmF

Time: O(m*n) Space: O(n) ^iFdPh7fx

daynimic programming fix coins problem ^jt1CgIkJ

%%
# Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://excalidraw.com",
	"elements": [
		{
			"type": "text",
			"version": 101,
			"versionNonce": 871892603,
			"isDeleted": false,
			"id": "3RRmuyYu",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -398,
			"y": -382.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 186,
			"height": 25,
			"seed": 956317403,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669703669659,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Greedy Algorithms ",
			"rawText": "Greedy Algorithms ",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Greedy Algorithms "
		},
		{
			"type": "text",
			"version": 105,
			"versionNonce": 154084341,
			"isDeleted": false,
			"id": "wSmxMo2l",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -397,
			"y": -330.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 218,
			"height": 25,
			"seed": 737564699,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669703456791,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Coins: 0.20 0.15 0.01 ",
			"rawText": "Coins: 0.20 0.15 0.01 ",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Coins: 0.20 0.15 0.01 "
		},
		{
			"type": "text",
			"version": 122,
			"versionNonce": 1904404571,
			"isDeleted": false,
			"id": "v4Ul9txu",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -397,
			"y": -286.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 342,
			"height": 25,
			"seed": 1243694523,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669703450458,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Get 0.30 = 0.20 * 1 + 0.01 * 10 ",
			"rawText": "Get 0.30 = 0.20 * 1 + 0.01 * 10 ",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Get 0.30 = 0.20 * 1 + 0.01 * 10 "
		},
		{
			"type": "text",
			"version": 299,
			"versionNonce": 63417813,
			"isDeleted": false,
			"id": "VBdHkskf",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -397,
			"y": -247.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 466,
			"height": 150,
			"seed": 2023460571,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669703695660,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "it will take 10 coins, \nbut the better solution is 0.30 = 0.15 * 2\n\nso Greedy algorithms cant get optimal solution\n\n",
			"rawText": "it will take 10 coins, \nbut the better solution is 0.30 = 0.15 * 2\n\nso Greedy algorithms cant get optimal solution\n\n",
			"baseline": 143,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "it will take 10 coins, \nbut the better solution is 0.30 = 0.15 * 2\n\nso Greedy algorithms cant get optimal solution\n\n"
		},
		{
			"id": "N2E6rSkv",
			"type": "text",
			"x": -400,
			"y": -96.2421875,
			"width": 183,
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
			"seed": 934768277,
			"version": 41,
			"versionNonce": 686403579,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669703747844,
			"link": null,
			"locked": false,
			"text": "Fibonacci Sequence",
			"rawText": "Fibonacci Sequence",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Fibonacci Sequence"
		},
		{
			"id": "UssRQz6o",
			"type": "text",
			"x": -397,
			"y": -51.2421875,
			"width": 336,
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
			"seed": 45868475,
			"version": 50,
			"versionNonce": 2002131061,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669703645625,
			"link": null,
			"locked": false,
			"text": "0 1 1 2 3 5 8 13 21 34 55 89 ...",
			"rawText": "0 1 1 2 3 5 8 13 21 34 55 89 ...",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "0 1 1 2 3 5 8 13 21 34 55 89 ..."
		},
		{
			"id": "XWUMAFvD",
			"type": "text",
			"x": -437,
			"y": 26.7578125,
			"width": 192,
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
			"seed": 164100251,
			"version": 86,
			"versionNonce": 132963605,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704833230,
			"link": null,
			"locked": false,
			"text": "solution1: recursion ",
			"rawText": "solution1: recursion ",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "solution1: recursion "
		},
		{
			"id": "1fpJjY3B",
			"type": "text",
			"x": -435,
			"y": 88.7578125,
			"width": 277,
			"height": 125,
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
			"seed": 1166970805,
			"version": 689,
			"versionNonce": 2090997205,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704512076,
			"link": null,
			"locked": false,
			"text": "function fib(n) {\n    if n <= 1\n        return n\n    return fib(n-1) + fib(n-2)\n} ",
			"rawText": "function fib(n) {\n    if n <= 1\n        return n\n    return fib(n-1) + fib(n-2)\n} ",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 118,
			"containerId": null,
			"originalText": "function fib(n) {\n    if n <= 1\n        return n\n    return fib(n-1) + fib(n-2)\n} "
		},
		{
			"id": "1J93oyYS",
			"type": "text",
			"x": 68,
			"y": 6.7578125,
			"width": 54,
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
			"seed": 1614942453,
			"version": 362,
			"versionNonce": 719087355,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704447609,
			"link": null,
			"locked": false,
			"text": "fib(6)",
			"rawText": "fib(6)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "fib(6)"
		},
		{
			"type": "text",
			"version": 433,
			"versionNonce": 1287510581,
			"isDeleted": false,
			"id": "VDuBST9q",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 209,
			"y": 126.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 55,
			"height": 25,
			"seed": 372898811,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704447609,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(3)",
			"rawText": "fib(3)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(3)"
		},
		{
			"type": "text",
			"version": 452,
			"versionNonce": 1246482331,
			"isDeleted": false,
			"id": "YQlAJBbo",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 105,
			"y": 135.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 55,
			"height": 25,
			"seed": 2138051893,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704447609,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(3)",
			"rawText": "fib(3)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(3)"
		},
		{
			"type": "text",
			"version": 408,
			"versionNonce": 751750037,
			"isDeleted": false,
			"id": "M3MwZBFy",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -59,
			"y": 128.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 54,
			"height": 25,
			"seed": 2133771419,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704447609,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(4)",
			"rawText": "fib(4)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(4)"
		},
		{
			"type": "text",
			"version": 442,
			"versionNonce": 1291657973,
			"isDeleted": false,
			"id": "kPiyQ4v9",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 223,
			"y": 65.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 54,
			"height": 25,
			"seed": 1669441173,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704502561,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(4)",
			"rawText": "fib(4)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(4)"
		},
		{
			"type": "text",
			"version": 467,
			"versionNonce": 880447733,
			"isDeleted": false,
			"id": "z45Xycdj",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -7,
			"y": 68.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 54,
			"height": 25,
			"seed": 1270970683,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704447609,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(5)",
			"rawText": "fib(5)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(5)"
		},
		{
			"type": "text",
			"version": 252,
			"versionNonce": 1391125723,
			"isDeleted": false,
			"id": "9yaRMCd5",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 294.5,
			"y": 125.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 56,
			"height": 25,
			"seed": 974398395,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704447609,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(2)",
			"rawText": "fib(2)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(2)"
		},
		{
			"type": "text",
			"version": 504,
			"versionNonce": 1975601013,
			"isDeleted": false,
			"id": "oo28i4m0",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -105.5,
			"y": 183.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 55,
			"height": 25,
			"seed": 1782402997,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704730924,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(3)",
			"rawText": "fib(3)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(3)"
		},
		{
			"type": "text",
			"version": 478,
			"versionNonce": 1267700091,
			"isDeleted": false,
			"id": "KQPVLHHH",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 19,
			"y": 179.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 56,
			"height": 25,
			"seed": 688576533,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704447609,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(2)",
			"rawText": "fib(2)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(2)"
		},
		{
			"type": "text",
			"version": 533,
			"versionNonce": 604542901,
			"isDeleted": false,
			"id": "ALVlUJSj",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -126.5,
			"y": 243.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 56,
			"height": 25,
			"seed": 1918818907,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704447609,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(2)",
			"rawText": "fib(2)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(2)"
		},
		{
			"type": "text",
			"version": 566,
			"versionNonce": 1882565147,
			"isDeleted": false,
			"id": "04GqhjyC",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -53.5,
			"y": 240.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 47,
			"height": 25,
			"seed": 492699899,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704447609,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(1)",
			"rawText": "fib(1)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(1)"
		},
		{
			"type": "text",
			"version": 332,
			"versionNonce": 1956425147,
			"isDeleted": false,
			"id": "MZCtDHIH",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -163.5,
			"y": 296.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 47,
			"height": 25,
			"seed": 1643097691,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704451529,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(1)",
			"rawText": "fib(1)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(1)"
		},
		{
			"type": "text",
			"version": 584,
			"versionNonce": 1033737371,
			"isDeleted": false,
			"id": "4Qwj8tcu",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -90.5,
			"y": 298.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 55,
			"height": 25,
			"seed": 704765013,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704490985,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(0)",
			"rawText": "fib(0)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(0)"
		},
		{
			"type": "text",
			"version": 585,
			"versionNonce": 365932219,
			"isDeleted": false,
			"id": "LpVoweTf",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 14.5,
			"y": 233.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 47,
			"height": 25,
			"seed": 82074901,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704447609,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(1)",
			"rawText": "fib(1)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(1)"
		},
		{
			"type": "text",
			"version": 593,
			"versionNonce": 861138549,
			"isDeleted": false,
			"id": "zcVv9KxP",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 68.5,
			"y": 233.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 55,
			"height": 25,
			"seed": 1877699259,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669704447609,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "fib(0)",
			"rawText": "fib(0)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "fib(0)"
		},
		{
			"id": "z4QZc6Usgf5P2O9Afqu9R",
			"type": "line",
			"x": 127,
			"y": 29.7578125,
			"width": 94,
			"height": 41,
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
			"seed": 1133633237,
			"version": 21,
			"versionNonce": 319244437,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704460650,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					94,
					41
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "ji24htmUq6Q2xYNVUkp4o",
			"type": "line",
			"x": 68,
			"y": 29.7578125,
			"width": 31,
			"height": 38,
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
			"seed": 390326101,
			"version": 9,
			"versionNonce": 990844117,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704466158,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-31,
					38
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "bJ8pWFn4UR1HIcbNTrLJw",
			"type": "line",
			"x": 3,
			"y": 96.7578125,
			"width": 27,
			"height": 24,
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
			"seed": 1046458933,
			"version": 17,
			"versionNonce": 1066560821,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704469534,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-27,
					24
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "6AkJLM8W7jXRjATNkfEf3",
			"type": "line",
			"x": 48,
			"y": 88.7578125,
			"width": 56,
			"height": 44,
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
			"seed": 184413339,
			"version": 26,
			"versionNonce": 1188128533,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704472576,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					56,
					44
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "IeQYgMD5xPA8XqopVOoH2",
			"type": "line",
			"x": -52,
			"y": 146.7578125,
			"width": 14,
			"height": 20,
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
			"seed": 1454112955,
			"version": 11,
			"versionNonce": 1582115803,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704474696,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-14,
					20
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "Kcf_h-Mm2l2jPu24bUlN-",
			"type": "line",
			"x": -16,
			"y": 151.7578125,
			"width": 40,
			"height": 23,
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
			"seed": 1498436437,
			"version": 12,
			"versionNonce": 1272925083,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704476710,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					40,
					23
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "ew6RTEVFSjaJb0wHMUt4W",
			"type": "line",
			"x": -81,
			"y": 204.7578125,
			"width": 23,
			"height": 37,
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
			"seed": 1564544917,
			"version": 13,
			"versionNonce": 654810069,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704479033,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-23,
					37
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "zezq0--iO5vCP3E58uBlZ",
			"type": "line",
			"x": -52,
			"y": 212.7578125,
			"width": 12,
			"height": 30,
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
			"seed": 1672509435,
			"version": 9,
			"versionNonce": 1163076219,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704481084,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					12,
					30
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "tHTsJTwaESEh6hsHk7VPo",
			"type": "line",
			"x": 44,
			"y": 196.7578125,
			"width": 18,
			"height": 37,
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
			"seed": 1187157685,
			"version": 14,
			"versionNonce": 17091291,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704483169,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-18,
					37
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "lnoHEAZhXRUx4C2RlTeR9",
			"type": "line",
			"x": 55,
			"y": 199.7578125,
			"width": 32,
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
			"strokeSharpness": "round",
			"seed": 379987029,
			"version": 14,
			"versionNonce": 689545019,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704485650,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					32,
					39
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "60yJ4V4_X8yzogLa6j8PO",
			"type": "line",
			"x": -118,
			"y": 265.7578125,
			"width": 31,
			"height": 34,
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
			"seed": 1178551797,
			"version": 9,
			"versionNonce": 110502773,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704488301,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-31,
					34
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "4nM7joqjed8TUin4xk28n",
			"type": "line",
			"x": -95,
			"y": 265.7578125,
			"width": 24,
			"height": 30,
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
			"seed": 1943423291,
			"version": 17,
			"versionNonce": 425532987,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704494210,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					24,
					30
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "heinWo22ZE0qgHL9FZbc5",
			"type": "line",
			"x": 230,
			"y": 81.7578125,
			"width": 17,
			"height": 37,
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
			"seed": 1848876763,
			"version": 11,
			"versionNonce": 1252914683,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704497561,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-17,
					37
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "Vpyl02JbmUgbD4rJROMeN",
			"type": "line",
			"x": 267,
			"y": 82.7578125,
			"width": 34,
			"height": 43,
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
			"seed": 2010168117,
			"version": 23,
			"versionNonce": 1722336853,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704500403,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					34,
					43
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "DARk12Vi",
			"type": "text",
			"x": 213,
			"y": 158.7578125,
			"width": 29,
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
			"seed": 1912533525,
			"version": 11,
			"versionNonce": 1973001813,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704537661,
			"link": null,
			"locked": false,
			"text": ".....",
			"rawText": ".....",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "....."
		},
		{
			"id": "GnXTHe0R",
			"type": "text",
			"x": -437,
			"y": 461.7578125,
			"width": 241,
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
			"seed": 702352309,
			"version": 161,
			"versionNonce": 1636935675,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705190427,
			"link": null,
			"locked": false,
			"text": "Time: O(2^n) Space: O(1)",
			"rawText": "Time: O(2^n) Space: O(1)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Time: O(2^n) Space: O(1)"
		},
		{
			"id": "fVrzmH4u",
			"type": "text",
			"x": -444,
			"y": 265.7578125,
			"width": 429,
			"height": 175,
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
			"seed": 271776411,
			"version": 281,
			"versionNonce": 1632321627,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "UFZAFIuOb6VWEjzRy7nyH",
					"type": "arrow"
				}
			],
			"updated": 1669704734922,
			"link": null,
			"locked": false,
			"text": "fib(6)\nfib(5)\nfib(4) fib(4)\nfib(3) fib(3) fib(3)\nfib(2) fib(2) fib(2) fib(2) fib(2)\nfib(1) fib(1) fib(1) fib(1) fib(1) fib(1) fib(1) fib(1)\nfib(0) fib(0) fib(0) fib(0) fib(0)",
			"rawText": "fib(6)\nfib(5)\nfib(4) fib(4)\nfib(3) fib(3) fib(3)\nfib(2) fib(2) fib(2) fib(2) fib(2)\nfib(1) fib(1) fib(1) fib(1) fib(1) fib(1) fib(1) fib(1)\nfib(0) fib(0) fib(0) fib(0) fib(0)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 168,
			"containerId": null,
			"originalText": "fib(6)\nfib(5)\nfib(4) fib(4)\nfib(3) fib(3) fib(3)\nfib(2) fib(2) fib(2) fib(2) fib(2)\nfib(1) fib(1) fib(1) fib(1) fib(1) fib(1) fib(1) fib(1)\nfib(0) fib(0) fib(0) fib(0) fib(0)"
		},
		{
			"id": "UFZAFIuOb6VWEjzRy7nyH",
			"type": "arrow",
			"x": 153,
			"y": 253.7578125,
			"width": 154,
			"height": 118,
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
			"seed": 930141787,
			"version": 32,
			"versionNonce": 1024015733,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704734922,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-154,
					118
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": {
				"elementId": "fVrzmH4u",
				"focus": 0.7686278554117465,
				"gap": 14
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "AEXjqpeoYYzl-IP4vIasV",
			"type": "line",
			"x": 355,
			"y": 3.7578125,
			"width": 4,
			"height": 307,
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
			"seed": 987025787,
			"version": 111,
			"versionNonce": 163551195,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704777213,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					4,
					307
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "Zg5P3KtQ",
			"type": "text",
			"x": 377,
			"y": 97.7578125,
			"width": 57,
			"height": 50,
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
			"seed": 1263920251,
			"version": 14,
			"versionNonce": 1055205621,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704786962,
			"link": null,
			"locked": false,
			"text": "height\n6",
			"rawText": "height\n6",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 43,
			"containerId": null,
			"originalText": "height\n6"
		},
		{
			"id": "DLUgANS8",
			"type": "text",
			"x": 115,
			"y": 312.7578125,
			"width": 180,
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
			"seed": 1654538837,
			"version": 44,
			"versionNonce": 1010898587,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704804833,
			"link": null,
			"locked": false,
			"text": "roughly 2 ^6 calls",
			"rawText": "roughly 2 ^6 calls",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "roughly 2 ^6 calls"
		},
		{
			"id": "8hCZ6ivZ",
			"type": "text",
			"x": -426,
			"y": 561.7578125,
			"width": 171,
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
			"seed": 1960473205,
			"version": 41,
			"versionNonce": 1042332373,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704987933,
			"link": null,
			"locked": false,
			"text": "solution2: repeat",
			"rawText": "solution2: repeat",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "solution2: repeat"
		},
		{
			"id": "LZmWB9c6",
			"type": "text",
			"x": -425,
			"y": 608.7578125,
			"width": 356,
			"height": 175,
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
			"seed": 300867387,
			"version": 160,
			"versionNonce": 1691913557,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669704976039,
			"link": null,
			"locked": false,
			"text": "memo = new map{0:0, 1:1}\nfunction fib(n) {\n    if memo[n] is null\n        memo[n] = fib(n-1) + fib(n-2)\n    return memo[n]\n}\n",
			"rawText": "memo = new map{0:0, 1:1}\nfunction fib(n) {\n    if memo[n] is null\n        memo[n] = fib(n-1) + fib(n-2)\n    return memo[n]\n}\n",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 168,
			"containerId": null,
			"originalText": "memo = new map{0:0, 1:1}\nfunction fib(n) {\n    if memo[n] is null\n        memo[n] = fib(n-1) + fib(n-2)\n    return memo[n]\n}\n"
		},
		{
			"id": "wms4-JiWiEP7igIF0i_PY",
			"type": "rectangle",
			"x": -49,
			"y": 539.7578125,
			"width": 95,
			"height": 191,
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
			"seed": 2085394485,
			"version": 167,
			"versionNonce": 1049884725,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705176964,
			"link": null,
			"locked": false
		},
		{
			"id": "ZG5G4Xb4",
			"type": "text",
			"x": -39,
			"y": 502.7578125,
			"width": 49,
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
			"seed": 2082987131,
			"version": 55,
			"versionNonce": 154595509,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705181096,
			"link": null,
			"locked": false,
			"text": "memo",
			"rawText": "memo",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "memo"
		},
		{
			"id": "44Rhyrpr",
			"type": "text",
			"x": -31,
			"y": 553.7578125,
			"width": 54,
			"height": 150,
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
			"seed": 1651268821,
			"version": 81,
			"versionNonce": 334420635,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705275191,
			"link": null,
			"locked": false,
			"text": "0 : 0\n1 : 1\n5 : 5\n4 : 3\n3 : 2\n2 : 1",
			"rawText": "0 : 0\n1 : 1\n5 : 5\n4 : 3\n3 : 2\n2 : 1",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 143,
			"containerId": null,
			"originalText": "0 : 0\n1 : 1\n5 : 5\n4 : 3\n3 : 2\n2 : 1"
		},
		{
			"id": "BwRpJhzV",
			"type": "text",
			"x": 56,
			"y": 544.7578125,
			"width": 297,
			"height": 125,
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
			"seed": 79173429,
			"version": 226,
			"versionNonce": 1968258549,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705169039,
			"link": null,
			"locked": false,
			"text": "memo[5] = fib(4) + fib(3)\nmemo[4] = fib(3) + fib(2)\nmemo[3] = fib(2) + fib(1)\nmemo[2] = fib(1) + fib(0) \n         = memo[1] + memo[0)",
			"rawText": "memo[5] = fib(4) + fib(3)\nmemo[4] = fib(3) + fib(2)\nmemo[3] = fib(2) + fib(1)\nmemo[2] = fib(1) + fib(0) \n         = memo[1] + memo[0)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 118,
			"containerId": null,
			"originalText": "memo[5] = fib(4) + fib(3)\nmemo[4] = fib(3) + fib(2)\nmemo[3] = fib(2) + fib(1)\nmemo[2] = fib(1) + fib(0) \n         = memo[1] + memo[0)"
		},
		{
			"id": "2nPulWhz",
			"type": "text",
			"x": -429,
			"y": 776.7578125,
			"width": 220,
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
			"seed": 294072283,
			"version": 89,
			"versionNonce": 1617481019,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705389510,
			"link": null,
			"locked": false,
			"text": "Time: O(n) Space: O(n)",
			"rawText": "Time: O(n) Space: O(n)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Time: O(n) Space: O(n)"
		},
		{
			"id": "hV7hcX0Y",
			"type": "text",
			"x": -422,
			"y": 845.7578125,
			"width": 408,
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
			"seed": 1441885659,
			"version": 67,
			"versionNonce": 143954965,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705985787,
			"link": null,
			"locked": false,
			"text": "solution3: dynamic programming(bottom-up)",
			"rawText": "solution3: dynamic programming(bottom-up)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "solution3: dynamic programming(bottom-up)"
		},
		{
			"id": "vr5zd6GS",
			"type": "text",
			"x": -414,
			"y": 896.7578125,
			"width": 279,
			"height": 225,
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
			"seed": 1581600379,
			"version": 183,
			"versionNonce": 695337749,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705917944,
			"link": null,
			"locked": false,
			"text": "function fib(n) {\n    if n <= 1\n        return n\n    p1 = 1\n    p2 = 0\n    for i=2; i<=n; i++\n        p1, p2 = p2, p1 + p2\n    return p2\n}",
			"rawText": "function fib(n) {\n    if n <= 1\n        return n\n    p1 = 1\n    p2 = 0\n    for i=2; i<=n; i++\n        p1, p2 = p2, p1 + p2\n    return p2\n}",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 218,
			"containerId": null,
			"originalText": "function fib(n) {\n    if n <= 1\n        return n\n    p1 = 1\n    p2 = 0\n    for i=2; i<=n; i++\n        p1, p2 = p2, p1 + p2\n    return p2\n}"
		},
		{
			"id": "63pnsnCJ",
			"type": "text",
			"x": -95,
			"y": 933.7578125,
			"width": 71,
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
			"seed": 1391429589,
			"version": 42,
			"versionNonce": 853952571,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705591321,
			"link": null,
			"locked": false,
			"text": "0 1 1 2",
			"rawText": "0 1 1 2",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "0 1 1 2"
		},
		{
			"id": "gqODNbub",
			"type": "text",
			"x": -81,
			"y": 897.7578125,
			"width": 17,
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
			"seed": 1332314165,
			"version": 17,
			"versionNonce": 141551771,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705593118,
			"link": null,
			"locked": false,
			"text": "p1",
			"rawText": "p1",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "p1"
		},
		{
			"id": "1YczEoq1",
			"type": "text",
			"x": -40,
			"y": 899.7578125,
			"width": 26,
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
			"seed": 789454555,
			"version": 21,
			"versionNonce": 502067931,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705596110,
			"link": null,
			"locked": false,
			"text": "p2",
			"rawText": "p2",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "p2"
		},
		{
			"id": "vyH9VdFz",
			"type": "text",
			"x": 40,
			"y": 385.7578125,
			"width": 258,
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
			"seed": 929051,
			"version": 42,
			"versionNonce": 154108757,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705838074,
			"link": null,
			"locked": false,
			"text": "1 + 2 + 4 + 8 + 16 + 32",
			"rawText": "1 + 2 + 4 + 8 + 16 + 32",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "1 + 2 + 4 + 8 + 16 + 32"
		},
		{
			"id": "BJPAp33d",
			"type": "text",
			"x": -424,
			"y": 1136.7578125,
			"width": 216,
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
			"seed": 919263349,
			"version": 42,
			"versionNonce": 1469397467,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669705952505,
			"link": null,
			"locked": false,
			"text": "Time: O(n) Space: O(1)",
			"rawText": "Time: O(n) Space: O(1)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Time: O(n) Space: O(1)"
		},
		{
			"id": "rylJ8nmb",
			"type": "text",
			"x": -418,
			"y": 1192.7578125,
			"width": 245,
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
			"seed": 359876981,
			"version": 58,
			"versionNonce": 962566779,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706034444,
			"link": null,
			"locked": false,
			"text": "Solution4: math formula ",
			"rawText": "Solution4: math formula ",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Solution4: math formula "
		},
		{
			"id": "rlI8Ijgc7D87fe4Bob36A",
			"type": "freedraw",
			"x": -399,
			"y": 1253.7578125,
			"width": 25,
			"height": 95,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 226791989,
			"version": 31,
			"versionNonce": 811235669,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-1,
					0
				],
				[
					-2,
					0
				],
				[
					-3,
					0
				],
				[
					-4,
					4
				],
				[
					-6,
					8
				],
				[
					-6,
					14
				],
				[
					-7,
					22
				],
				[
					-7,
					30
				],
				[
					-7,
					37
				],
				[
					-7,
					52
				],
				[
					-7,
					65
				],
				[
					-7,
					72
				],
				[
					-7,
					82
				],
				[
					-7,
					89
				],
				[
					-7,
					91
				],
				[
					-9,
					94
				],
				[
					-9,
					95
				],
				[
					-10,
					95
				],
				[
					-11,
					95
				],
				[
					-12,
					95
				],
				[
					-13,
					95
				],
				[
					-14,
					93
				],
				[
					-16,
					90
				],
				[
					-18,
					86
				],
				[
					-21,
					83
				],
				[
					-25,
					77
				],
				[
					-25,
					76
				],
				[
					-25,
					76
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-25,
				76
			]
		},
		{
			"id": "EaugEuORRCjwKz2NoYfaB",
			"type": "freedraw",
			"x": -424,
			"y": 1309.7578125,
			"width": 34,
			"height": 6,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1927359189,
			"version": 11,
			"versionNonce": 1940140667,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					2,
					0
				],
				[
					4,
					0
				],
				[
					8,
					-1
				],
				[
					15,
					-2
				],
				[
					25,
					-4
				],
				[
					31,
					-5
				],
				[
					34,
					-6
				],
				[
					34,
					-6
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				34,
				-6
			]
		},
		{
			"id": "YgebrV9Cg1OK6FXpbyZmg",
			"type": "freedraw",
			"x": -382,
			"y": 1295.7578125,
			"width": 20,
			"height": 32,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 230868405,
			"version": 52,
			"versionNonce": 768247477,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-1,
					0
				],
				[
					-2,
					0
				],
				[
					-2,
					1
				],
				[
					-2,
					4
				],
				[
					-2,
					6
				],
				[
					-2,
					10
				],
				[
					-3,
					13
				],
				[
					-4,
					19
				],
				[
					-4,
					22
				],
				[
					-4,
					23
				],
				[
					-5,
					24
				],
				[
					-5,
					25
				],
				[
					-5,
					26
				],
				[
					-6,
					26
				],
				[
					-6,
					27
				],
				[
					-7,
					26
				],
				[
					-7,
					25
				],
				[
					-7,
					24
				],
				[
					-7,
					22
				],
				[
					-7,
					20
				],
				[
					-6,
					17
				],
				[
					-6,
					16
				],
				[
					-5,
					14
				],
				[
					-3,
					12
				],
				[
					-1,
					9
				],
				[
					1,
					7
				],
				[
					3,
					5
				],
				[
					4,
					5
				],
				[
					5,
					4
				],
				[
					5,
					3
				],
				[
					7,
					2
				],
				[
					8,
					2
				],
				[
					8,
					1
				],
				[
					9,
					1
				],
				[
					10,
					1
				],
				[
					11,
					4
				],
				[
					11,
					6
				],
				[
					11,
					11
				],
				[
					12,
					14
				],
				[
					12,
					19
				],
				[
					12,
					23
				],
				[
					12,
					27
				],
				[
					12,
					29
				],
				[
					12,
					30
				],
				[
					12,
					31
				],
				[
					13,
					31
				],
				[
					13,
					32
				],
				[
					13,
					31
				],
				[
					13,
					31
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				13,
				31
			]
		},
		{
			"id": "F0iDtfQTquso0qcZkAgFY",
			"type": "freedraw",
			"x": -392,
			"y": 1297.7578125,
			"width": 3,
			"height": 30,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 426254267,
			"version": 21,
			"versionNonce": 1326328603,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-1,
					1
				],
				[
					-2,
					1
				],
				[
					-3,
					2
				],
				[
					-3,
					3
				],
				[
					-3,
					4
				],
				[
					-3,
					7
				],
				[
					-3,
					11
				],
				[
					-3,
					17
				],
				[
					-3,
					19
				],
				[
					-3,
					23
				],
				[
					-3,
					26
				],
				[
					-3,
					27
				],
				[
					-2,
					28
				],
				[
					-2,
					30
				],
				[
					-1,
					30
				],
				[
					0,
					30
				],
				[
					0,
					29
				],
				[
					0,
					29
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				0,
				29
			]
		},
		{
			"id": "71oBra4W46foaJ-yQArZ4",
			"type": "freedraw",
			"x": -359,
			"y": 1300.7578125,
			"width": 6,
			"height": 30,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1176551931,
			"version": 15,
			"versionNonce": 277997589,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					0
				],
				[
					2,
					4
				],
				[
					3,
					7
				],
				[
					3,
					9
				],
				[
					3,
					14
				],
				[
					3,
					20
				],
				[
					2,
					22
				],
				[
					0,
					26
				],
				[
					-2,
					29
				],
				[
					-2,
					30
				],
				[
					-3,
					30
				],
				[
					-3,
					30
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-3,
				30
			]
		},
		{
			"id": "L3xWBRfsS5enk2B3EFR9l",
			"type": "freedraw",
			"x": -345,
			"y": 1309.7578125,
			"width": 15,
			"height": 2,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1321785947,
			"version": 12,
			"versionNonce": 1743785915,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					2,
					0
				],
				[
					6,
					-1
				],
				[
					7,
					-1
				],
				[
					8,
					-1
				],
				[
					9,
					-1
				],
				[
					10,
					-1
				],
				[
					14,
					-1
				],
				[
					15,
					-2
				],
				[
					15,
					-2
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				15,
				-2
			]
		},
		{
			"id": "4GKi9Jn9HgVvy-ycxi77W",
			"type": "freedraw",
			"x": -345,
			"y": 1327.7578125,
			"width": 19,
			"height": 4,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1944659893,
			"version": 11,
			"versionNonce": 1520835957,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					0
				],
				[
					10,
					-2
				],
				[
					12,
					-2
				],
				[
					16,
					-4
				],
				[
					17,
					-4
				],
				[
					18,
					-4
				],
				[
					19,
					-4
				],
				[
					19,
					-4
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				19,
				-4
			]
		},
		{
			"id": "FMf2AigUHB4LejWkA_tRr",
			"type": "freedraw",
			"x": -318,
			"y": 1248.7578125,
			"width": 12,
			"height": 37,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1171306133,
			"version": 20,
			"versionNonce": 543973467,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-2,
					0
				],
				[
					-3,
					0
				],
				[
					-4,
					0
				],
				[
					-6,
					2
				],
				[
					-8,
					7
				],
				[
					-9,
					10
				],
				[
					-10,
					17
				],
				[
					-10,
					25
				],
				[
					-10,
					27
				],
				[
					-9,
					32
				],
				[
					-6,
					35
				],
				[
					-5,
					36
				],
				[
					-4,
					37
				],
				[
					-3,
					37
				],
				[
					-1,
					36
				],
				[
					2,
					34
				],
				[
					2,
					34
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				2,
				34
			]
		},
		{
			"id": "chXUXabb7wI6E1hvoiidR",
			"type": "freedraw",
			"x": -303,
			"y": 1251.7578125,
			"width": 1,
			"height": 36,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1833221851,
			"version": 14,
			"versionNonce": 860661461,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					3
				],
				[
					1,
					8
				],
				[
					1,
					9
				],
				[
					1,
					15
				],
				[
					1,
					23
				],
				[
					1,
					29
				],
				[
					1,
					32
				],
				[
					1,
					34
				],
				[
					1,
					35
				],
				[
					1,
					36
				],
				[
					1,
					36
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				1,
				36
			]
		},
		{
			"id": "N3hbvVRluGzZ-ImWL7zRq",
			"type": "freedraw",
			"x": -295,
			"y": 1267.7578125,
			"width": 29,
			"height": 5,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1489398933,
			"version": 12,
			"versionNonce": 1918213371,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					-1
				],
				[
					1,
					-1
				],
				[
					4,
					-1
				],
				[
					9,
					-1
				],
				[
					13,
					-2
				],
				[
					21,
					-4
				],
				[
					25,
					-5
				],
				[
					29,
					-5
				],
				[
					29,
					-5
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				29,
				-5
			]
		},
		{
			"id": "kR-d_SITEEyPhS56iPKdd",
			"type": "freedraw",
			"x": -281,
			"y": 1252.7578125,
			"width": 4,
			"height": 28,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 374976091,
			"version": 12,
			"versionNonce": 1025180725,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					1
				],
				[
					1,
					3
				],
				[
					1,
					8
				],
				[
					1,
					14
				],
				[
					2,
					21
				],
				[
					3,
					24
				],
				[
					3,
					27
				],
				[
					4,
					28
				],
				[
					4,
					28
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				4,
				28
			]
		},
		{
			"id": "I12lpDi2vE3YH_-mkCI75",
			"type": "freedraw",
			"x": -262,
			"y": 1242.7578125,
			"width": 26,
			"height": 1,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1643910069,
			"version": 11,
			"versionNonce": 2033075611,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					0
				],
				[
					4,
					1
				],
				[
					8,
					1
				],
				[
					15,
					1
				],
				[
					19,
					1
				],
				[
					24,
					1
				],
				[
					26,
					1
				],
				[
					26,
					1
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				26,
				1
			]
		},
		{
			"id": "7BCq8r710HkaMR59axAHp",
			"type": "freedraw",
			"x": -261,
			"y": 1246.7578125,
			"width": 8,
			"height": 32,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1854939797,
			"version": 29,
			"versionNonce": 737588629,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					1
				],
				[
					0,
					3
				],
				[
					0,
					4
				],
				[
					0,
					8
				],
				[
					0,
					11
				],
				[
					0,
					13
				],
				[
					0,
					17
				],
				[
					0,
					19
				],
				[
					0,
					18
				],
				[
					-1,
					17
				],
				[
					-2,
					17
				],
				[
					-2,
					16
				],
				[
					-3,
					16
				],
				[
					-4,
					16
				],
				[
					-4,
					15
				],
				[
					-5,
					15
				],
				[
					-6,
					15
				],
				[
					-7,
					15
				],
				[
					-8,
					14
				],
				[
					-8,
					15
				],
				[
					-8,
					22
				],
				[
					-6,
					27
				],
				[
					-6,
					29
				],
				[
					-6,
					31
				],
				[
					-5,
					32
				],
				[
					-5,
					32
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-5,
				32
			]
		},
		{
			"id": "f9UO6feGP_3AC9W8Fr9YH",
			"type": "freedraw",
			"x": -253,
			"y": 1255.7578125,
			"width": 18,
			"height": 27,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 98601429,
			"version": 31,
			"versionNonce": 1009310267,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					1
				],
				[
					0,
					2
				],
				[
					0,
					3
				],
				[
					0,
					5
				],
				[
					0,
					8
				],
				[
					0,
					9
				],
				[
					0,
					10
				],
				[
					0,
					11
				],
				[
					0,
					12
				],
				[
					1,
					13
				],
				[
					3,
					13
				],
				[
					5,
					13
				],
				[
					10,
					13
				],
				[
					14,
					13
				],
				[
					15,
					13
				],
				[
					16,
					13
				],
				[
					16,
					14
				],
				[
					16,
					15
				],
				[
					16,
					17
				],
				[
					13,
					20
				],
				[
					11,
					22
				],
				[
					7,
					24
				],
				[
					3,
					26
				],
				[
					1,
					27
				],
				[
					0,
					27
				],
				[
					-1,
					27
				],
				[
					-2,
					27
				],
				[
					-2,
					27
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-2,
				27
			]
		},
		{
			"id": "tvfNX0gaIdubM691Jvxa7",
			"type": "freedraw",
			"x": -255,
			"y": 1260.7578125,
			"width": 19,
			"height": 5,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 942691957,
			"version": 15,
			"versionNonce": 454376181,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					0
				],
				[
					4,
					-1
				],
				[
					6,
					-1
				],
				[
					10,
					-3
				],
				[
					11,
					-3
				],
				[
					12,
					-3
				],
				[
					13,
					-3
				],
				[
					14,
					-3
				],
				[
					17,
					-4
				],
				[
					19,
					-4
				],
				[
					19,
					-5
				],
				[
					19,
					-5
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				19,
				-5
			]
		},
		{
			"id": "Gv8cAHDJ5nJPmp9aul8bE",
			"type": "freedraw",
			"x": -304,
			"y": 1300.7578125,
			"width": 103,
			"height": 16,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1488788501,
			"version": 17,
			"versionNonce": 942136027,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					-1
				],
				[
					1,
					-1
				],
				[
					3,
					-2
				],
				[
					12,
					-3
				],
				[
					15,
					-3
				],
				[
					32,
					-5
				],
				[
					62,
					-10
				],
				[
					74,
					-11
				],
				[
					86,
					-13
				],
				[
					101,
					-15
				],
				[
					103,
					-15
				],
				[
					103,
					-16
				],
				[
					102,
					-16
				],
				[
					102,
					-16
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				102,
				-16
			]
		},
		{
			"id": "oV-U5HfdbFumdwM19Dajw",
			"type": "freedraw",
			"x": -257,
			"y": 1303.7578125,
			"width": 35,
			"height": 19,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1484781333,
			"version": 27,
			"versionNonce": 586945621,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					0
				],
				[
					4,
					0
				],
				[
					8,
					0
				],
				[
					10,
					0
				],
				[
					11,
					0
				],
				[
					12,
					0
				],
				[
					12,
					1
				],
				[
					12,
					3
				],
				[
					12,
					6
				],
				[
					12,
					8
				],
				[
					11,
					9
				],
				[
					9,
					10
				],
				[
					5,
					13
				],
				[
					0,
					16
				],
				[
					-5,
					17
				],
				[
					-6,
					18
				],
				[
					-3,
					18
				],
				[
					2,
					19
				],
				[
					12,
					19
				],
				[
					18,
					19
				],
				[
					22,
					19
				],
				[
					28,
					18
				],
				[
					29,
					18
				],
				[
					29,
					18
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				29,
				18
			]
		},
		{
			"id": "BC2fArMXkpG_opmuhcDDW",
			"type": "freedraw",
			"x": -203,
			"y": 1241.7578125,
			"width": 15,
			"height": 88,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1057547509,
			"version": 21,
			"versionNonce": 700303227,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					2,
					5
				],
				[
					4,
					10
				],
				[
					6,
					15
				],
				[
					9,
					24
				],
				[
					11,
					33
				],
				[
					11,
					36
				],
				[
					11,
					41
				],
				[
					11,
					49
				],
				[
					11,
					55
				],
				[
					8,
					68
				],
				[
					3,
					78
				],
				[
					1,
					83
				],
				[
					-2,
					87
				],
				[
					-2,
					88
				],
				[
					-3,
					88
				],
				[
					-4,
					88
				],
				[
					-4,
					87
				],
				[
					-4,
					87
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-4,
				87
			]
		},
		{
			"id": "t5TAyyZlH3JtC-V0GDihh",
			"type": "freedraw",
			"x": -190,
			"y": 1233.7578125,
			"width": 17,
			"height": 20,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 508765877,
			"version": 36,
			"versionNonce": 485673563,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706129511,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					1
				],
				[
					0,
					4
				],
				[
					-1,
					8
				],
				[
					-1,
					9
				],
				[
					-2,
					10
				],
				[
					-3,
					11
				],
				[
					-3,
					13
				],
				[
					-4,
					13
				],
				[
					-4,
					10
				],
				[
					-4,
					9
				],
				[
					-4,
					5
				],
				[
					-4,
					2
				],
				[
					-4,
					1
				],
				[
					-3,
					-1
				],
				[
					-1,
					-4
				],
				[
					0,
					-5
				],
				[
					1,
					-6
				],
				[
					2,
					-6
				],
				[
					2,
					-7
				],
				[
					3,
					-6
				],
				[
					3,
					-5
				],
				[
					4,
					-2
				],
				[
					5,
					-1
				],
				[
					5,
					2
				],
				[
					5,
					7
				],
				[
					5,
					9
				],
				[
					6,
					11
				],
				[
					7,
					12
				],
				[
					8,
					12
				],
				[
					10,
					12
				],
				[
					11,
					12
				],
				[
					13,
					12
				],
				[
					13,
					12
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				13,
				12
			]
		},
		{
			"id": "rVTckLRZ5-_M86TBe_vet",
			"type": "freedraw",
			"x": -173,
			"y": 1280.7578125,
			"width": 38,
			"height": 0,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1231100347,
			"version": 12,
			"versionNonce": 796967349,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					0
				],
				[
					3,
					0
				],
				[
					6,
					0
				],
				[
					15,
					0
				],
				[
					25,
					0
				],
				[
					31,
					0
				],
				[
					37,
					0
				],
				[
					38,
					0
				],
				[
					38,
					0
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				38,
				0
			]
		},
		{
			"id": "Nzv2-kC66RWlyqh99SfKX",
			"type": "freedraw",
			"x": -102,
			"y": 1256.7578125,
			"width": 21,
			"height": 72,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1708845653,
			"version": 21,
			"versionNonce": 388833307,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-1,
					0
				],
				[
					-3,
					0
				],
				[
					-6,
					2
				],
				[
					-11,
					6
				],
				[
					-13,
					9
				],
				[
					-18,
					14
				],
				[
					-21,
					22
				],
				[
					-21,
					25
				],
				[
					-21,
					30
				],
				[
					-21,
					40
				],
				[
					-21,
					44
				],
				[
					-20,
					56
				],
				[
					-14,
					66
				],
				[
					-10,
					69
				],
				[
					-9,
					71
				],
				[
					-5,
					72
				],
				[
					-3,
					72
				],
				[
					-3,
					72
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-3,
				72
			]
		},
		{
			"id": "PF_Nyps2Ekz6zMxSPW8GQ",
			"type": "freedraw",
			"x": -79,
			"y": 1245.7578125,
			"width": 2,
			"height": 24,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 950006805,
			"version": 19,
			"versionNonce": 365478677,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					2
				],
				[
					0,
					4
				],
				[
					1,
					7
				],
				[
					1,
					10
				],
				[
					1,
					12
				],
				[
					1,
					14
				],
				[
					1,
					16
				],
				[
					1,
					17
				],
				[
					1,
					18
				],
				[
					2,
					19
				],
				[
					2,
					20
				],
				[
					2,
					21
				],
				[
					2,
					22
				],
				[
					2,
					23
				],
				[
					2,
					24
				],
				[
					2,
					24
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				2,
				24
			]
		},
		{
			"id": "MI3gdvEK-bZHun_IPo9qr",
			"type": "freedraw",
			"x": -62,
			"y": 1253.7578125,
			"width": 18,
			"height": 2,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 510272629,
			"version": 9,
			"versionNonce": 443049147,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					0
				],
				[
					6,
					0
				],
				[
					9,
					0
				],
				[
					14,
					-1
				],
				[
					18,
					-2
				],
				[
					18,
					-2
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				18,
				-2
			]
		},
		{
			"id": "N_ScyMoe0S99rSDu8eKuZ",
			"type": "freedraw",
			"x": -29,
			"y": 1235.7578125,
			"width": 59,
			"height": 5,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1696449013,
			"version": 10,
			"versionNonce": 1783208053,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					0
				],
				[
					7,
					0
				],
				[
					28,
					0
				],
				[
					39,
					-1
				],
				[
					53,
					-3
				],
				[
					59,
					-5
				],
				[
					59,
					-5
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				59,
				-5
			]
		},
		{
			"id": "D2EehQK5zO-qoD6a_xNmK",
			"type": "freedraw",
			"x": -18,
			"y": 1235.7578125,
			"width": 12,
			"height": 32,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1501363803,
			"version": 32,
			"versionNonce": 1796347227,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					3
				],
				[
					0,
					8
				],
				[
					0,
					11
				],
				[
					0,
					12
				],
				[
					0,
					16
				],
				[
					0,
					17
				],
				[
					0,
					18
				],
				[
					0,
					19
				],
				[
					-2,
					19
				],
				[
					-3,
					19
				],
				[
					-5,
					19
				],
				[
					-7,
					19
				],
				[
					-8,
					17
				],
				[
					-9,
					16
				],
				[
					-10,
					15
				],
				[
					-11,
					14
				],
				[
					-11,
					13
				],
				[
					-11,
					12
				],
				[
					-12,
					12
				],
				[
					-12,
					13
				],
				[
					-12,
					15
				],
				[
					-11,
					17
				],
				[
					-11,
					22
				],
				[
					-10,
					25
				],
				[
					-10,
					27
				],
				[
					-10,
					30
				],
				[
					-10,
					32
				],
				[
					-9,
					32
				],
				[
					-9,
					32
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-9,
				32
			]
		},
		{
			"id": "sq3_jE4IoDtXXALCRiW9W",
			"type": "freedraw",
			"x": -6,
			"y": 1246.7578125,
			"width": 16,
			"height": 30,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 2045678965,
			"version": 30,
			"versionNonce": 382255573,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					1
				],
				[
					0,
					5
				],
				[
					0,
					6
				],
				[
					0,
					10
				],
				[
					0,
					11
				],
				[
					0,
					14
				],
				[
					2,
					15
				],
				[
					3,
					15
				],
				[
					4,
					16
				],
				[
					5,
					17
				],
				[
					5,
					18
				],
				[
					5,
					19
				],
				[
					5,
					20
				],
				[
					5,
					21
				],
				[
					4,
					22
				],
				[
					1,
					25
				],
				[
					-2,
					28
				],
				[
					-3,
					28
				],
				[
					-5,
					29
				],
				[
					-5,
					30
				],
				[
					-6,
					30
				],
				[
					-7,
					30
				],
				[
					-8,
					29
				],
				[
					-9,
					28
				],
				[
					-10,
					27
				],
				[
					-11,
					26
				],
				[
					-11,
					26
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-11,
				26
			]
		},
		{
			"id": "kBDz_sMksnY1he3PkbzUk",
			"type": "freedraw",
			"x": 4,
			"y": 1247.7578125,
			"width": 12,
			"height": 3,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1366564123,
			"version": 8,
			"versionNonce": 679260667,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					5,
					-1
				],
				[
					7,
					-1
				],
				[
					10,
					-2
				],
				[
					12,
					-3
				],
				[
					12,
					-3
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				12,
				-3
			]
		},
		{
			"id": "VQe5FvytU1HOzFgy_FOjf",
			"type": "freedraw",
			"x": -91,
			"y": 1292.7578125,
			"width": 110,
			"height": 9,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1284239925,
			"version": 21,
			"versionNonce": 811319093,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					0
				],
				[
					3,
					0
				],
				[
					11,
					0
				],
				[
					21,
					-1
				],
				[
					31,
					-2
				],
				[
					48,
					-4
				],
				[
					69,
					-6
				],
				[
					78,
					-6
				],
				[
					84,
					-6
				],
				[
					85,
					-6
				],
				[
					87,
					-7
				],
				[
					91,
					-8
				],
				[
					92,
					-8
				],
				[
					95,
					-8
				],
				[
					100,
					-8
				],
				[
					105,
					-8
				],
				[
					110,
					-9
				],
				[
					110,
					-9
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				110,
				-9
			]
		},
		{
			"id": "f24CeT6QX-wpFw2TBYCFU",
			"type": "freedraw",
			"x": -33,
			"y": 1307.7578125,
			"width": 63,
			"height": 21,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 593667061,
			"version": 31,
			"versionNonce": 1672133275,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					2,
					0
				],
				[
					5,
					0
				],
				[
					8,
					0
				],
				[
					12,
					0
				],
				[
					12,
					1
				],
				[
					13,
					1
				],
				[
					13,
					3
				],
				[
					12,
					5
				],
				[
					8,
					10
				],
				[
					3,
					14
				],
				[
					1,
					16
				],
				[
					-2,
					18
				],
				[
					-4,
					20
				],
				[
					-5,
					20
				],
				[
					-6,
					20
				],
				[
					-5,
					21
				],
				[
					1,
					21
				],
				[
					8,
					21
				],
				[
					25,
					21
				],
				[
					45,
					19
				],
				[
					53,
					18
				],
				[
					56,
					18
				],
				[
					57,
					18
				],
				[
					56,
					18
				],
				[
					56,
					17
				],
				[
					56,
					16
				],
				[
					55,
					15
				],
				[
					55,
					15
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				55,
				15
			]
		},
		{
			"id": "jz6e9Tag3fac-X45LLcQr",
			"type": "freedraw",
			"x": 70,
			"y": 1241.7578125,
			"width": 17,
			"height": 86,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1759704213,
			"version": 19,
			"versionNonce": 224923797,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					1
				],
				[
					3,
					4
				],
				[
					5,
					9
				],
				[
					7,
					17
				],
				[
					9,
					25
				],
				[
					9,
					31
				],
				[
					9,
					46
				],
				[
					6,
					57
				],
				[
					0,
					74
				],
				[
					-4,
					83
				],
				[
					-5,
					85
				],
				[
					-5,
					86
				],
				[
					-6,
					86
				],
				[
					-7,
					86
				],
				[
					-8,
					86
				],
				[
					-8,
					86
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-8,
				86
			]
		},
		{
			"id": "e26PZOAoYdcC1ApcCyDzH",
			"type": "freedraw",
			"x": 79,
			"y": 1219.7578125,
			"width": 30,
			"height": 35,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 86021365,
			"version": 40,
			"versionNonce": 645842683,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706131701,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					0
				],
				[
					1,
					2
				],
				[
					1,
					3
				],
				[
					1,
					8
				],
				[
					1,
					10
				],
				[
					1,
					13
				],
				[
					0,
					15
				],
				[
					0,
					16
				],
				[
					-1,
					16
				],
				[
					-1,
					15
				],
				[
					-1,
					14
				],
				[
					-1,
					10
				],
				[
					-1,
					7
				],
				[
					0,
					4
				],
				[
					3,
					0
				],
				[
					5,
					-4
				],
				[
					7,
					-7
				],
				[
					10,
					-10
				],
				[
					13,
					-14
				],
				[
					14,
					-15
				],
				[
					16,
					-18
				],
				[
					18,
					-18
				],
				[
					18,
					-19
				],
				[
					19,
					-19
				],
				[
					20,
					-19
				],
				[
					20,
					-17
				],
				[
					22,
					-12
				],
				[
					23,
					-10
				],
				[
					24,
					-3
				],
				[
					24,
					-1
				],
				[
					25,
					0
				],
				[
					25,
					3
				],
				[
					26,
					4
				],
				[
					27,
					4
				],
				[
					28,
					4
				],
				[
					29,
					4
				],
				[
					29,
					4
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				29,
				4
			]
		},
		{
			"id": "TbNqZza1JBJ72RYu__0oS",
			"type": "freedraw",
			"x": -290,
			"y": 1355.7578125,
			"width": 467,
			"height": 24,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1670059195,
			"version": 23,
			"versionNonce": 1766764347,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					2,
					0
				],
				[
					4,
					0
				],
				[
					12,
					0
				],
				[
					28,
					0
				],
				[
					42,
					0
				],
				[
					84,
					0
				],
				[
					161,
					0
				],
				[
					208,
					-4
				],
				[
					296,
					-12
				],
				[
					360,
					-17
				],
				[
					383,
					-18
				],
				[
					428,
					-21
				],
				[
					454,
					-22
				],
				[
					462,
					-23
				],
				[
					465,
					-23
				],
				[
					466,
					-24
				],
				[
					467,
					-24
				],
				[
					464,
					-24
				],
				[
					462,
					-24
				],
				[
					462,
					-24
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				462,
				-24
			]
		},
		{
			"id": "yRRBY-aS7tAy2PvEp-0V3",
			"type": "freedraw",
			"x": -172,
			"y": 1387.7578125,
			"width": 130,
			"height": 16,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 483581851,
			"version": 15,
			"versionNonce": 463681013,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					4,
					-1
				],
				[
					23,
					-3
				],
				[
					55,
					-8
				],
				[
					70,
					-9
				],
				[
					107,
					-13
				],
				[
					127,
					-15
				],
				[
					129,
					-15
				],
				[
					130,
					-15
				],
				[
					130,
					-16
				],
				[
					129,
					-16
				],
				[
					127,
					-16
				],
				[
					127,
					-16
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				127,
				-16
			]
		},
		{
			"id": "8T6uBeBJgNGv-Qn2heZv-",
			"type": "freedraw",
			"x": -171,
			"y": 1388.7578125,
			"width": 26,
			"height": 40,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 171084795,
			"version": 31,
			"versionNonce": 1271599067,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					1
				],
				[
					0,
					5
				],
				[
					1,
					8
				],
				[
					1,
					11
				],
				[
					1,
					17
				],
				[
					1,
					22
				],
				[
					2,
					24
				],
				[
					2,
					25
				],
				[
					0,
					25
				],
				[
					-3,
					25
				],
				[
					-7,
					22
				],
				[
					-10,
					20
				],
				[
					-16,
					16
				],
				[
					-20,
					11
				],
				[
					-22,
					11
				],
				[
					-24,
					9
				],
				[
					-24,
					12
				],
				[
					-24,
					14
				],
				[
					-24,
					17
				],
				[
					-24,
					22
				],
				[
					-24,
					28
				],
				[
					-24,
					33
				],
				[
					-23,
					35
				],
				[
					-23,
					38
				],
				[
					-22,
					39
				],
				[
					-22,
					40
				],
				[
					-21,
					40
				],
				[
					-21,
					40
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-21,
				40
			]
		},
		{
			"id": "q4BG17kTQrF_85zafjKVB",
			"type": "freedraw",
			"x": -121,
			"y": 1403.7578125,
			"width": 48,
			"height": 40,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 310614363,
			"version": 26,
			"versionNonce": 1980936021,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					2
				],
				[
					0,
					5
				],
				[
					0,
					7
				],
				[
					0,
					11
				],
				[
					4,
					16
				],
				[
					9,
					20
				],
				[
					15,
					22
				],
				[
					18,
					25
				],
				[
					20,
					25
				],
				[
					20,
					26
				],
				[
					16,
					29
				],
				[
					14,
					31
				],
				[
					3,
					35
				],
				[
					-6,
					39
				],
				[
					-12,
					40
				],
				[
					-14,
					40
				],
				[
					-18,
					40
				],
				[
					-19,
					40
				],
				[
					-23,
					37
				],
				[
					-26,
					33
				],
				[
					-27,
					31
				],
				[
					-28,
					31
				],
				[
					-28,
					31
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				-28,
				31
			]
		},
		{
			"id": "4XWIm1Ssks3ZQMp0jRqc7",
			"type": "freedraw",
			"x": -128,
			"y": 1406.7578125,
			"width": 46,
			"height": 14,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1446839893,
			"version": 14,
			"versionNonce": 758275195,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					0
				],
				[
					6,
					-1
				],
				[
					12,
					-2
				],
				[
					17,
					-3
				],
				[
					32,
					-9
				],
				[
					42,
					-12
				],
				[
					44,
					-13
				],
				[
					45,
					-13
				],
				[
					46,
					-13
				],
				[
					46,
					-14
				],
				[
					46,
					-14
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				46,
				-14
			]
		},
		{
			"id": "x_yS9i-6yPx23OwhzygGs",
			"type": "freedraw",
			"x": -322,
			"y": 1251.7578125,
			"width": 16,
			"height": 73,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 63021397,
			"version": 58,
			"versionNonce": 1229395227,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706126566,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					1
				],
				[
					0,
					2
				],
				[
					0,
					3
				],
				[
					0,
					4
				],
				[
					-1,
					5
				],
				[
					-1,
					6
				],
				[
					-1,
					7
				],
				[
					-1,
					8
				],
				[
					-1,
					9
				],
				[
					-1,
					10
				],
				[
					-1,
					11
				],
				[
					-2,
					12
				],
				[
					-2,
					13
				],
				[
					-3,
					15
				],
				[
					-3,
					16
				],
				[
					-3,
					17
				],
				[
					-3,
					18
				],
				[
					-3,
					19
				],
				[
					-3,
					20
				],
				[
					-3,
					21
				],
				[
					-3,
					22
				],
				[
					-3,
					23
				],
				[
					-3,
					24
				],
				[
					-3,
					26
				],
				[
					-3,
					28
				],
				[
					-3,
					30
				],
				[
					-3,
					31
				],
				[
					-3,
					34
				],
				[
					-2,
					35
				],
				[
					-2,
					36
				],
				[
					-2,
					39
				],
				[
					-1,
					41
				],
				[
					0,
					43
				],
				[
					0,
					46
				],
				[
					2,
					48
				],
				[
					2,
					51
				],
				[
					4,
					53
				],
				[
					4,
					55
				],
				[
					5,
					57
				],
				[
					6,
					59
				],
				[
					7,
					62
				],
				[
					8,
					64
				],
				[
					9,
					66
				],
				[
					9,
					67
				],
				[
					9,
					68
				],
				[
					10,
					68
				],
				[
					10,
					69
				],
				[
					10,
					70
				],
				[
					11,
					70
				],
				[
					12,
					70
				],
				[
					12,
					71
				],
				[
					13,
					71
				],
				[
					13,
					72
				],
				[
					13,
					73
				],
				[
					13,
					73
				]
			],
			"pressures": [],
			"simulatePressure": true,
			"lastCommittedPoint": [
				13,
				73
			]
		},
		{
			"id": "LqpUUyd1",
			"type": "text",
			"x": -408,
			"y": 1468.7578125,
			"width": 96,
			"height": 25,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1802254261,
			"version": 18,
			"versionNonce": 1064042267,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706154385,
			"link": null,
			"locked": false,
			"text": "Time: O(1)",
			"rawText": "Time: O(1)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Time: O(1)"
		},
		{
			"id": "BKlaKMUXfA8TyNzoiY93k",
			"type": "line",
			"x": -433,
			"y": 1531.7578125,
			"width": 827,
			"height": 7,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 896754619,
			"version": 20,
			"versionNonce": 1581985237,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706281421,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					827,
					-7
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "loKpkmABlC2Db5SQ39g44",
			"type": "line",
			"x": -441,
			"y": -119.2421875,
			"width": 798,
			"height": 9,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 883993397,
			"version": 19,
			"versionNonce": 389666709,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706291150,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					798,
					-9
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "DPlxDNB5",
			"type": "text",
			"x": -416,
			"y": 1658.7578125,
			"width": 501,
			"height": 300,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 899947765,
			"version": 523,
			"versionNonce": 1895112437,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706765728,
			"link": null,
			"locked": false,
			"text": "function coinChange(coins, amount) {\n    a = initialize array with size(amount + 1)\n    Fill a with (amount + 1)\n    a[0] = 0\n    for i=1; i<amount+1; i++\n        for j=0; j<length of coins; j++\n            if coins[j] <= i\n                a[i] = min(a[i], a[i - coins[j]] + 1)\n    if a[amount] > amount\n        return -1\n    return a[amount]\n}",
			"rawText": "function coinChange(coins, amount) {\n    a = initialize array with size(amount + 1)\n    Fill a with (amount + 1)\n    a[0] = 0\n    for i=1; i<amount+1; i++\n        for j=0; j<length of coins; j++\n            if coins[j] <= i\n                a[i] = min(a[i], a[i - coins[j]] + 1)\n    if a[amount] > amount\n        return -1\n    return a[amount]\n}",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 293,
			"containerId": null,
			"originalText": "function coinChange(coins, amount) {\n    a = initialize array with size(amount + 1)\n    Fill a with (amount + 1)\n    a[0] = 0\n    for i=1; i<amount+1; i++\n        for j=0; j<length of coins; j++\n            if coins[j] <= i\n                a[i] = min(a[i], a[i - coins[j]] + 1)\n    if a[amount] > amount\n        return -1\n    return a[amount]\n}"
		},
		{
			"id": "rbb8eEZM",
			"type": "text",
			"x": 203,
			"y": 1744.7578125,
			"width": 95,
			"height": 25,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1310541845,
			"version": 157,
			"versionNonce": 464008923,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706765728,
			"link": null,
			"locked": false,
			"text": "amount(n)",
			"rawText": "amount(n)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "amount(n)"
		},
		{
			"id": "epDFZwmF",
			"type": "text",
			"x": 219,
			"y": 1783.7578125,
			"width": 179,
			"height": 25,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1484747861,
			"version": 97,
			"versionNonce": 931342101,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1669706765961,
			"link": null,
			"locked": false,
			"text": "number of coins(m)",
			"rawText": "number of coins(m)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "number of coins(m)"
		},
		{
			"id": "pXe0IoLkG1MHYlBcI5ieV",
			"type": "arrow",
			"x": -7,
			"y": 1753.7578125,
			"width": 194,
			"height": 3,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1748311899,
			"version": 153,
			"versionNonce": 2067757141,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706765728,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					194,
					3
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "ocCRUcVqzy2DsvdMj0Te3",
			"type": "arrow",
			"x": -24,
			"y": 1793.7578125,
			"width": 210,
			"height": 7,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1924826869,
			"version": 242,
			"versionNonce": 1665563675,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706765961,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					210,
					7
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "iFdPh7fx",
			"type": "text",
			"x": -426,
			"y": 1979.7578125,
			"width": 243,
			"height": 25,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 686876027,
			"version": 180,
			"versionNonce": 1907860917,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706765728,
			"link": null,
			"locked": false,
			"text": "Time: O(m*n) Space: O(n)",
			"rawText": "Time: O(m*n) Space: O(n)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Time: O(m*n) Space: O(n)"
		},
		{
			"id": "jt1CgIkJ",
			"type": "text",
			"x": -420,
			"y": 1579.7578125,
			"width": 367,
			"height": 25,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1456088475,
			"version": 134,
			"versionNonce": 1199159317,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669706774179,
			"link": null,
			"locked": false,
			"text": "daynimic programming fix coins problem",
			"rawText": "daynimic programming fix coins problem",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "daynimic programming fix coins problem"
		},
		{
			"id": "tn4WPsZk",
			"type": "text",
			"x": -255,
			"y": 1616.7578125,
			"width": 12,
			"height": 25,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 71536123,
			"version": 3,
			"versionNonce": 1513494171,
			"isDeleted": true,
			"boundElements": null,
			"updated": 1669706769264,
			"link": null,
			"locked": false,
			"text": "",
			"rawText": "",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": ""
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#000000",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "hachure",
		"currentItemStrokeWidth": 0.5,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 1,
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