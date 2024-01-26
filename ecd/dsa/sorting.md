---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
Sorting Algorithms

 ^Xoddr2ar

Selection sort ^x96E2PbS

Merge sort ^ZpM9gDnU

Quick sort ^ujleNJU4

Heap sort ^icKfGHIx

Pros ^vll5T14T

Cons ^TxO3AufA

Easy to understand ^L7DEtYOK

O(n^2) Time ^FRj03cQm

Average O(nlogn) Time
O(1) Space ^xG8EUWJU

Worst case O(n^2) Time
Not Stable ^FN82Yes5

Worst case O(nlogn) Time
Stable ^hxBYhCKl

O(n) Space ^8jeth7Wb

Worst case O(nlogn) Time
O(1) Space ^Y5UBM8Mb

Not Stable ^lIiPJuUX

function selection_sort(arr, len) {
    for i=0; i < len-1; i++
        min = i
        for j = i+1; j < len; j++
            if arr[i] < arr[j]
                min = j
        temp = arr[i]
        arr[i] = arr[min]
        arr[min] = temp
}

function quick_sort(arr, len) {
    quick_sort_range(arr, 0, len-1)
}

function quick_sort_range(arr, first, last) {
    if last <= first
        return
    pivot = arr[first]
    pos = last
    for i=last; i>first; --i
        if arr[i] > pivot
            swap(arr[pos], arr[i])
            --pos
    swap(arr[first], arr[pos])
    quick_sort_range(arr, first, pos-1)
    quick_sort_range(arr, pos+1, last
}

function mergeSort(arr) {
    if size of arr > 1
        mid = (length of arr)/2
        L = arr[:mid]
        R = arr[mid:]
        mergeSort(L)
        mergeSort(R)
        head_L, head_R, i = 0
        while head_L<length of L and head_R<length of R
            if L[head_L]<R[head_R]
                arr[i]=L[head_L]
                head_L += 1
            else 
                arr[i] = R[head_R]
                head_R += 1
            i += 1
        while head_L < length of L
            arr[i] = L[head_L]
            head_L += 1
            i += 1
        while head_R < length of R
            arr[i] = L[head_R]
            head_R += 1
            i += 1
}

function Heapify(A) {
    n = A.size()
    for(i=parent(last(n)); i>=0; --i)
        fix_down(A, n, i)
}

function HeapSort(A, n) {
    Heapify(A)
    while n > 1
        swap(A[root()], A[last(n)])
        n -= 1
        fix_down(A, n, root())
} ^aikdwaXD

divide ^vr2eRAss

sort ^8Y2ePQ6R

compare & overwrite ^1AFd9muE

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
			"version": 227,
			"versionNonce": 992403383,
			"isDeleted": false,
			"id": "Xoddr2ar",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -431,
			"y": -391.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 175,
			"height": 75,
			"seed": 291306937,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628570042,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Sorting Algorithms\n\n",
			"rawText": "Sorting Algorithms\n\n",
			"baseline": 68,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Sorting Algorithms\n\n"
		},
		{
			"type": "rectangle",
			"version": 108,
			"versionNonce": 1335700793,
			"isDeleted": false,
			"id": "X_1wfdyIT_nwOSgZ05zWF",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -428,
			"y": -347.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 727,
			"height": 469,
			"seed": 822396695,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628935996,
			"link": null,
			"locked": false
		},
		{
			"type": "line",
			"version": 57,
			"versionNonce": 1561582361,
			"isDeleted": false,
			"id": "lhXdF1t74usSRuzpneF3k",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -423,
			"y": -266.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 660,
			"height": 4,
			"seed": 79342233,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669628742246,
			"link": null,
			"locked": false,
			"startBinding": null,
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": null,
			"points": [
				[
					0,
					0
				],
				[
					660,
					4
				]
			]
		},
		{
			"type": "line",
			"version": 75,
			"versionNonce": 2143135641,
			"isDeleted": false,
			"id": "FK81gdyVHtR2CPLGlH2PS",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -420,
			"y": -174.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 653,
			"height": 11,
			"seed": 318619383,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669628773204,
			"link": null,
			"locked": false,
			"startBinding": null,
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": null,
			"points": [
				[
					0,
					0
				],
				[
					653,
					11
				]
			]
		},
		{
			"type": "line",
			"version": 113,
			"versionNonce": 943398457,
			"isDeleted": false,
			"id": "bHt7ri-yMCZNAXQ1pmCML",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -416,
			"y": -81.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 647,
			"height": 16,
			"seed": 775273847,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669628775832,
			"link": null,
			"locked": false,
			"startBinding": null,
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": null,
			"points": [
				[
					0,
					0
				],
				[
					647,
					16
				]
			]
		},
		{
			"type": "line",
			"version": 75,
			"versionNonce": 1140628343,
			"isDeleted": false,
			"id": "tqFcPr3kewcVAnhsykYMo",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -412,
			"y": 17.7578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 639,
			"height": 12,
			"seed": 1396162007,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669628779939,
			"link": null,
			"locked": false,
			"startBinding": null,
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": null,
			"points": [
				[
					0,
					0
				],
				[
					639,
					12
				]
			]
		},
		{
			"type": "text",
			"version": 48,
			"versionNonce": 1595455641,
			"isDeleted": false,
			"id": "x96E2PbS",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -399,
			"y": -228.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 139,
			"height": 25,
			"seed": 2129138839,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628827238,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Selection sort",
			"rawText": "Selection sort",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Selection sort"
		},
		{
			"type": "text",
			"version": 96,
			"versionNonce": 1673876087,
			"isDeleted": false,
			"id": "ZpM9gDnU",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -400.5,
			"y": -46.7421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 110,
			"height": 25,
			"seed": 1278634327,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628829597,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Merge sort",
			"rawText": "Merge sort",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Merge sort"
		},
		{
			"type": "text",
			"version": 95,
			"versionNonce": 1030194679,
			"isDeleted": false,
			"id": "ujleNJU4",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -403.5,
			"y": -140.7421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 103,
			"height": 25,
			"seed": 130071449,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628828545,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Quick sort",
			"rawText": "Quick sort",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Quick sort"
		},
		{
			"type": "text",
			"version": 110,
			"versionNonce": 421654521,
			"isDeleted": false,
			"id": "icKfGHIx",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -401.5,
			"y": 53.2578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 99,
			"height": 25,
			"seed": 1584211575,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628831051,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Heap sort",
			"rawText": "Heap sort",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Heap sort"
		},
		{
			"type": "text",
			"version": 24,
			"versionNonce": 1585554777,
			"isDeleted": false,
			"id": "vll5T14T",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -142,
			"y": -317.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 46,
			"height": 25,
			"seed": 765961977,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628943599,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Pros",
			"rawText": "Pros",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Pros"
		},
		{
			"type": "text",
			"version": 6,
			"versionNonce": 339735481,
			"isDeleted": false,
			"id": "TxO3AufA",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 102,
			"y": -319.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 46,
			"height": 25,
			"seed": 1929416951,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628849964,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Cons",
			"rawText": "Cons",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Cons"
		},
		{
			"type": "text",
			"version": 70,
			"versionNonce": 1928472407,
			"isDeleted": false,
			"id": "L7DEtYOK",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -209,
			"y": -231.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 199,
			"height": 25,
			"seed": 1801131161,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628989556,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Easy to understand",
			"rawText": "Easy to understand",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Easy to understand"
		},
		{
			"type": "text",
			"version": 38,
			"versionNonce": 195310937,
			"isDeleted": false,
			"id": "FRj03cQm",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 94,
			"y": -231.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 120,
			"height": 25,
			"seed": 2018331191,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628878107,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "O(n^2) Time",
			"rawText": "O(n^2) Time",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "O(n^2) Time"
		},
		{
			"type": "text",
			"version": 88,
			"versionNonce": 888988087,
			"isDeleted": false,
			"id": "xG8EUWJU",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -216,
			"y": -148.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 217,
			"height": 50,
			"seed": 470164025,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669629079222,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Average O(nlogn) Time\nO(1) Space",
			"rawText": "Average O(nlogn) Time\nO(1) Space",
			"baseline": 43,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Average O(nlogn) Time\nO(1) Space"
		},
		{
			"type": "text",
			"version": 95,
			"versionNonce": 1147443159,
			"isDeleted": false,
			"id": "FN82Yes5",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 55,
			"y": -145.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 241,
			"height": 50,
			"seed": 761157495,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628938693,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Worst case O(n^2) Time\nNot Stable",
			"rawText": "Worst case O(n^2) Time\nNot Stable",
			"baseline": 43,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Worst case O(n^2) Time\nNot Stable"
		},
		{
			"type": "text",
			"version": 87,
			"versionNonce": 407985431,
			"isDeleted": false,
			"id": "hxBYhCKl",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -221,
			"y": -54.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 252,
			"height": 50,
			"seed": 2144909495,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628969470,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Worst case O(nlogn) Time\nStable",
			"rawText": "Worst case O(nlogn) Time\nStable",
			"baseline": 43,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Worst case O(nlogn) Time\nStable"
		},
		{
			"type": "text",
			"version": 39,
			"versionNonce": 1162894681,
			"isDeleted": false,
			"id": "8jeth7Wb",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 79,
			"y": -42.2421875,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 107,
			"height": 25,
			"seed": 1439687641,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669628983654,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "O(n) Space",
			"rawText": "O(n) Space",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "O(n) Space"
		},
		{
			"type": "text",
			"version": 87,
			"versionNonce": 2057210681,
			"isDeleted": false,
			"id": "Y5UBM8Mb",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -231,
			"y": 44.7578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 252,
			"height": 50,
			"seed": 170384793,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669629016668,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Worst case O(nlogn) Time\nO(1) Space",
			"rawText": "Worst case O(nlogn) Time\nO(1) Space",
			"baseline": 43,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Worst case O(nlogn) Time\nO(1) Space"
		},
		{
			"type": "text",
			"version": 24,
			"versionNonce": 1864998329,
			"isDeleted": false,
			"id": "lIiPJuUX",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 86,
			"y": 53.7578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 110,
			"height": 25,
			"seed": 1825415383,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669629026205,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Not Stable",
			"rawText": "Not Stable",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Not Stable"
		},
		{
			"type": "text",
			"version": 2537,
			"versionNonce": 1280798645,
			"isDeleted": false,
			"id": "aikdwaXD",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -417.6470588235294,
			"y": 179.22840073529414,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 672,
			"height": 1972,
			"seed": 913500697,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669686510691,
			"link": null,
			"locked": false,
			"fontSize": 23.085714285714293,
			"fontFamily": 1,
			"text": "function selection_sort(arr, len) {\n    for i=0; i < len-1; i++\n        min = i\n        for j = i+1; j < len; j++\n            if arr[i] < arr[j]\n                min = j\n        temp = arr[i]\n        arr[i] = arr[min]\n        arr[min] = temp\n}\n\nfunction quick_sort(arr, len) {\n    quick_sort_range(arr, 0, len-1)\n}\n\nfunction quick_sort_range(arr, first, last) {\n    if last <= first\n        return\n    pivot = arr[first]\n    pos = last\n    for i=last; i>first; --i\n        if arr[i] > pivot\n            swap(arr[pos], arr[i])\n            --pos\n    swap(arr[first], arr[pos])\n    quick_sort_range(arr, first, pos-1)\n    quick_sort_range(arr, pos+1, last\n}\n\nfunction mergeSort(arr) {\n    if size of arr > 1\n        mid = (length of arr)/2\n        L = arr[:mid]\n        R = arr[mid:]\n        mergeSort(L)\n        mergeSort(R)\n        head_L, head_R, i = 0\n        while head_L<length of L and head_R<length of R\n            if L[head_L]<R[head_R]\n                arr[i]=L[head_L]\n                head_L += 1\n            else \n                arr[i] = R[head_R]\n                head_R += 1\n            i += 1\n        while head_L < length of L\n            arr[i] = L[head_L]\n            head_L += 1\n            i += 1\n        while head_R < length of R\n            arr[i] = L[head_R]\n            head_R += 1\n            i += 1\n}\n\nfunction Heapify(A) {\n    n = A.size()\n    for(i=parent(last(n)); i>=0; --i)\n        fix_down(A, n, i)\n}\n\nfunction HeapSort(A, n) {\n    Heapify(A)\n    while n > 1\n        swap(A[root()], A[last(n)])\n        n -= 1\n        fix_down(A, n, root())\n}",
			"rawText": "function selection_sort(arr, len) {\n    for i=0; i < len-1; i++\n        min = i\n        for j = i+1; j < len; j++\n            if arr[i] < arr[j]\n                min = j\n        temp = arr[i]\n        arr[i] = arr[min]\n        arr[min] = temp\n}\n\nfunction quick_sort(arr, len) {\n    quick_sort_range(arr, 0, len-1)\n}\n\nfunction quick_sort_range(arr, first, last) {\n    if last <= first\n        return\n    pivot = arr[first]\n    pos = last\n    for i=last; i>first; --i\n        if arr[i] > pivot\n            swap(arr[pos], arr[i])\n            --pos\n    swap(arr[first], arr[pos])\n    quick_sort_range(arr, first, pos-1)\n    quick_sort_range(arr, pos+1, last\n}\n\nfunction mergeSort(arr) {\n    if size of arr > 1\n        mid = (length of arr)/2\n        L = arr[:mid]\n        R = arr[mid:]\n        mergeSort(L)\n        mergeSort(R)\n        head_L, head_R, i = 0\n        while head_L<length of L and head_R<length of R\n            if L[head_L]<R[head_R]\n                arr[i]=L[head_L]\n                head_L += 1\n            else \n                arr[i] = R[head_R]\n                head_R += 1\n            i += 1\n        while head_L < length of L\n            arr[i] = L[head_L]\n            head_L += 1\n            i += 1\n        while head_R < length of R\n            arr[i] = L[head_R]\n            head_R += 1\n            i += 1\n}\n\nfunction Heapify(A) {\n    n = A.size()\n    for(i=parent(last(n)); i>=0; --i)\n        fix_down(A, n, i)\n}\n\nfunction HeapSort(A, n) {\n    Heapify(A)\n    while n > 1\n        swap(A[root()], A[last(n)])\n        n -= 1\n        fix_down(A, n, root())\n}",
			"baseline": 1963,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "function selection_sort(arr, len) {\n    for i=0; i < len-1; i++\n        min = i\n        for j = i+1; j < len; j++\n            if arr[i] < arr[j]\n                min = j\n        temp = arr[i]\n        arr[i] = arr[min]\n        arr[min] = temp\n}\n\nfunction quick_sort(arr, len) {\n    quick_sort_range(arr, 0, len-1)\n}\n\nfunction quick_sort_range(arr, first, last) {\n    if last <= first\n        return\n    pivot = arr[first]\n    pos = last\n    for i=last; i>first; --i\n        if arr[i] > pivot\n            swap(arr[pos], arr[i])\n            --pos\n    swap(arr[first], arr[pos])\n    quick_sort_range(arr, first, pos-1)\n    quick_sort_range(arr, pos+1, last\n}\n\nfunction mergeSort(arr) {\n    if size of arr > 1\n        mid = (length of arr)/2\n        L = arr[:mid]\n        R = arr[mid:]\n        mergeSort(L)\n        mergeSort(R)\n        head_L, head_R, i = 0\n        while head_L<length of L and head_R<length of R\n            if L[head_L]<R[head_R]\n                arr[i]=L[head_L]\n                head_L += 1\n            else \n                arr[i] = R[head_R]\n                head_R += 1\n            i += 1\n        while head_L < length of L\n            arr[i] = L[head_L]\n            head_L += 1\n            i += 1\n        while head_R < length of R\n            arr[i] = L[head_R]\n            head_R += 1\n            i += 1\n}\n\nfunction Heapify(A) {\n    n = A.size()\n    for(i=parent(last(n)); i>=0; --i)\n        fix_down(A, n, i)\n}\n\nfunction HeapSort(A, n) {\n    Heapify(A)\n    while n > 1\n        swap(A[root()], A[last(n)])\n        n -= 1\n        fix_down(A, n, root())\n}"
		},
		{
			"type": "freedraw",
			"version": 42,
			"versionNonce": 1469709497,
			"isDeleted": false,
			"id": "GuRf8_7f6DMkcMi0Ar94V",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -53,
			"y": 1080.7578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 201,
			"height": 58,
			"seed": 1176750007,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669631194000,
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
					9,
					0
				],
				[
					15,
					0
				],
				[
					31,
					1
				],
				[
					54,
					3
				],
				[
					66,
					4
				],
				[
					78,
					4
				],
				[
					95,
					5
				],
				[
					101,
					5
				],
				[
					105,
					6
				],
				[
					110,
					7
				],
				[
					111,
					8
				],
				[
					110,
					8
				],
				[
					100,
					10
				],
				[
					97,
					11
				],
				[
					87,
					13
				],
				[
					62,
					17
				],
				[
					50,
					19
				],
				[
					18,
					26
				],
				[
					-9,
					33
				],
				[
					-21,
					36
				],
				[
					-41,
					42
				],
				[
					-56,
					46
				],
				[
					-65,
					49
				],
				[
					-70,
					50
				],
				[
					-76,
					53
				],
				[
					-78,
					53
				],
				[
					-80,
					54
				],
				[
					-82,
					55
				],
				[
					-83,
					55
				],
				[
					-84,
					56
				],
				[
					-85,
					56
				],
				[
					-86,
					56
				],
				[
					-87,
					57
				],
				[
					-88,
					57
				],
				[
					-89,
					57
				],
				[
					-89,
					58
				],
				[
					-90,
					58
				],
				[
					-90,
					58
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 29,
			"versionNonce": 1586336535,
			"isDeleted": false,
			"id": "HUEONA1oiFbksiFr1Bd7F",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -169,
			"y": 1165.7578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 78,
			"height": 30,
			"seed": 77334359,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669631200150,
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
					9,
					0
				],
				[
					14,
					1
				],
				[
					22,
					2
				],
				[
					25,
					3
				],
				[
					35,
					4
				],
				[
					46,
					6
				],
				[
					50,
					6
				],
				[
					57,
					7
				],
				[
					63,
					7
				],
				[
					66,
					8
				],
				[
					70,
					9
				],
				[
					74,
					9
				],
				[
					75,
					9
				],
				[
					74,
					10
				],
				[
					71,
					11
				],
				[
					67,
					12
				],
				[
					59,
					14
				],
				[
					55,
					15
				],
				[
					38,
					19
				],
				[
					21,
					23
				],
				[
					14,
					25
				],
				[
					2,
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
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 101,
			"versionNonce": 1710798233,
			"isDeleted": false,
			"id": "3eqLuawy0RMW59xBdNlZp",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 260,
			"y": 1269.7578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 462,
			"height": 191,
			"seed": 1226444249,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1669631209645,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					3,
					2
				],
				[
					10,
					6
				],
				[
					17,
					8
				],
				[
					21,
					10
				],
				[
					33,
					14
				],
				[
					42,
					17
				],
				[
					45,
					18
				],
				[
					49,
					19
				],
				[
					49,
					20
				],
				[
					50,
					20
				],
				[
					50,
					21
				],
				[
					51,
					21
				],
				[
					53,
					23
				],
				[
					55,
					25
				],
				[
					56,
					25
				],
				[
					56,
					26
				],
				[
					57,
					26
				],
				[
					57,
					27
				],
				[
					51,
					29
				],
				[
					46,
					31
				],
				[
					41,
					31
				],
				[
					30,
					34
				],
				[
					15,
					38
				],
				[
					-4,
					44
				],
				[
					-15,
					47
				],
				[
					-37,
					54
				],
				[
					-48,
					58
				],
				[
					-68,
					66
				],
				[
					-75,
					69
				],
				[
					-93,
					76
				],
				[
					-111,
					85
				],
				[
					-118,
					88
				],
				[
					-142,
					97
				],
				[
					-150,
					100
				],
				[
					-161,
					106
				],
				[
					-168,
					108
				],
				[
					-180,
					113
				],
				[
					-187,
					116
				],
				[
					-200,
					121
				],
				[
					-211,
					126
				],
				[
					-216,
					129
				],
				[
					-227,
					132
				],
				[
					-231,
					135
				],
				[
					-235,
					136
				],
				[
					-237,
					137
				],
				[
					-241,
					138
				],
				[
					-241,
					139
				],
				[
					-244,
					139
				],
				[
					-246,
					141
				],
				[
					-249,
					142
				],
				[
					-251,
					142
				],
				[
					-254,
					143
				],
				[
					-255,
					144
				],
				[
					-258,
					146
				],
				[
					-261,
					146
				],
				[
					-263,
					147
				],
				[
					-269,
					149
				],
				[
					-270,
					150
				],
				[
					-275,
					151
				],
				[
					-278,
					152
				],
				[
					-282,
					154
				],
				[
					-283,
					154
				],
				[
					-288,
					156
				],
				[
					-292,
					157
				],
				[
					-296,
					158
				],
				[
					-298,
					159
				],
				[
					-304,
					161
				],
				[
					-311,
					163
				],
				[
					-314,
					163
				],
				[
					-320,
					165
				],
				[
					-327,
					167
				],
				[
					-331,
					168
				],
				[
					-338,
					170
				],
				[
					-349,
					172
				],
				[
					-356,
					175
				],
				[
					-360,
					176
				],
				[
					-362,
					177
				],
				[
					-365,
					178
				],
				[
					-368,
					179
				],
				[
					-369,
					180
				],
				[
					-371,
					180
				],
				[
					-373,
					180
				],
				[
					-375,
					182
				],
				[
					-378,
					182
				],
				[
					-380,
					184
				],
				[
					-383,
					184
				],
				[
					-385,
					185
				],
				[
					-388,
					186
				],
				[
					-389,
					186
				],
				[
					-393,
					188
				],
				[
					-395,
					188
				],
				[
					-398,
					189
				],
				[
					-400,
					189
				],
				[
					-401,
					190
				],
				[
					-402,
					190
				],
				[
					-403,
					190
				],
				[
					-404,
					191
				],
				[
					-405,
					191
				],
				[
					-405,
					191
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "text",
			"version": 12,
			"versionNonce": 502525657,
			"isDeleted": false,
			"id": "vr2eRAss",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 63,
			"y": 1073.7578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 55,
			"height": 25,
			"seed": 457821303,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669631215177,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "divide",
			"rawText": "divide",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "divide"
		},
		{
			"type": "text",
			"version": 91,
			"versionNonce": 1493465111,
			"isDeleted": false,
			"id": "8Y2ePQ6R",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -65,
			"y": 1158.7578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 44,
			"height": 25,
			"seed": 1786366039,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669631237892,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "sort",
			"rawText": "sort",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "sort"
		},
		{
			"type": "text",
			"version": 25,
			"versionNonce": 256189111,
			"isDeleted": false,
			"id": "1AFd9muE",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 336,
			"y": 1292.7578125,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 201,
			"height": 25,
			"seed": 738942263,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1669631249835,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "compare & overwrite",
			"rawText": "compare & overwrite",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "compare & overwrite"
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