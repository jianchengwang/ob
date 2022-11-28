---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
Priority Queue ADT

Max-Oriented <--> Min-Oriented ^36Ankm7s

IMPLEMENTS/ACT ^HRvB2hJ4

INSERT ^zEWET9me

DELETE_MAX ^TlvSBtkg

Array ^b8goIawl

Sorted Array ^D7nN1039

Heaps ^s3WeLbVi

O(1) ^hRD8z8fR

O(n) ^tMOeJPIi

O(n) ^TacgNFkx

O(1) ^zREHpDhA

30 ^73VilfM1

19 ^bXsWjc0y

25 ^QsZ091JO

18 ^TjMO3iKk

15 ^ZGut2Paz

5 ^yevAA8WF

16 ^9dZDp2Vv

2 ^m6ToPBXN

4 ^9Yb9WJBr

Binary Max Heap ^1HcNlUUP

Structual Property: 
levels completely filled 
left to right ^rbaTcxBH

Heap-order Property:
parent key larger or 
equals to children ^RLLBCNfz

Max Heap ^inBWdAz9

Balanced ^2VbyHzd8

Left Child of index i:
2 * i + 1
Right Child of Index i:
2 * i + 2
Parent of index i:
(i - 2)/2 ^bmVXS37b

30 ^whtkzVAX

19 ^t2h083RO

25 ^mGTZWJEA

18 ^W4GHbrxy

15 ^tZXR5eCu

2 ^JYTumYQZ

4 ^L7lgu6Pg

5 ^CGoj9p06

16 ^7fzd0WWl

Array ^ER8thw9y

function fix_down(A, n, i) {
    while i is not a leaf
        j = left_child(i)
        if A[j] < A[j+1]
            j += 1
        swap(A[i], A[j])
        i = j
} ^yqDbhPMS

O(logn) ^0e6iS6uw

O(logn) ^rW3JeHqo

function fix_up(A, i) {
    while parent(i) exists and A[parent(i)] < A[i]
        swap(A[i], A[parent(i))
        i = parent(i)
} ^3wDwPGiD

function insert(maxheap, x) {
    maxheap.n += 1
    maxheap.arr[haxheap.n-1] = x
    fix_up(maxheap.arr, maxheap.n-1)
} ^tXI03ePA

function delete_max(maxheap) {
    swap(maxheap.arr[0], maxheap.arr[maxheap.n-1])
    maxheap.n -= 1
    fix_down(maxheap.arr, maxheap.n, 0)
    return arr[maxheap.n]
} ^nUsB8uSC

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
			"version": 261,
			"versionNonce": 935285849,
			"isDeleted": false,
			"id": "36Ankm7s",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -668.0000000000002,
			"y": -716.2421875000003,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 308,
			"height": 75,
			"seed": 184906105,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669625663534,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Priority Queue ADT\n\nMax-Oriented <--> Min-Oriented",
			"rawText": "Priority Queue ADT\n\nMax-Oriented <--> Min-Oriented",
			"baseline": 68,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Priority Queue ADT\n\nMax-Oriented <--> Min-Oriented"
		},
		{
			"type": "rectangle",
			"version": 177,
			"versionNonce": 1035082137,
			"isDeleted": false,
			"id": "jLHZ-10JgjiLHBw-Sj69D",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -677.0000000000002,
			"y": -605.4843750000001,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 507,
			"height": 316.99999999999994,
			"seed": 17090135,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669627457470,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 102,
			"versionNonce": 476405815,
			"isDeleted": false,
			"id": "HRvB2hJ4",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -667.5000000000005,
			"y": -587.9843750000002,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 189,
			"height": 25,
			"seed": 1008846393,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669625716673,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "IMPLEMENTS/ACT",
			"rawText": "IMPLEMENTS/ACT",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "IMPLEMENTS/ACT"
		},
		{
			"type": "text",
			"version": 18,
			"versionNonce": 1814589623,
			"isDeleted": false,
			"id": "zEWET9me",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -441.5000000000002,
			"y": -589.9843750000002,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 81,
			"height": 25,
			"seed": 2136621399,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669625741695,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "INSERT",
			"rawText": "INSERT",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "INSERT"
		},
		{
			"type": "text",
			"version": 31,
			"versionNonce": 565280025,
			"isDeleted": false,
			"id": "TlvSBtkg",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -325.50000000000017,
			"y": -589.9843750000002,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 143,
			"height": 25,
			"seed": 423579417,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669625736999,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "DELETE_MAX",
			"rawText": "DELETE_MAX",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "DELETE_MAX"
		},
		{
			"type": "text",
			"version": 24,
			"versionNonce": 633159991,
			"isDeleted": false,
			"id": "b8goIawl",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -654.5000000000006,
			"y": -534.9843750000002,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 55,
			"height": 25,
			"seed": 1762110233,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669625761850,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Array",
			"rawText": "Array",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Array"
		},
		{
			"type": "text",
			"version": 41,
			"versionNonce": 1721965465,
			"isDeleted": false,
			"id": "D7nN1039",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -657.5000000000005,
			"y": -463.98437500000017,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 131,
			"height": 25,
			"seed": 1277481273,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669625759928,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Sorted Array",
			"rawText": "Sorted Array",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Sorted Array"
		},
		{
			"type": "text",
			"version": 21,
			"versionNonce": 2086994137,
			"isDeleted": false,
			"id": "s3WeLbVi",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -645.5000000000005,
			"y": -390.98437500000006,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 58,
			"height": 25,
			"seed": 890110551,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669627499887,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Heaps",
			"rawText": "Heaps",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Heaps"
		},
		{
			"type": "text",
			"version": 10,
			"versionNonce": 1843004663,
			"isDeleted": false,
			"id": "hRD8z8fR",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -426.5000000000002,
			"y": -529.9843750000002,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 37,
			"height": 25,
			"seed": 128731545,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669625809935,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "O(1)",
			"rawText": "O(1)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "O(1)"
		},
		{
			"type": "text",
			"version": 10,
			"versionNonce": 1310183001,
			"isDeleted": false,
			"id": "tMOeJPIi",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -273.50000000000006,
			"y": -531.9843750000002,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 41,
			"height": 25,
			"seed": 744246807,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669625817376,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "O(n)",
			"rawText": "O(n)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "O(n)"
		},
		{
			"type": "text",
			"version": 16,
			"versionNonce": 2122419095,
			"isDeleted": false,
			"id": "TacgNFkx",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -428.5000000000003,
			"y": -461.98437500000017,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 41,
			"height": 25,
			"seed": 1429412247,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669625860859,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "O(n)",
			"rawText": "O(n)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "O(n)"
		},
		{
			"type": "text",
			"version": 18,
			"versionNonce": 1969195031,
			"isDeleted": false,
			"id": "zREHpDhA",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -270.50000000000006,
			"y": -460.98437500000017,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 37,
			"height": 25,
			"seed": 1049092601,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669625863633,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "O(1)",
			"rawText": "O(1)",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "O(1)"
		},
		{
			"type": "rectangle",
			"version": 163,
			"versionNonce": 1474039639,
			"isDeleted": false,
			"id": "gufFg2B0qsj6O5abUuSnC",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -535.0000000000003,
			"y": -192.9843749999999,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 65,
			"height": 35,
			"seed": 483018393,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "OOh5PjJyobFaHwH5Zecs3",
					"type": "arrow"
				},
				{
					"id": "xfoIhVBbphv0eElnpeY-H",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "73VilfM1"
				}
			],
			"updated": 1669627504129,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 167,
			"versionNonce": 1459714489,
			"isDeleted": false,
			"id": "OcF1g4RB1C4SIHVmbxKmC",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -592.5000000000005,
			"y": -107.98437499999989,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 48,
			"height": 35,
			"seed": 1531808601,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "OOh5PjJyobFaHwH5Zecs3",
					"type": "arrow"
				},
				{
					"id": "Ldw0AoBVQkET0Hpy72j4r",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "bXsWjc0y"
				}
			],
			"updated": 1669626103600,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 163,
			"versionNonce": 1000858263,
			"isDeleted": false,
			"id": "vUVQQwn2Oxev56_7uFq_0",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -637.5000000000003,
			"y": -29.984374999999773,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 47,
			"height": 35,
			"seed": 1709545815,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "Ldw0AoBVQkET0Hpy72j4r",
					"type": "arrow"
				},
				{
					"id": "SkFrFDxziRlfzchUyZ-Wp",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "TjMO3iKk"
				},
				{
					"id": "QkNLXjEe3VFnM27ArUWxw",
					"type": "arrow"
				}
			],
			"updated": 1669626104625,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 173,
			"versionNonce": 1309562169,
			"isDeleted": false,
			"id": "kfoZdW0YaiV6YbS8upakY",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -548.0000000000003,
			"y": -29.984374999999773,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 58,
			"height": 35,
			"seed": 828702135,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "EM91C2hCqfk336PYyMEFg",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "ZGut2Paz"
				}
			],
			"updated": 1669626103600,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 163,
			"versionNonce": 1850770423,
			"isDeleted": false,
			"id": "NzVjZ3_bakg3CB4KuVYI_",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -661.0000000000005,
			"y": 53.015625000000284,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 44,
			"height": 35,
			"seed": 1482108855,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "SkFrFDxziRlfzchUyZ-Wp",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "yevAA8WF"
				}
			],
			"updated": 1669626103600,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 176,
			"versionNonce": 1586975705,
			"isDeleted": false,
			"id": "3afctEDPu3izp7zfxGdID",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -601.5000000000005,
			"y": 50.015625000000284,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 48,
			"height": 35,
			"seed": 1175277591,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "QkNLXjEe3VFnM27ArUWxw",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "9dZDp2Vv"
				}
			],
			"updated": 1669626103600,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 225,
			"versionNonce": 1216729943,
			"isDeleted": false,
			"id": "Uv1VQ9_e9xSWfLcyt_of1",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -440.5000000000003,
			"y": -107.98437499999989,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 48,
			"height": 35,
			"seed": 1780958583,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "xfoIhVBbphv0eElnpeY-H",
					"type": "arrow"
				},
				{
					"id": "tygYbGurDaiC903QxipWp",
					"type": "arrow"
				},
				{
					"id": "dwRlg7XZ8aawS1oeUGOHj",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "QsZ091JO"
				}
			],
			"updated": 1669626103600,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 220,
			"versionNonce": 961945433,
			"isDeleted": false,
			"id": "ErkNAaxCCrP91eAnk41bD",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -462.0000000000002,
			"y": -25.984374999999773,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 47,
			"height": 35,
			"seed": 159954423,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "tygYbGurDaiC903QxipWp",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "m6ToPBXN"
				}
			],
			"updated": 1669626103600,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 231,
			"versionNonce": 304213975,
			"isDeleted": false,
			"id": "JCul_jyPPQYsAIFYVlF7q",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -372.50000000000017,
			"y": -24.984374999999773,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 58,
			"height": 35,
			"seed": 956238073,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "dwRlg7XZ8aawS1oeUGOHj",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "9Yb9WJBr"
				}
			],
			"updated": 1669626103600,
			"link": null,
			"locked": false
		},
		{
			"type": "arrow",
			"version": 436,
			"versionNonce": 1257169273,
			"isDeleted": false,
			"id": "OOh5PjJyobFaHwH5Zecs3",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -544.2646466434526,
			"y": -154.52885890291253,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 18.991544200320277,
			"height": 41.544483902912646,
			"seed": 1995156535,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669626269017,
			"link": null,
			"locked": false,
			"startBinding": {
				"elementId": "gufFg2B0qsj6O5abUuSnC",
				"gap": 9.888087222777699,
				"focus": 0.7946954728108132
			},
			"endBinding": {
				"elementId": "OcF1g4RB1C4SIHVmbxKmC",
				"gap": 5,
				"focus": -0.15755627009646125
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-18.991544200320277,
					41.544483902912646
				]
			]
		},
		{
			"type": "arrow",
			"version": 442,
			"versionNonce": 1984632953,
			"isDeleted": false,
			"id": "xfoIhVBbphv0eElnpeY-H",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -467.83037125650264,
			"y": -144.36521103109519,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 37.01921539310854,
			"height": 26.3808360310953,
			"seed": 11067415,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669626269030,
			"link": null,
			"locked": false,
			"startBinding": {
				"elementId": "gufFg2B0qsj6O5abUuSnC",
				"gap": 13.790899756597582,
				"focus": 0.1577140067362642
			},
			"endBinding": {
				"elementId": "Uv1VQ9_e9xSWfLcyt_of1",
				"gap": 10,
				"focus": 0.5000000000000001
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					37.01921539310854,
					26.3808360310953
				]
			]
		},
		{
			"type": "arrow",
			"version": 435,
			"versionNonce": 1790809913,
			"isDeleted": false,
			"id": "Ldw0AoBVQkET0Hpy72j4r",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -598.9186342936964,
			"y": -69.41057013360015,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 14.516817682536498,
			"height": 25.42619513360026,
			"seed": 1413873495,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669626269019,
			"link": null,
			"locked": false,
			"startBinding": {
				"elementId": "OcF1g4RB1C4SIHVmbxKmC",
				"gap": 7.346492184662793,
				"focus": 0.5409234722149314
			},
			"endBinding": {
				"elementId": "vUVQQwn2Oxev56_7uFq_0",
				"gap": 14.000000000000114,
				"focus": -0.5201342281879252
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-14.516817682536498,
					25.42619513360026
				]
			]
		},
		{
			"type": "arrow",
			"version": 289,
			"versionNonce": 330683865,
			"isDeleted": false,
			"id": "EM91C2hCqfk336PYyMEFg",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -553.5000000000005,
			"y": -82.98437499999989,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 24.179750080102508,
			"height": 39,
			"seed": 1080031319,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669626269022,
			"link": null,
			"locked": false,
			"startBinding": null,
			"endBinding": {
				"elementId": "kfoZdW0YaiV6YbS8upakY",
				"gap": 14.000000000000114,
				"focus": 0.23110600097288966
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					24.179750080102508,
					39
				]
			]
		},
		{
			"type": "arrow",
			"version": 440,
			"versionNonce": 1645195961,
			"isDeleted": false,
			"id": "SkFrFDxziRlfzchUyZ-Wp",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -631.7346480470662,
			"y": 15.015625000000227,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 14.892747188955923,
			"height": 24.00000000000005,
			"seed": 1256557335,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669626269024,
			"link": null,
			"locked": false,
			"startBinding": {
				"elementId": "vUVQQwn2Oxev56_7uFq_0",
				"gap": 10,
				"focus": 0.01950103738918821
			},
			"endBinding": {
				"elementId": "NzVjZ3_bakg3CB4KuVYI_",
				"gap": 14,
				"focus": -0.8269844519189089
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-14.892747188955923,
					24.00000000000005
				]
			]
		},
		{
			"type": "arrow",
			"version": 295,
			"versionNonce": 115012505,
			"isDeleted": false,
			"id": "QkNLXjEe3VFnM27ArUWxw",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -601.5000000000005,
			"y": 12.015625000000231,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 23.38039532069388,
			"height": 35.00000000000006,
			"seed": 258132633,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669626269026,
			"link": null,
			"locked": false,
			"startBinding": {
				"elementId": "vUVQQwn2Oxev56_7uFq_0",
				"gap": 7,
				"focus": 0.10986800249895746
			},
			"endBinding": {
				"elementId": "3afctEDPu3izp7zfxGdID",
				"gap": 3,
				"focus": 0.3663366336633679
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					23.38039532069388,
					35.00000000000006
				]
			]
		},
		{
			"type": "arrow",
			"version": 445,
			"versionNonce": 1307303705,
			"isDeleted": false,
			"id": "tygYbGurDaiC903QxipWp",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -430.93930049612385,
			"y": -61.984374999999886,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 9.576198864633852,
			"height": 27,
			"seed": 229007545,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669626269032,
			"link": null,
			"locked": false,
			"startBinding": {
				"elementId": "Uv1VQ9_e9xSWfLcyt_of1",
				"gap": 11,
				"focus": 0.1433813320182445
			},
			"endBinding": {
				"elementId": "ErkNAaxCCrP91eAnk41bD",
				"gap": 9.000000000000114,
				"focus": -0.3842340109072933
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-9.576198864633852,
					27
				]
			]
		},
		{
			"type": "arrow",
			"version": 442,
			"versionNonce": 322081785,
			"isDeleted": false,
			"id": "dwRlg7XZ8aawS1oeUGOHj",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -400.6954807776518,
			"y": -58.984374999999886,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 32.909577844321575,
			"height": 22.000000000000057,
			"seed": 177497113,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669626269035,
			"link": null,
			"locked": false,
			"startBinding": {
				"elementId": "Uv1VQ9_e9xSWfLcyt_of1",
				"gap": 14,
				"focus": 0.6240975517948715
			},
			"endBinding": {
				"elementId": "JCul_jyPPQYsAIFYVlF7q",
				"gap": 12.000000000000057,
				"focus": 0.35961475264855414
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					32.909577844321575,
					22.000000000000057
				]
			]
		},
		{
			"type": "text",
			"version": 140,
			"versionNonce": 1981920279,
			"isDeleted": false,
			"id": "73VilfM1",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -517.0000000000003,
			"y": -187.9843749999999,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 29,
			"height": 25,
			"seed": 2145446775,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626103600,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "30",
			"rawText": "30",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "gufFg2B0qsj6O5abUuSnC",
			"originalText": "30"
		},
		{
			"type": "text",
			"version": 152,
			"versionNonce": 792123991,
			"isDeleted": false,
			"id": "bXsWjc0y",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -578.5000000000005,
			"y": -102.98437499999989,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 20,
			"height": 25,
			"seed": 485107831,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626103600,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "19",
			"rawText": "19",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "OcF1g4RB1C4SIHVmbxKmC",
			"originalText": "19"
		},
		{
			"type": "text",
			"version": 140,
			"versionNonce": 1240789401,
			"isDeleted": false,
			"id": "QsZ091JO",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -431.0000000000003,
			"y": -102.98437499999989,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 29,
			"height": 25,
			"seed": 546118681,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626103600,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "25",
			"rawText": "25",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "Uv1VQ9_e9xSWfLcyt_of1",
			"originalText": "25"
		},
		{
			"type": "text",
			"version": 141,
			"versionNonce": 2125024023,
			"isDeleted": false,
			"id": "TjMO3iKk",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -625.5000000000003,
			"y": -24.984374999999773,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 23,
			"height": 25,
			"seed": 1221978295,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626269021,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "18",
			"rawText": "18",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "vUVQQwn2Oxev56_7uFq_0",
			"originalText": "18"
		},
		{
			"type": "text",
			"version": 140,
			"versionNonce": 546797271,
			"isDeleted": false,
			"id": "ZGut2Paz",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -529.0000000000003,
			"y": -24.984374999999773,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 20,
			"height": 25,
			"seed": 467444601,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626103600,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "15",
			"rawText": "15",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "kfoZdW0YaiV6YbS8upakY",
			"originalText": "15"
		},
		{
			"type": "text",
			"version": 139,
			"versionNonce": 1071848185,
			"isDeleted": false,
			"id": "yevAA8WF",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -646.0000000000005,
			"y": 58.015625000000284,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 14,
			"height": 25,
			"seed": 408004887,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626103600,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "5",
			"rawText": "5",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "NzVjZ3_bakg3CB4KuVYI_",
			"originalText": "5"
		},
		{
			"type": "text",
			"version": 140,
			"versionNonce": 2015048247,
			"isDeleted": false,
			"id": "9dZDp2Vv",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -587.5000000000005,
			"y": 55.015625000000284,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 20,
			"height": 25,
			"seed": 2064784055,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626103600,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "16",
			"rawText": "16",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "3afctEDPu3izp7zfxGdID",
			"originalText": "16"
		},
		{
			"type": "text",
			"version": 139,
			"versionNonce": 1176559287,
			"isDeleted": false,
			"id": "m6ToPBXN",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -446.5000000000002,
			"y": -20.984374999999773,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 16,
			"height": 25,
			"seed": 753462201,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626103600,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "2",
			"rawText": "2",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "ErkNAaxCCrP91eAnk41bD",
			"originalText": "2"
		},
		{
			"type": "text",
			"version": 139,
			"versionNonce": 219602201,
			"isDeleted": false,
			"id": "9Yb9WJBr",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -351.00000000000017,
			"y": -19.984374999999773,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 15,
			"height": 25,
			"seed": 598525753,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626103600,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "4",
			"rawText": "4",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "JCul_jyPPQYsAIFYVlF7q",
			"originalText": "4"
		},
		{
			"type": "text",
			"version": 634,
			"versionNonce": 1574921687,
			"isDeleted": false,
			"id": "1HcNlUUP",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -683.5000000000003,
			"y": 221.01562500000043,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 167,
			"height": 25,
			"seed": 38510647,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626752252,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Binary Max Heap",
			"rawText": "Binary Max Heap",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Binary Max Heap"
		},
		{
			"type": "freedraw",
			"version": 550,
			"versionNonce": 343137113,
			"isDeleted": false,
			"id": "Wg3wjz_8V9Y7yDVhySZ2A",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -437.50000000000017,
			"y": 80.01562500000023,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 63.000000000000114,
			"height": 338.0000000000003,
			"seed": 435055191,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669626749705,
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
					1.2202166064981945
				],
				[
					-5,
					8.5415162454875
				],
				[
					-7,
					12.202166064982084
				],
				[
					-10,
					18.303249097473056
				],
				[
					-15,
					29.285198555956807
				],
				[
					-19,
					37.82671480144417
				],
				[
					-24.000000000000057,
					50.02888086642611
				],
				[
					-29.000000000000057,
					65.89169675090265
				],
				[
					-30.000000000000057,
					71.9927797833936
				],
				[
					-32.00000000000006,
					84.19494584837555
				],
				[
					-32.00000000000006,
					95.17689530685931
				],
				[
					-32.00000000000006,
					100.05776173285209
				],
				[
					-32.00000000000006,
					107.37906137184125
				],
				[
					-32.00000000000006,
					112.25992779783402
				],
				[
					-30.000000000000057,
					124.46209386281598
				],
				[
					-28.000000000000057,
					131.78339350180514
				],
				[
					-27.000000000000057,
					133.00361010830335
				],
				[
					-26.000000000000057,
					134.2238267148015
				],
				[
					-26.000000000000057,
					136.6642599277979
				],
				[
					-26.000000000000057,
					137.8844765342961
				],
				[
					-26.000000000000057,
					140.3249097472925
				],
				[
					-26.000000000000057,
					141.5451263537907
				],
				[
					-29.000000000000057,
					146.42599277978348
				],
				[
					-36.00000000000006,
					152.52707581227446
				],
				[
					-40.00000000000006,
					154.96750902527086
				],
				[
					-46.00000000000006,
					157.40794223826725
				],
				[
					-57.000000000000114,
					162.28880866426002
				],
				[
					-59.000000000000114,
					163.50902527075834
				],
				[
					-63.000000000000114,
					164.72924187725653
				],
				[
					-63.000000000000114,
					165.9494584837547
				],
				[
					-62.000000000000114,
					167.16967509025292
				],
				[
					-62.000000000000114,
					168.3898916967511
				],
				[
					-60.000000000000114,
					172.05054151624572
				],
				[
					-56.000000000000114,
					176.93140794223848
				],
				[
					-54,
					178.15162454873666
				],
				[
					-47.00000000000006,
					183.03249097472946
				],
				[
					-38.00000000000006,
					187.91335740072222
				],
				[
					-36.00000000000006,
					189.13357400722043
				],
				[
					-33.00000000000006,
					191.57400722021683
				],
				[
					-30.000000000000057,
					195.23465703971138
				],
				[
					-28.000000000000057,
					198.895306859206
				],
				[
					-27.000000000000057,
					202.55595667870057
				],
				[
					-24.000000000000057,
					213.53790613718434
				],
				[
					-24.000000000000057,
					215.97833935018073
				],
				[
					-23.000000000000057,
					226.96028880866447
				],
				[
					-23.000000000000057,
					230.62093862815902
				],
				[
					-23.000000000000057,
					236.72202166065
				],
				[
					-23.000000000000057,
					247.70397111913374
				],
				[
					-23.000000000000057,
					253.80505415162472
				],
				[
					-23.000000000000057,
					258.6859205776175
				],
				[
					-23.000000000000057,
					267.2274368231049
				],
				[
					-23.000000000000057,
					270.8880866425995
				],
				[
					-22,
					276.98916967509047
				],
				[
					-21,
					283.0902527075814
				],
				[
					-20,
					289.19133574007236
				],
				[
					-20,
					291.6317689530688
				],
				[
					-20,
					295.29241877256334
				],
				[
					-18,
					300.17328519855613
				],
				[
					-18,
					302.6137184115525
				],
				[
					-17,
					303.83393501805074
				],
				[
					-16,
					306.2743682310471
				],
				[
					-16,
					307.4945848375453
				],
				[
					-16,
					308.7148014440435
				],
				[
					-15,
					308.7148014440435
				],
				[
					-15,
					311.1552346570399
				],
				[
					-15,
					312.375451263538
				],
				[
					-14,
					314.8158844765345
				],
				[
					-13,
					317.2563176895308
				],
				[
					-13,
					322.1371841155237
				],
				[
					-12,
					324.5776173285202
				],
				[
					-11,
					327.0180505415165
				],
				[
					-11,
					328.23826714801476
				],
				[
					-11,
					330.6787003610111
				],
				[
					-11,
					331.8989169675093
				],
				[
					-10,
					333.1191335740075
				],
				[
					-10,
					334.33935018050573
				],
				[
					-10,
					335.559566787004
				],
				[
					-9,
					336.77978339350204
				],
				[
					-9,
					338.0000000000003
				],
				[
					-9,
					338.0000000000003
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "text",
			"version": 373,
			"versionNonce": 1351254071,
			"isDeleted": false,
			"id": "rbaTcxBH",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -406.5000000000001,
			"y": 78.01562500000037,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 228,
			"height": 75,
			"seed": 338042361,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "pSqgEBmL-8TDG4L7yr_0F",
					"type": "arrow"
				}
			],
			"updated": 1669626401242,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Structual Property: \nlevels completely filled \nleft to right",
			"rawText": "Structual Property: \nlevels completely filled \nleft to right",
			"baseline": 68,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Structual Property: \nlevels completely filled \nleft to right"
		},
		{
			"type": "text",
			"version": 270,
			"versionNonce": 577617049,
			"isDeleted": false,
			"id": "RLLBCNfz",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -420.50000000000017,
			"y": 200.01562500000043,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 211,
			"height": 75,
			"seed": 1508580825,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "1p6L4EtWRigxo3aCI4iK3",
					"type": "arrow"
				},
				{
					"id": "H09UEs8DAEqqhb8M00I4Y",
					"type": "arrow"
				}
			],
			"updated": 1669627790234,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Heap-order Property:\nparent key larger or \nequals to children",
			"rawText": "Heap-order Property:\nparent key larger or \nequals to children",
			"baseline": 68,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Heap-order Property:\nparent key larger or \nequals to children"
		},
		{
			"id": "inBWdAz9",
			"type": "text",
			"x": -661.5000000000005,
			"y": -218.98437499999983,
			"width": 97,
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
			"seed": 1384293017,
			"version": 105,
			"versionNonce": 835664151,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626757961,
			"link": null,
			"locked": false,
			"text": "Max Heap",
			"rawText": "Max Heap",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Max Heap"
		},
		{
			"id": "2VbyHzd8",
			"type": "text",
			"x": -252.50000000000006,
			"y": -19.984374999999602,
			"width": 90,
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
			"seed": 2057109145,
			"version": 32,
			"versionNonce": 1142611991,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "pSqgEBmL-8TDG4L7yr_0F",
					"type": "arrow"
				}
			],
			"updated": 1669626416056,
			"link": null,
			"locked": false,
			"text": "Balanced",
			"rawText": "Balanced",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Balanced"
		},
		{
			"id": "pSqgEBmL-8TDG4L7yr_0F",
			"type": "arrow",
			"x": -314.4173315255865,
			"y": 71.01562500000045,
			"width": 72.90737081561178,
			"height": 61.00000000000006,
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
			"seed": 1686720055,
			"version": 71,
			"versionNonce": 940885303,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626416056,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					72.90737081561178,
					-61.00000000000006
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "rbaTcxBH",
				"focus": -0.47101148471011395,
				"gap": 6.999999999999915
			},
			"endBinding": {
				"elementId": "2VbyHzd8",
				"focus": 0.21845018450184525,
				"gap": 5
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "bjXizQ7etvCHWSx_MIb25",
			"type": "ellipse",
			"x": -442.5000000000002,
			"y": 59.015625000000455,
			"width": 273.0000000000002,
			"height": 107.00000000000006,
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
			"seed": 884153975,
			"version": 146,
			"versionNonce": 433791127,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "1p6L4EtWRigxo3aCI4iK3",
					"type": "arrow"
				}
			],
			"updated": 1669627598285,
			"link": null,
			"locked": false
		},
		{
			"id": "bmVXS37b",
			"type": "text",
			"x": -424.5000000000002,
			"y": 325.0156250000008,
			"width": 218,
			"height": 150,
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
			"seed": 1588558039,
			"version": 272,
			"versionNonce": 238501753,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626926195,
			"link": null,
			"locked": false,
			"text": "Left Child of index i:\n2 * i + 1\nRight Child of Index i:\n2 * i + 2\nParent of index i:\n(i - 2)/2",
			"rawText": "Left Child of index i:\n2 * i + 1\nRight Child of Index i:\n2 * i + 2\nParent of index i:\n(i - 2)/2",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 143,
			"containerId": null,
			"originalText": "Left Child of index i:\n2 * i + 1\nRight Child of Index i:\n2 * i + 2\nParent of index i:\n(i - 2)/2"
		},
		{
			"id": "eRHAdWx63YyriOvd7u7-v",
			"type": "rectangle",
			"x": -351.99999999999994,
			"y": -207.4843749999998,
			"width": 436,
			"height": 69,
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
			"seed": 2144730679,
			"version": 177,
			"versionNonce": 1686695479,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "L7lgu6Pg"
				},
				{
					"id": "4WOsPnuBfr3VFUTUU0KIC",
					"type": "arrow"
				}
			],
			"updated": 1669627504129,
			"link": null,
			"locked": false
		},
		{
			"id": "ruyHoCPfc-FwHzR_XrPnw",
			"type": "line",
			"x": -329.5000000000001,
			"y": -191.98437499999983,
			"width": 2,
			"height": 49.00000000000006,
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
			"seed": 284645625,
			"version": 12,
			"versionNonce": 1234321591,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626769205,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					2,
					49.00000000000006
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "SOhVY9OUzBVdXOAaCw4YS",
			"type": "line",
			"x": -288.5000000000001,
			"y": -198.98437499999983,
			"width": 0,
			"height": 53,
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
			"seed": 3705401,
			"version": 12,
			"versionNonce": 1268648823,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626771246,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					53
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "k_OdvoVVxLJa3mo8ZWMwe",
			"type": "line",
			"x": -237.50000000000006,
			"y": -202.98437499999983,
			"width": 0,
			"height": 51,
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
			"seed": 1303628665,
			"version": 16,
			"versionNonce": 869393527,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626773592,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					51
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "fqXwiStk_qHlntbIGOgXq",
			"type": "line",
			"x": -195.5,
			"y": -196.98437499999983,
			"width": 2,
			"height": 51,
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
			"seed": 369492601,
			"version": 8,
			"versionNonce": 80507127,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626775273,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					2,
					51
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "UefMJf3D9fcqZ0xBd6x2G",
			"type": "line",
			"x": -140.5,
			"y": -199.98437499999983,
			"width": 2,
			"height": 74.00000000000006,
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
			"seed": 1361608185,
			"version": 11,
			"versionNonce": 1138598489,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626777140,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					2,
					74.00000000000006
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "Sw5J4dWKiEquvyGn-hwGG",
			"type": "line",
			"x": -90.49999999999989,
			"y": -207.9843749999999,
			"width": 10,
			"height": 73.00000000000011,
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
			"seed": 738659255,
			"version": 13,
			"versionNonce": 1354667639,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626779312,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					10,
					73.00000000000011
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "Q61NLvb-yxGVKQz8efvaK",
			"type": "line",
			"x": -31.499999999999886,
			"y": -204.98437499999983,
			"width": 1,
			"height": 72.00000000000006,
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
			"seed": 284520569,
			"version": 10,
			"versionNonce": 1256107031,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626781056,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					1,
					72.00000000000006
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "OdlmRV1ML4kzgYPggW-av",
			"type": "line",
			"x": 20.500000000000227,
			"y": -207.9843749999999,
			"width": 9,
			"height": 64.00000000000006,
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
			"seed": 1489175769,
			"version": 15,
			"versionNonce": 1732784185,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626822498,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					4.5,
					32.00000000000003
				],
				[
					9,
					64.00000000000006
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "whtkzVAX",
			"type": "text",
			"x": -354.50000000000017,
			"y": -178.98437499999983,
			"width": 29,
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
			"seed": 1382949239,
			"version": 4,
			"versionNonce": 1598619225,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626793424,
			"link": null,
			"locked": false,
			"text": "30",
			"rawText": "30",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "30"
		},
		{
			"id": "t2h083RO",
			"type": "text",
			"x": -304.5000000000001,
			"y": -187.98437499999983,
			"width": 20,
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
			"seed": 1293810713,
			"version": 4,
			"versionNonce": 1987532567,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626797615,
			"link": null,
			"locked": false,
			"text": "19",
			"rawText": "19",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "19"
		},
		{
			"id": "mGTZWJEA",
			"type": "text",
			"x": -264.50000000000006,
			"y": -188.98437499999983,
			"width": 29,
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
			"seed": 1740760761,
			"version": 4,
			"versionNonce": 2024536695,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626802445,
			"link": null,
			"locked": false,
			"text": "25",
			"rawText": "25",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "25"
		},
		{
			"id": "W4GHbrxy",
			"type": "text",
			"x": -218.50000000000006,
			"y": -183.98437499999983,
			"width": 23,
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
			"seed": 185750873,
			"version": 4,
			"versionNonce": 186046935,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626805885,
			"link": null,
			"locked": false,
			"text": "18",
			"rawText": "18",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "18"
		},
		{
			"id": "tZXR5eCu",
			"type": "text",
			"x": -172.5,
			"y": -180.98437499999983,
			"width": 20,
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
			"seed": 1118685177,
			"version": 4,
			"versionNonce": 1678865719,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626809102,
			"link": null,
			"locked": false,
			"text": "15",
			"rawText": "15",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "15"
		},
		{
			"id": "JYTumYQZ",
			"type": "text",
			"x": -70.5,
			"y": -188.98437499999983,
			"width": 16,
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
			"seed": 1623375513,
			"version": 25,
			"versionNonce": 1069094647,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626872272,
			"link": null,
			"locked": false,
			"text": "2",
			"rawText": "2",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "2"
		},
		{
			"id": "L7lgu6Pg",
			"type": "text",
			"x": -141.49999999999994,
			"y": -185.4843749999998,
			"width": 15,
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
			"seed": 526229943,
			"version": 87,
			"versionNonce": 500044793,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626882543,
			"link": null,
			"locked": false,
			"text": "4",
			"rawText": "4",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "eRHAdWx63YyriOvd7u7-v",
			"originalText": "4"
		},
		{
			"type": "text",
			"version": 34,
			"versionNonce": 1619469367,
			"isDeleted": false,
			"id": "CGoj9p06",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -17.499999999999886,
			"y": -186.48437499999983,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 14,
			"height": 25,
			"seed": 1072804185,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626857694,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "5",
			"rawText": "5",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "5"
		},
		{
			"type": "text",
			"version": 24,
			"versionNonce": 1321600121,
			"isDeleted": false,
			"id": "7fzd0WWl",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 39.50000000000023,
			"y": -188.48437499999983,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 20,
			"height": 25,
			"seed": 104292185,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669626861551,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "16",
			"rawText": "16",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "16"
		},
		{
			"id": "4WOsPnuBfr3VFUTUU0KIC",
			"type": "arrow",
			"x": -361.5000000000001,
			"y": -210.98437499999983,
			"width": 124.00000000000011,
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
			"seed": 1248182423,
			"version": 177,
			"versionNonce": 929920185,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669627504129,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-124.00000000000011,
					-2
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "eRHAdWx63YyriOvd7u7-v",
				"focus": 0.9030547305897333,
				"gap": 9.50000000000017
			},
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "ER8thw9y",
			"type": "text",
			"x": -184.5,
			"y": -129.98437499999977,
			"width": 55,
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
			"seed": 1630896343,
			"version": 9,
			"versionNonce": 214883671,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669626906351,
			"link": null,
			"locked": false,
			"text": "Array",
			"rawText": "Array",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "Array"
		},
		{
			"id": "yqDbhPMS",
			"type": "text",
			"x": -115.16666666666663,
			"y": 541.0156250000007,
			"width": 254,
			"height": 192,
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
			"seed": 1538408281,
			"version": 780,
			"versionNonce": 1237182457,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1669628129301,
			"link": null,
			"locked": false,
			"text": "function fix_down(A, n, i) {\n    while i is not a leaf\n        j = left_child(i)\n        if A[j] < A[j+1]\n            j += 1\n        swap(A[i], A[j])\n        i = j\n}",
			"rawText": "function fix_down(A, n, i) {\n    while i is not a leaf\n        j = left_child(i)\n        if A[j] < A[j+1]\n            j += 1\n        swap(A[i], A[j])\n        i = j\n}",
			"fontSize": 19.001248439450695,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 185,
			"containerId": null,
			"originalText": "function fix_down(A, n, i) {\n    while i is not a leaf\n        j = left_child(i)\n        if A[j] < A[j+1]\n            j += 1\n        swap(A[i], A[j])\n        i = j\n}"
		},
		{
			"id": "qLwqkuw1bgnNff6qqropr",
			"type": "ellipse",
			"x": -464.5000000000003,
			"y": 177.01562500000068,
			"width": 280.0000000000002,
			"height": 118,
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
			"seed": 1363917751,
			"version": 78,
			"versionNonce": 141392377,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "ViK_SxpHrDzLFlBmGCsx-",
					"type": "arrow"
				}
			],
			"updated": 1669628124184,
			"link": null,
			"locked": false
		},
		{
			"id": "0e6iS6uw",
			"type": "text",
			"x": -435.5000000000003,
			"y": -389.9843749999999,
			"width": 67,
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
			"seed": 1839814295,
			"version": 25,
			"versionNonce": 788033847,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669627455082,
			"link": null,
			"locked": false,
			"text": "O(logn)",
			"rawText": "O(logn)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "O(logn)"
		},
		{
			"id": "rW3JeHqo",
			"type": "text",
			"x": -283.5000000000001,
			"y": -390.984375,
			"width": 67,
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
			"seed": 1397587577,
			"version": 28,
			"versionNonce": 82648407,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669627488909,
			"link": null,
			"locked": false,
			"text": "O(logn)",
			"rawText": "O(logn)",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "O(logn)"
		},
		{
			"id": "3wDwPGiD",
			"type": "text",
			"x": -114.70000000000044,
			"y": 194.00113224637747,
			"width": 451,
			"height": 115,
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
			"seed": 94629177,
			"version": 401,
			"versionNonce": 448396823,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1669628124185,
			"link": null,
			"locked": false,
			"text": "function fix_up(A, i) {\n    while parent(i) exists and A[parent(i)] < A[i]\n        swap(A[i], A[parent(i))\n        i = parent(i)\n}",
			"rawText": "function fix_up(A, i) {\n    while parent(i) exists and A[parent(i)] < A[i]\n        swap(A[i], A[parent(i))\n        i = parent(i)\n}",
			"fontSize": 18.60568974771874,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 108,
			"containerId": null,
			"originalText": "function fix_up(A, i) {\n    while parent(i) exists and A[parent(i)] < A[i]\n        swap(A[i], A[parent(i))\n        i = parent(i)\n}"
		},
		{
			"id": "tXI03ePA",
			"type": "text",
			"x": -122.2666666666671,
			"y": 34.39713541666708,
			"width": 361,
			"height": 125,
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
			"seed": 112747641,
			"version": 146,
			"versionNonce": 110936441,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "H09UEs8DAEqqhb8M00I4Y",
					"type": "arrow"
				}
			],
			"updated": 1669627790234,
			"link": null,
			"locked": false,
			"text": "function insert(maxheap, x) {\n    maxheap.n += 1\n    maxheap.arr[haxheap.n-1] = x\n    fix_up(maxheap.arr, maxheap.n-1)\n}",
			"rawText": "function insert(maxheap, x) {\n    maxheap.n += 1\n    maxheap.arr[haxheap.n-1] = x\n    fix_up(maxheap.arr, maxheap.n-1)\n}",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 118,
			"containerId": null,
			"originalText": "function insert(maxheap, x) {\n    maxheap.n += 1\n    maxheap.arr[haxheap.n-1] = x\n    fix_up(maxheap.arr, maxheap.n-1)\n}"
		},
		{
			"id": "H09UEs8DAEqqhb8M00I4Y",
			"type": "arrow",
			"x": -202.2666666666671,
			"y": 223.39713541666708,
			"width": 68,
			"height": 141.33333333333337,
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
			"seed": 1116240985,
			"version": 29,
			"versionNonce": 1372370295,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669627790234,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					68,
					-141.33333333333337
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "RLLBCNfz",
				"focus": 0.8575228410204502,
				"gap": 7.233333333333064
			},
			"endBinding": {
				"elementId": "tXI03ePA",
				"focus": 0.9480746399050202,
				"gap": 12
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "nUsB8uSC",
			"type": "text",
			"x": -119.60000000000036,
			"y": 370.3971354166671,
			"width": 515,
			"height": 150,
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
			"seed": 264259639,
			"version": 203,
			"versionNonce": 411354967,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "ViK_SxpHrDzLFlBmGCsx-",
					"type": "arrow"
				}
			],
			"updated": 1669628117171,
			"link": null,
			"locked": false,
			"text": "function delete_max(maxheap) {\n    swap(maxheap.arr[0], maxheap.arr[maxheap.n-1])\n    maxheap.n -= 1\n    fix_down(maxheap.arr, maxheap.n, 0)\n    return arr[maxheap.n]\n}",
			"rawText": "function delete_max(maxheap) {\n    swap(maxheap.arr[0], maxheap.arr[maxheap.n-1])\n    maxheap.n -= 1\n    fix_down(maxheap.arr, maxheap.n, 0)\n    return arr[maxheap.n]\n}",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 143,
			"containerId": null,
			"originalText": "function delete_max(maxheap) {\n    swap(maxheap.arr[0], maxheap.arr[maxheap.n-1])\n    maxheap.n -= 1\n    fix_down(maxheap.arr, maxheap.n, 0)\n    return arr[maxheap.n]\n}"
		},
		{
			"id": "ViK_SxpHrDzLFlBmGCsx-",
			"type": "arrow",
			"x": -207.60000000000036,
			"y": 296.73046875000034,
			"width": 78.66666666666663,
			"height": 78.66666666666674,
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
			"seed": 935438359,
			"version": 19,
			"versionNonce": 1929723065,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1669628117171,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					78.66666666666663,
					78.66666666666674
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "qLwqkuw1bgnNff6qqropr",
				"focus": -0.3698233109876871,
				"gap": 24.707378695953764
			},
			"endBinding": {
				"elementId": "nUsB8uSC",
				"focus": -0.5919799498746874,
				"gap": 9.333333333333371
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "1p6L4EtWRigxo3aCI4iK3",
			"type": "arrow",
			"x": -185.62745732854785,
			"y": 261.6593844192679,
			"width": 51.46079066188108,
			"height": 48.57092135752109,
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
			"seed": 1919065209,
			"version": 514,
			"versionNonce": 1110291703,
			"isDeleted": true,
			"boundElements": null,
			"updated": 1669628124184,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					51.46079066188108,
					-48.57092135752109
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "qLwqkuw1bgnNff6qqropr",
				"focus": 1.082991340169544,
				"gap": 8.714458930488789
			},
			"endBinding": {
				"elementId": "3wDwPGiD",
				"focus": 0.9973594098006371,
				"gap": 19.4666666666663
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "QMyvZ2HSDCLTUV5eUousx",
			"type": "arrow",
			"x": -214.50000000000006,
			"y": 251.01562500000068,
			"width": 100.10234484533079,
			"height": 314.66666666666674,
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
			"seed": 1048730521,
			"version": 358,
			"versionNonce": 1763713847,
			"isDeleted": true,
			"boundElements": null,
			"updated": 1669628126217,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					100.10234484533079,
					314.66666666666674
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": {
				"elementId": "yqDbhPMS",
				"focus": -0.41979709350150846,
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