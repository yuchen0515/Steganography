# 圖片隱寫術

## 一、基本資料

* 姓名：Yu-Chen Lin
* 系級：資工111
* 學號：40771131H

---

## 二、檔案有哪些？

|File Name|Description|
|:--|:--|
|README.md|原理&使用方式說明|
|img_hide.c|Main program|
|output.bmp|test case|
|test_out.bmp|test case 2|

---

## 三、How to run?

* tutorial

```bash
$ make
$ ./img_hide [option] [cover_bmp] [secret_data]
-w, --write: Write the secret data to the cover_bmp
-e, --extract: Extract the secret data from the cover_bmp to the secret_data
-b, --bits=N: use last N bits. N is from 1 to 8. The default N is 1.
```

* example

```bash
$ ./img_hide -w -b 3 [cover_bmp] [secret_data]
```

* notice

1. -b表示選取之位元數，預設為1，若需設置必須在[1,8]範圍內
2. cover_bmp若不夠存secret_data會跳warning

---

## 四、Introducion

> 圖片隱寫術 能將 任意檔案、訊息、圖檔、影片 藏在另一張圖片中，便於隱藏私密檔案而不為人知 <br><br>
> 一般來說檔案為binary file寫成，單位為1byte，無論其為exe, .c等等，都可將其以1byte為單位取出，將該資訊分別存進RGB三色末端，以達到隱藏資訊卻不至於影響圖片過多，而外人看來圖片只是一張單純的圖而已

### 原理
* 以一個正規bmp檔案而論，分為RGB三色，各色佔 1 byte，從 8 bits 倒數位元替換較不會影響到原先的數值，因此隱藏順序由最小位元(LSB)至最大位元(MSB)

	![](https://i.imgur.com/8hxcfa2.png)

	> 如圖所示，若 b 輸入為 2，則會像 secret data 塞進R, G, B最後兩個位元，每次 **secret data 以 1 byte 為單位**，如為上述 100110，則存入 00100110

* 將隱藏檔案大小等資訊儲存於bmp的headfile => reserve中，可儲存 4 bytes的長度資訊



---

## 五、執行狀況

* cover_bmp => output.bmp\
![](https://i.imgur.com/W8LZMxH.png)

* secret_data => test_out.bmp\
![](https://i.imgur.com/lMS709H.png)

---

### 測試一： 1 bit

#### 存入

* 輸入

```bash
$ ./img_hide -w output.bmp test_out.bmp

# output
Done!
```

* result
	> 將test_out.bmp => output.bmp後 1 bits內\
	> 因只藏 1 bit，基本上看不出差異
	
	![](https://i.imgur.com/ExQ9vlf.png)

#### 提取	

* 輸入

```bash
$ ./img_hide -e output.bmp test_out.bmp

# output
Done!
```

* result
	> 從 output.bmp，直接取出原始圖檔 test_out.bmp
	
	![](https://i.imgur.com/QVEvGNH.png)
	
---

### 測試二： 7 bit

#### 存入

* 輸入

```bash
$ ./img_hide -w -b 7 output.bmp test_out.bmp

# output
Done!
```

* result
	> 將檔案存進output.bmp(彩色渲染圖)各色後 7 bits\
	> 因每色 8 bits 被替換掉後 7 bits，雖主要色調不變，但後 7 bits幾乎影響顏色一半的數值，因此可看到圖下方隱藏檔案部份的細微顏色就被吃掉了
	
	![](https://i.imgur.com/vv3sDpq.png)


#### 提取

* 輸入

```bash
$ ./img_hide -e -b 7 output.bmp test_out.bmp

# output
Done!
```

* result

	![](https://i.imgur.com/oqiZGOX.png)


---

### 測試三： 5 bit


* secret data => test3.c

	![](https://i.imgur.com/lICZm3B.png)

#### 存入

* 輸入

```bash
$ ./img_hide -w -b 5 output.bmp test3.c

# output
Done!
```

* result
	> 主要因為程式檔較小，存入5 bits不算太大，只影響到左下方一點點像素，藍色稍稍變紫

	![](https://i.imgur.com/l7HbQkj.png)


#### 提取

* 輸入

```bash
$ ./img_hide -e -b 5 output.bmp test3.c

# output
Done!
```

* result

	![](https://i.imgur.com/8HxBdUf.png)

