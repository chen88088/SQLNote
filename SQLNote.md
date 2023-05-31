# SQL筆記
## 01. 請在192.168.10.180的StockDB底下建立一張資料表
|  CTIME   | MTIME | RecordID |   日期   |   股票代號   | 股票名稱 |高盤價|最高價|最低價|收盤價|漲跌|PK|非叢集所引|CTIME預設值|
|:--------:|:-----:|:--------:|:--------:|:------------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
| datatime |  int  |  bigint  | nvarchar | nvarchar(10) |  nvarchar(20)  |decimal(9, 2)|decimal(9, 2)|decimal(9, 2)|decimal(9, 2)|decimal(9, 2)|日期(DESC)、股票代號(ASC)|MTIME(DESC)|getdate()|

```sql=use StockDB
create table 日收盤_新人訓練_陳鵬升
(
	CTIME  datetime DEFAULT getdate(),
	MTIME  int,
	RecordID  bigint,
	日期 nvarchar(8) ,
	股票代號  nvarchar(10),
	股票名稱  nvarchar(20),
	開盤價  decimal(9, 2),
	最高價  decimal(9, 2),
	最低價  decimal(9, 2),
	收盤價  decimal(9, 2),
	漲跌    decimal(9, 2),
	primary key(日期 DESC, 股票代號 ASC),
);
CREATE NONCLUSTERED INDEX MTIME_Index ON 日收盤_新人訓練_陳鵬升 (MTIME DESC) ;
```

## 02. 以下常用的欄位型態分別是存什麼樣的資料？他的儲存大小為何？

數值:
| tinyint | int | bigint | real | float | decimal | numeric |
|:-------:|:---:|:------:|:----:|:-----:|:-------:|:-------:|
|    1    |  4  |   8    |  4   |   8   |    n    |    n    |


* real 的 ISO 同義字是 float(24) 
  float [ (n) ]
  n 值	Precision	儲存體大小
1-24	7 位數	4 個位元組
25-53	15 位數	8 個位元組

* float	- 1.79E+308 到 -2.23E-308、0 及 2.23E-308 到 1.79E+308	這會隨著 n 的值而不同
* real	- 3.40E + 38 到 -1.18E - 38、0 及 1.18E - 38 到 3.40E + 38	4 個位元組
* Float Data type is Double Precision 64-bit format. It uses 8 bytes of Storage
* Real Data Type is Single Precision 32-bit format. It uses 4 bytes of Storage


* decimal 和 numeric 的差異在於，numeric(M, N) 型別所儲存的數字總數可以大於 M，而 decimal 必須依照實際定義的有效數 M 儲存 (不得超過)，在 SQL Server 的世界裡，則是將 decimal 和 numeric 皆實作為 「依照實際定義的有效數 M 儲存」，因此以規範來看，在 MSSQL 使用 decimal 是比較合乎規範的。

- 資料類型	範圍	
bigint	 |  -2^63  到 2^63-1 8 位元組 [8Byte * 8(bit/Byte) ]
int	     | -2^31  到 2^31-1 	4 位元組
smallint | -2^15  到 2^15-1 	2 位元組
tinyint	 | 0      至 255	    1 位元組

字串:
| char(n) | nchar(n) | varchar | text |
|:-------:|:-----:|:-------:|:----:|
|  n      |   0   |    0    |  0   |



* ntext
最大字串長度為 2^30 - 1 (1,073,741,823) 位元組的可變長度 Unicode 資料。 儲存體大小 (以位元組為單位) 是輸入字串長度的兩倍。 ntext 的 ISO 同義字為 national text。

* 文字
在伺服器字碼頁中、最大字串長度為 2^31-1 (2,147,483,647) 的可變長度非 Unicode 資料。 當伺服器字碼頁使用雙位元組字元時，儲存體大小仍是 2,147,483,647 個位元組。 儲存體大小有可能少於 2,147,483,647 個位元組，這會隨著字元字串而不同。

* image
0 到 2^31-1 (2,147,483,647) 位元組的可變長度二進位資料。

日期:
| datetime |
|:--------:|
|    8     |






## 03. char、nchar、varchar、nvarchar有什麼差別？

char varchar nchar nvarchar

仔細觀察以上4種型態從字面上的差別只在有無var以及有無n，

接下來將要來介紹 char varchar nchar nvarchar 的差別，幫助選擇自己適合的資料型態。

所以var跟n是什麼呢？

*** var***

定長與變長
定長(沒有var):文字的長度固定，當輸入的數據長度沒有達到指定的長度時將自動以英文空格在其後面填充，讓長度達到相對應的長度。

變長(有var):表示是實際存儲空間是變長的，也就是說當輸入的數據長度沒有達到指定的長度時不會在後面填充空格。(不過text所存儲的也是可變長的)。

舉個例子來說，現在資料表有兩個欄位:

rocket1 char(10)
rocket2 varchar(10)
存入相同的資料「HiIU」

rocket1 會自動在「HiIU」後面補空白，存滿10 Bytes。
rocket2 會根據資料長度變更儲存空間，「HiIU」共四個英文字符，所以則存了4 Bytes。

Unicode或非Unicode
在資料庫中，每個英文字符的儲存空間只需要一個 1 Byte，但若是非英文字及符號，如中文、泰文、日文、韓文等非英文字符，則需要 2 Byte來儲存。
如果英文與中文同時存在，由於占用空間數不同，容易造成混亂，導致讀取出來的字符串是亂碼。所以就要使用Unicode字符集(N)。

*** N ***

Unicode(有n):所有的文字都用2 Byte來儲存，即使是英文字也是使用2 Byte來儲存，就可以解決中英文字符集不兼容的問題。

非Unicode(沒有n):文字是英文字符就是1 Byte;非英文字符就是使用2 Byte來儲存。

舉例來說，一樣有兩個欄位:
rocket1 varchar(50)
rocket2 nvarchar(50)
存入相同的資料「我愛TsungHua」

rocket1: 我愛 = 22= 4 Byte ; TsungHua =81= 8 Byte ，所以總共4+8=12 Byte的儲存空間。

rocket2:「我愛TsungHua」共10個字，所以儲存空間為10*2=20 Byte。

雖然rocket2較佔儲存空間，但若儲存的資料不限於英文，建議都以n開頭的欄位型態為主，避免亂碼。

***
有var的:在存不固定長度的資料時，較省空間。
有n的:支援Unicode，不過儲存的資料會大兩倍(全部都英文字的話啦)。

然後建議開資料表欄位時，要以儲存的資料為考量，
建議:

char 存資料有固定長度，並且都為英文數字。
nchar 存資料有固定長度，但不確定是否都是英文數字。
varchar 存資料沒有固定長度，並且都為英文數字。
nvarchar 存資料沒有固定長度，且不確定是否皆為英文數字。
***

char(n) 定長，n 必須是一個介於 1 和 8000 之間的數值，最多8000個英文字，4000個非英文字，儲存大小為 n 個位元組。

nchar(n) 定長，n 的值必須介於 1 與 4000 之間。儲存大小為 n 位元組的兩倍。

varchar(n) 變長，n 必須是一個介於 1 和 8000 之間的數值，可存儲4000個字，無論英文還是非英文，儲存大小為輸入資料的位元組的實際長度。

nvarchar(n) 變長，n 的值必須介於 1 與 4000 之間，位元組的儲存大小是所輸入字元個數的兩倍。

text(n) 變長，非Unicode數據，最大長度為2^31-1(2,147,483,647)個字符。

ntext(n) 變長，最大字串長度為 2^30 - 1 (1,073,741,823) 位元組的可變長度 Unicode 資料。 儲存體大小 (以位元組為單位) 是輸入字串長度的兩倍。



## 04. decimal(9,2)代表什麼跟decimal(19,4)差別？ 為何不用real、float？什麼是浮點數誤差？
* decimal(9,2)--> 有效位數=9, 小數位數2，存五位元
* decimal(19,4)--> 有效位數=19, 小數位數4,存九位元
* 最大儲存體大小會隨著有效位數而不同。
* ![](https://i.imgur.com/0OZsnEv.png)



* float 和 real 僅為近似值，而非精準值，在資料呈現上不會全然正確。

* Float & Real Data Types in SQL Server uses the floating-point number format. Real is a Single Precision Floating Point number, while Float is a Double Precision Floating Point number. The Floating point numbers can store very large or very small numbers than decimal numbers. But this trade-off comes at the cost of precision.****
* 什麼是浮點數誤差？
位元有限但小數無限 , 對表達次方位元數的取捨造成對精確值位數的位數數量改變造成精確值改變

## 05. 什麼是PrimaryKey(PK or 主鍵)? 什麼是ForeignKey(FK or 外來鍵)？
* Foreign Key 外鍵/外部鍵 : 關聯中被用來參考到其他表格主鍵的關聯鍵，就是外鍵。
* Super key 超鍵 : 符合唯一性的關聯鍵。
* Candidate Key 候選鍵 : 符合唯一性以及最小性的關聯鍵。
*Primary Key 主鍵 : 從候選鍵中，挑選出其中一個關聯鍵，也就是最具識別意義的關聯鍵。
*Alternate Key 次要鍵 : 沒有被選為主鍵的其他候選鍵。
* 例如學生資料表(student_id, student_no, student_name, student_depid)
student_id表示學生身分證字號
student_no表示學生學號
student_name表示學生姓名
student_depid表示學生的科系代號

Super key 就可以是 {student_id}、{student_no}、 {student_id, student_no}、{student_id, student_name}、{student_id, student_depid} ... 等等，都符合唯一性的條件。

Candidate Key 就可以是 {student_id}、{student_no}，都符合唯一性及最小性的條件。

Primary Key 就可以從Candidate Key挑選一個，至於挑選哪一個，就看你的系統特性。如果你的學校是多學制的話，就可能不適合挑選學生身分證字號當主鍵，因為可能某個學生原本是國中部，畢業後再進入高中部，如果系統沒有考慮清楚，這個畢業後再變新生的個體，就可能出錯。

Alternate Key 就是沒被挑中當成Primary Key的其他Candidate Key，例如，如果挑選 {student_id}當成主鍵，Alternate Key 就是{student_no}。

如果存在科系資料表 (depid, dep_name)，而且depid當成科系資料表的主鍵，學生資料表的 student_depid就是Foreign Key。


## 06. 為什麼要設定PK或FK？

1. [PK]作為能夠識別資料且具唯一性的標記，建立PK同時建立索引，加快查詢
3. [FK]可以做表跟表之間的reference 以節省儲存空間/方便資料修改/減少輸入錯誤

## 07. 叢集索引是什麼？非叢集索引是什麼？
* 資料庫索引（Database Index）
  從資料庫找符合某特定條件的資料，資料庫的標準程序是一行一行檢查，如果是，就把資料拿出來，如果不是，就繼續檢查直到結束。
  當資料量一大，資料庫要找到資料的時間就會越來越長。「有沒有方法能夠讓資料庫不用一行一行爬也可以找到想要的資料？」有的！就是索引（Index）。索引其實是一種資料結構（data structure），我們看不到，因為它通常存在我們使用的資料庫管理系統（DBSM, Database Management System）裡面。

* 叢集 Clustered
Clustering alters the data block into a certain distinct order to match the index, resulting in the row data being stored in order.

  叢集索引（Clustered Index）直接修改原本的資料表順序，將資料按照要索引的欄位排。如此可以讓資料搜尋非常有效率，尤其是依照順序（accessed sequentially）或是一個範圍連續資料的時候。想當然爾，一張資料表只會有一個叢集索引，通常就是主鍵欄位（primary key column）。

  因為叢集索引會調整資料順序，所以它最大的特色就是實體的資料（physical data rows）跟索引表（index block/index table）中的順序會是一樣的。

* 非叢集 Non-Clustered
非叢集索引（Non-Clustered Index）是指不管資料在原本資料表中的排序，而在索引表中自己依照索引值建立排列。與叢集索引的差別是，叢集索引的排列順序就是實際上資料的排列順序，而非叢集索引的排列順序不會/無法影響到實際資料的排列順序。也因此，一個資料表中可以包含很多非叢集索引。

  通常會使用非叢集索引的會是在 JOIN, WHERE 或是 ORDER BY 使用的非主鍵欄位（non-primary key columns）。
  
 * 叢集索引（Clustered Index）：

1. 當資料表設定了「叢集索引」，則資料表「實體資料列」的順序會依據叢集索引的值做排序

2. 每一資料表只能有一個「叢集索引」

3. SQL Server  資料表的主索鍵（PK），預設為「叢集索引」且是唯一的（Unique）

4. B-Tree結構

* 非叢集索引（NonClustered Index）:

1. 資料列未根據非叢集索引進行排序與儲存

2. 每一資料表能有249個「非叢集索引」

3. B-Tree結構


## 08. 建立索引能帶來什麼好處？壞處？
* 好處-- 加速資料檢索效率
* 壞處-- 在資料表中加入索引的缺點是它們會影響寫入的效能。此外，不正確建立的索引甚至會對 SELECT 査詢產生不利影響！由於索引過多、不正確或缺失而影響效能的任何資料表配置都被視為效果不佳的索引。
* 效果不佳的索引的影響
效果不佳的索引可能是於一欄上建立的索引無法提供更簡單的資料作業，也可能是於多欄上建立的索引不但不能加快査詢速度，反而會降低査詢速度。


## 09. 條件約束有哪些？其中Primary Key與UNIQUE有何差別？
Primary Key: 該欄位之值必須非空且唯一
FOREIGN KEY: 該欄位之值必須來自REFERENCE的資料欄位

單一張資料表的約束條件，關鍵字有以下三種。

1.CHECK 檢查資料值是否符合邏輯判斷式,輸入值必須在定義之範圍內
```sql=
ALTER TABLE dbo.DocExc   
   ADD ColumnD int NULL   
   CONSTRAINT CHK_ColumnD_DocExc   
   CHECK (ColumnD > 10 AND ColumnD < 50);  
```
2.UNIQUE 檢查資料表中該欄位的欄位值是否為獨一無二的值。可有NULL但也只可以一個
```sql=
CREATE TABLE Production.TransactionHistoryArchive2  
(  
   TransactionID int NOT NULL,  
   CONSTRAINT AK_TransactionID UNIQUE(TransactionID)  
);  
```
3.NOT NULL 欄位資料值不能為空值NULL
* UNIQUE 條件約束和 CHECK 條件約束是兩種類型的條件約束，可用來在 SQL Server資料表中強制執行資料完整性。 這些都是重要的資料庫物件。
* 條件約束是SQL Server Database Engine 為您強制執行的規則。 例如，您可以使用 UNIQUE 條件約束，確定在未參與主索引鍵的特定資料行中沒有重複值

* 雖然 UNIQUE 條件約束和 PRIMARY KEY 條件約束兩者都強制唯一性，但是當您想要強制非主索引鍵之資料行 (或資料行組合) 的唯一性時，請使用 UNIQUE 條件約束而不要使用 PRIMARY KEY 條件約束。

  UNIQUE 條件約束允許 NULL 值，這與 PRIMARY KEY 條件約束不同。 但是，就像參與 UNIQUE 條件約束的任何值，一個資料行只能有一個 Null 值。 UNIQUE 條件約束也可被 FOREIGN KEY 條件約束所參考。
  
* CHECK 條件約束限制一個或多個資料行接受的值，藉此強制值域完整性。 您可使用能根據邏輯運算子傳回 TRUE 或 FALSE 的任何邏輯 (布林) 運算式來建立 CHECK 條件約束。 例如，可以藉由建立只允許範圍從 $15,000 到 $100,000 之資料的 CHECK 條件約束，以限制 salary 資料行的值範圍。 這可防止輸入的薪水超過一般的薪水範圍。 邏輯運算式可以是： salary >= 15000 AND salary <= 100000。

  可以將多個 CHECK 條件約束套用到單一資料行。 您也可以在資料表層級建立單一 CHECK 條件約束，將它套用到多個資料行。 例如，多重資料行的 CHECK 條件約束可用來確認 country_region 資料行值為 USA 的任何資料列，其 state 資料行內也會有兩個字元的值。 這允許可在某一個位置上檢查多個條件。

  CHECK 條件約束類似於 FOREIGN KEY 條件約束，用來控制放入資料行的值。 其間的差異在於它們如何判定哪些值有效：FOREIGN KEY 條件約束從另一個資料表取得有效值清單，而 CHECK 條件約束則會從邏輯運算式來判定有效值。


## 10. VIEW是什麼？
* View 是藉由 SQL SELECT 查詢動態組合生成的資料表 (亦即 View 是由查詢得到的結果集組合而成的資料表)。View 內的資料紀錄是由其它實際存在的資料表中產生的，它就像是一個虛擬資料表，實際上資料庫 (或說是硬碟) 裡面是不存在這一個資料表的 (只存在此 View 的相關定義)，但是我們使用上卻有如實際存在的資料表 - 所有的 SQL 查詢語法都可以操作在此 View 上。

![](https://i.imgur.com/QMl6Tja.png)

```sql= 
CREATE VIEW view_p_sum (Product, P_SUM) AS
SELECT Product, Price*Quantity FROM p_orders GROUP BY Product;
```
```sql=
SELECT * FROM view_p_sum
```
![](https://i.imgur.com/KleKqNt.png)

資料表是一種實體結構 (physical structure)，而 View 是一種虛擬結構 (virtual structure)。


## 11. 什麼情況下會使用VIEW？
* view的使用時機，是在於將多量變少量的情況下。並可利用其view索引應用。
但如果資料表欄位常變動就不適合用view。

  其實view你也可以將其視為另外一張表，它的好處就是你可以指定統計好的sql在裏面。
這樣程式碼的部份就只要呼叫這個view就好。不需要理會它是怎麼跑sql。
這是view的優點。但是，建立起來的view認真來說還是需要一段sql運做。
當view所處理的資料表有變動的情況下就會運做。

  也就是說，建立view表的缺點，就是在新增或更新時。效能會比較差。

  最後是我個人的見解，使用view表的時機最好是在多表結合下的語法使用。
且需確定欄位不會再變動的情況下，才去使用。

## 12. 請使用語法新增一筆資料到第一題所建立的表中

其餘為NULL。
|   日期   | 股票代號 | 股票名稱 | 開盤價 |
|:--------:|:--------:|:--------:|:------:|
| 20181218 |   0000   |   測試   |   0    |

```sql=
use StockDB;

insert into 日收盤_新人訓練_陳鵬升 (日期, 股票代號, 股票名稱, 開盤價) 
			    values(N'20181218', N'0000', N'測試' ,0)


```
![](https://i.imgur.com/JkESLhb.png)




## 13. 請使用語法校正上一題新增的資料
更新:
| 開盤價 |
|:------:|
|   1    |
```sql=
USE StockDB;
UPDATE 日收盤_新人訓練_陳鵬升
SET    開盤價 = 1
WHERE  日期 = N'20181218' AND 股票代號= N'0000';

```
![](https://i.imgur.com/M49KfpJ.png)


## 14. 請使用語法刪除上一題修改的資料
```sql=
USE StockDB;
DELETE 日收盤_新人訓練_陳鵬升
WHERE  日期 = N'20181218' AND 股票代號= N'0000';
```
![](https://i.imgur.com/hkpkFoN.png)


## 15. 請使用語法新增資料到第一題所建立的表中
資料新增需求如下：
[StockDB].[dbo].[日收盤]裡20181214的所有資料
```sql=
USE StockDB;

ALTER TABLE 日收盤_新人訓練_陳鵬升 
ADD  參考價        decimal(9, 2),
	 上市櫃        nvarchar(2),
	 產業代號      nvarchar(2),
	 漲停價        decimal(9, 2),
	 跌停價        decimal(9, 2),
	 漲跌狀況      nvarchar(2),
	 最後委買價    decimal(9, 2),
	 最後委賣價    decimal(9, 2),
	 [漲幅(%)]     decimal(9, 2),
	 [振幅(%)]     decimal(9, 2),
	 成交量        bigint,
	 [成交量(股)]  bigint, 
	 成交筆數      bigint, 
	 [成交金額(千)] bigint,
	 [成交值比重(%)] decimal(9, 2),
	 [成交量變動(%)]  decimal(9, 2),
	 [股本(百萬)2]   decimal(19, 2),
	 [均張變動(%)]   decimal(9, 2),
	 均張 decimal(9, 1),
	 [總市值(億)] decimal(9, 1),
	 [市值比重(%)] decimal(9,3),
	 類股本益比 decimal(9, 1),
	 類股股價淨值比 decimal(9, 2),
	 類股股價淨值比2 decimal(9, 2),
	 本益比 decimal(9, 1),
	 [本益比(近四季)] decimal(9, 1),
	 [股東權益(百萬)] decimal(19, 2),
	 股價淨值比 decimal(9, 2),
	 股價淨值比2 decimal(9, 2),
	 [舊股本(百萬)] real,
	 委買張數 int,
	 委賣張數 int,
	 次日參考價 decimal(9,2),
	 [成交金額_元] bigint,
	 均價 decimal(9, 2),
	 [股本(百萬)] decimal(19,2),
	 週轉率 decimal(9, 2),
	 漲跌停  smallint;

```
```sql=
USE StockDB;
INSERT 日收盤_新人訓練_陳鵬升
SELECT 日期, 股票代號, 股票名稱, 開盤價
FROM dbo.日收盤
WHERE 日期 = N'20181214';
```
![](https://i.imgur.com/5n0hcsg.png)



## 16. 請使用兩種不同語法一次刪除第一題所建立的資料表內的所有資料
```sql=
USE StockDB;
DELETE FROM 日收盤_新人訓練_陳鵬升;
```
OR

```sql=
USE StockDB;
TRUNCATE TABLE  日收盤_新人訓練_陳鵬升;
```

## 17. 請使用語法刪除第一題建出來的table

```sql=
DROP TABLE "日收盤_新人訓練_陳鵬升"
```

## 18. 以下語法有何差異?

---

* DELETE FROM 資料表名:

---
 
使用SQL Delete語法，不加任何Where條件下，是可以移除整個Table的資料，這個方式簡單易懂，但是卻會耗用大量資源，因為Delete語法：


  1.Delete屬DML命令，不加任何Where條件下，會一筆一筆的移除資料列，且在交易記錄中每筆刪除的資  料，都會記錄成一個項目，當整個Table的資料移除完時，已耗用許多時間。
  不會刪除表結構、索引、約束條件
  DELETE 屬於 DML 會被記錄到交易日誌中，執行後不會馬上把資料清掉，而是標記為刪除，可以回復資料

  2.若Table存在刪除的觸發程序(Trigger)時，將會引發觸發程序。

  3.如果該Table包含識別欄位，使用Delete陳述式刪除資料表中所有資料時，並不會讓識別編號重置，所以後續再新增的資料，仍會從之前最後一個編號之後繼續編號下去
  


---

* TRUNCATE TABLE 資料表名: 

---

  使用SQL Truncate Table會移除資料表中的所有資料列，但會保留資料表結構及其欄位、條件約束、索引等。

  如果該Table包含識別欄位，這個識別編號會重設為原本定義的初始值。如果未定義任何初始值，就會使用預設值 1。

  * Tranc Table 與Delete 比較的優點為：

    1.使用的交易記錄空間較少：Truncate Table會取消配置用來儲存資料表資料的資料頁，以移除資料，所以交易記錄只會記錄頁面的取消配置。

    2.通常會使用較少鎖定：Delete會鎖定每一筆資料，以便執行刪除，但Truncate Table只鎖定資料表和頁面，不會鎖定每個資料列。

    3.速度較快：綜合以上兩點，Truncate Table會比Delete快！


---

* DROP TABLE 資料表名: 


---

使用SQL Drop Tablec會移除一或多個資料表定義及這些資料表的所有資料、索引、觸發程序、條件約束和權限規格，也就是完全刪除一個資料表。

  當卸除資料表時，資料表的規則或預設值會失去它們的繫結，資料表的任何相關條件約束或觸發程序也都會自動卸除。如果重新建立資料表，您必須重新繫結適當的規則和預設值、重新建立任何觸發程序，以及加入所有必要的條件約束。

所以使用Drop Table後，若你要重新使用被刪除的資料表，就只好重新Create Table了，但是要注意，Drop Table 和 Create Table 不得在相同批次的相同資料表上執行。否則，系統可能會發生非預期的錯誤。


***
***

## 19~41 找到以下資料
> 以下題目(19-41)請用.180的[StockDB].[dbo].[日收盤]實作
### 19.
* 找出日期為 20181206 的所有資料

```sql=
SELECT *
FROM DBO.日收盤
WHERE 日期 = N'20181206';
```
![](https://i.imgur.com/lHzLPWY.png)

### 20.
* 股票代號為0050、006201、00680L、00721B
* 日期在20181201到20181206的所有資料

```sql=
USE StockDB

SELECT *
FROM DBO.日收盤
WHERE 股票代號 IN (N'0050', N'006201', N'00680L' ,N'00721B')
AND 日期  BETWEEN N'20181201' AND N'20181206'
```
![](https://i.imgur.com/XxfWVS1.png)


### 21. 
* 所有20181201到20181206中不相同的股票代號

```sql=
USE StockDB

SELECT DISTINCT 股票代號 
FROM DBO.日收盤
WHERE 日期  BETWEEN N'20181201' AND N'20181206'
```
![](https://i.imgur.com/y0n2psU.png)


### 22.
* 2018年每個日期的資料筆數

```sql=
USE StockDB

SELECT 日期, 
COUNT(*) as number 
FROM 日收盤 
WHERE 日期  BETWEEN N'20180101' AND N'20181231'  
GROUP BY 日期 
```
![](https://i.imgur.com/CjUHmpS.png)

### 23. 
* 每檔股票在 20181201 到 20181206 的開盤價總和、最小最高價、最大最低價、平均收盤價

```sql=
USE StockDB

SELECT 股票代號, 
sum(開盤價) as 開盤價總和, 
max(最高價) as 最小最高價, 
max(最低價) as 最大最低價, 
avg(收盤價) as 平均收盤價
FROM 日收盤 
WHERE 日期  BETWEEN N'20181201' AND N'20181206'
GROUP BY 股票代號 ;
```
![](https://i.imgur.com/hIyIA1o.png)

### 24. 
* 所有在 20181201 到 20181206 所有不相同的股票名稱，並顯示它們的前兩個字 (假設有abc、abd兩檔，最後會顯示兩個ab)

```sql=
USE StockDB

SELECT 股票名稱,SUBSTRING(股票名稱, 1, 2) AS Initial 
FROM DBO.日收盤
WHERE 日期 BETWEEN N'20181201' AND N'20181206'
GROUP BY 股票名稱
```
![](https://i.imgur.com/RNYgZny.png)

### 25. 
* 所有在 20181201 到 20181206 的不相同股票名稱且頭尾要去空白
```sql=
USE StockDB

SELECT 股票名稱, LTRIM( RTRIM(股票名稱)) as TrimTitle
FROM DBO.日收盤
WHERE 日期 BETWEEN N'20181201' AND N'20181206'
GROUP BY 股票名稱
```
![](https://i.imgur.com/RERJ9J3.png)

### 26.
* 20181201 到 20181206 的所有股票
* 先依照股票代號由大到小排序
* 再依照日期由小到大排序

```sql=
SELECT *  
FROM DBO.日收盤
WHERE 日期 BETWEEN N'20181201' AND N'20181206'
ORDER BY  股票代號 DESC,日期 ASC
```
### 27. 
* 20181201 到 20181206 所有平均收盤價低於80的股票代號
```sql=
SELECT   股票名稱, AVG(收盤價) AS 平均收盤價
FROM dbo.日收盤
WHERE 日期 BETWEEN N'20181201' AND N'20181206' 
GROUP BY 股票名稱
HAVING  AVG(收盤價) < 80
```
![](https://i.imgur.com/KlSNGYs.png)

### 28. 
* 2018年總資料筆數

```sql=
USE StockDB

SELECT 
COUNT(*) as 總共有幾筆資料
FROM 日收盤 
WHERE 日期  BETWEEN N'20180101' AND N'20181231'  
```
![](https://i.imgur.com/zr5W8uc.png)

### 29. 
* 2018年忽略收盤價為null的總資料筆數
* 不能用where 收盤價 IS NOT NULL

```sql=
SELECT  COUNT(收盤價) as number
FROM dbo.日收盤
WHERE (日期 BETWEEN N'20180101' AND N'20181231') 
```
![](https://i.imgur.com/3WgTpox.png)
* https://www.fooish.com/sql/count-function.html
* 关于count函数的理解
  * https://learnku.com/articles/64244



### 30. 
* 2018年股票代號長度大於4的所有不相同股票代號
```sql=
SELECT DISTINCT 股票代號
FROM dbo.日收盤
WHERE (日期 BETWEEN N'20180101' AND N'20181231') 
AND (LEN(股票代號) > 4)
```
![](https://i.imgur.com/jnI8fQQ.png)

### 31. 

* 2018年包含"指數"且不相同的股票名稱
* 在顯示股票名稱時把"指數"換成"__"

```sql=
SELECT 股票名稱, Replace (股票名稱,'指數', '__')
FROM dbo.日收盤
WHERE (日期 BETWEEN N'20180101' AND N'20181231') 
AND (股票名稱 LIKE '%指數%')
GROUP BY 股票名稱
```
![](https://i.imgur.com/akibPJw.png)

### 32. 
* "今天"的所有資料，不能把日期寫死(可能沒有)

```sql=
SELECT *
FROM dbo.日收盤
WHERE 日期 = CONVERT (date, GETDATE())
```
![](https://i.imgur.com/yNhGHTw.png)

### 33. 
* 20181201到20181206的所有資料
**[上市櫃]欄位的規則：[上市櫃] = 1時顯示上市[上市櫃]=2時顯示上櫃


```sql=
USE StockDB;

SELECT CTIME, 
     MTIME, 
     RecordID,
     日期, 
     股票代號, 
     股票名稱, 
     參考價, 
     開盤價, 
     最高價, 
     最低價, 
     收盤價, 
     漲跌, 
     CASE [上市櫃]
        WHEN 1 THEN '上市'
        WHEN 2 THEN '上櫃'
        
     END AS [上市櫃], 
     產業代號,
     漲停價,
     跌停價,
	 漲跌狀況,
	 最後委買價,
	 最後委賣價,
	 [漲幅(%)],
	 [振幅(%)],
	 成交量,
	 [成交量(股)], 
	 成交筆數, 
	 [成交金額(千)],
	 [成交值比重(%)],
	 [成交量變動(%)],
	 [股本(百萬)2],
	 [均張變動(%)],
	 均張,
	 [總市值(億)],
	 [市值比重(%)],
	 類股本益比,
	 類股股價淨值比,
	 類股股價淨值比2,
	 本益比,
	 [本益比(近四季)],
	 [股東權益(百萬)],
	 股價淨值比,
	 股價淨值比2,
	 [舊股本(百萬)],
	 委買張數,
	 委賣張數,
	 次日參考價,
	 [成交金額_元],
	 均價,
	 [股本(百萬)] ,
	 週轉率,
	 漲跌停

FROM StockDB.dbo.日收盤
WHERE [日期] BETWEEN N'20181201' AND N'20181206'
```
![](https://i.imgur.com/ZddWLjF.png)

### 34. 
* 20181206 有但 20181205 沒有的股票代號
```sql=
SELECT 股票代號
FROM dbo.日收盤
WHERE 日期 = N'20181206'
AND 股票代號 NOT IN (SELECT 股票代號
                      FROM dbo.日收盤
                      WHERE 日期 = N'20181205')
```
![](https://i.imgur.com/vHfp5HR.png)

```sql=
SELECT 股票代號
FROM dbo.日收盤
WHERE 日期 = N'20181206'
EXCEPT
SELECT 股票代號
FROM dbo.日收盤
WHERE 日期 = N'20181205'
```
### 35. 
* 20181206 有但 20181205 也有的股票代號

```sql=
SELECT 股票代號
FROM dbo.日收盤
WHERE 日期 = N'20181206'
AND 股票代號  IN (SELECT 股票代號
                  FROM dbo.日收盤
                  WHERE 日期 = N'20181205')
```
![](https://i.imgur.com/wg7JNNv.png)

```sql=
SELECT 股票代號 FROM dbo.日收盤
WHERE 日期 = N'20181206'
INTERSECT 
SELECT 股票代號 FROM dbo.日收盤
WHERE 日期 =   N'20181205'
```


### 36. 
* 2018所有日收盤資料
* 加上該檔股票的英文名稱
* (使用[日收盤]、在[上市櫃資本資料表]中找出該筆資料所符合的年度與股票代號所對應的英文名稱)

```sql=
USE StockDB

select B.英文名稱, A.*  
from 日收盤  AS A
INNER JOIN 上市櫃基本資料表  AS B ON A.股票代號 = B.股票代號 AND B.年度 = '2018'
where 日期 BETWEEN N'20180101'  AND N'20181231'

ORDER BY 股票代號
```
![](https://i.imgur.com/nqLgpQA.png)

![](https://i.imgur.com/8EH3dAb.png)



***
(測試)
```sql=
select 日收盤.*  
from 日收盤 
where 日期 LIKE N'201812%' 
```
![](https://i.imgur.com/AFIVJbJ.png)


### 37. 
* [ETF基本資料表]中2018年的資料
* 以及該ETF在20181214的[日收盤]資料
* 且[ETF基本資料表]的[名稱]與[日收盤]的[股票名稱]不同
* 顯示[ETF基本資料表]的[年度][名稱]
* 以及[日收盤]的[日期][股票名稱]。


```sql=
SELECT ETF.[年度], ETF.[名稱],ETF.[代號] , DCS.[日期], DCS.[股票名稱]
FROM ETF基本資料表 AS ETF 
INNER JOIN 日收盤  AS DCS 
ON  (ETF.代號 = DCS.股票代號) AND  DCS.日期 = N'20181214' AND ETF.年度 = '2018'
AND ETF.名稱 != DCS.股票名稱
```
![](https://i.imgur.com/YFffiVK.png)


### 38. 
* 20181201 到 20181206 的所有資料
* [上市櫃]欄位的規則：
    * [上市櫃] = 1 時顯示上市
    * [上市櫃] = 2 時顯示上櫃
    * [上市櫃] = 3 時顯示興櫃
    * [上市櫃] = 4 時顯示公發
    * [上市櫃] = 其他數字 時顯示其他

```sql=
SELECT  日收盤.CTIME, 
     日收盤.MTIME, 
     RecordID,
     日期, 
     日收盤.股票代號,
 
     日收盤.股票名稱, 
     參考價, 
     開盤價, 
     最高價, 
     最低價, 
     收盤價, 
     漲跌, 
     CASE [上市櫃]
        WHEN 1 THEN '上市'
        WHEN 2 THEN '上櫃'
        WHEN 3 THEN '興櫃'
		WHEN 4 THEN '公發'
		ELSE '其他'
     END AS [上市櫃], 
     日收盤.產業代號,
     漲停價,
     跌停價,
	 漲跌狀況,
	 最後委買價,
	 最後委賣價,
	 [漲幅(%)],
	 [振幅(%)],
	 成交量,
	 [成交量(股)], 
	 成交筆數, 
	 [成交金額(千)],
	 [成交值比重(%)],
	 [成交量變動(%)],
	 [股本(百萬)2],
	 [均張變動(%)],
	 均張,
	 [總市值(億)],
	 [市值比重(%)],
	 類股本益比,
	 類股股價淨值比,
	 類股股價淨值比2,
	 本益比,
	 [本益比(近四季)],
	 [股東權益(百萬)],
	 股價淨值比,
	 股價淨值比2,
	 [舊股本(百萬)],
	 委買張數,
	 委賣張數,
	 次日參考價,
	 [成交金額_元],
	 均價,
	 [股本(百萬)] ,
	 週轉率,
	 漲跌停
FROM 日收盤
WHERE  (日收盤.日期 between N'20181201'and N'20181206') 
```
### 39. 
* 20181201到20181206的所有股票資料
* 並新加一個欄位[Range]
* 當收盤價<10時顯示Under_10
* 10<=收盤價<50顯示Under_50
* 50<=收盤價<100顯示Under_100
* 收盤價>=100時顯示Over_100

```sql=
SELECT *, case AS [Range]
  when [收盤價]<10 then 'Under_10'
  when 10<=收盤價 AND 收盤價<50 then 'Under_50'
  when 50<=收盤價 AND 收盤價<100then 'Under_100'
  when 收盤價>=100 then 'Under_100'
END
AS Range
FROM 日收盤
WHERE 日期 BETWEEN  N'20181201' AND N'20181206';
```
![](https://i.imgur.com/qIDWzDc.png)

### 40. 
* 20181206的所有股票資料
* 並以收盤價>5遞減、收盤價<=5遞增排序

```sql=
SELECT * 
FROM dbo.日收盤
WHERE 日期 = N'20181206'
ORDER BY 
CASE WHEN 收盤價 >5 THEN  收盤價  END DESC,
CASE  WHEN 收盤價 <= 5 THEN 收盤價 END ASC
```
![](https://i.imgur.com/26qKS6H.png)
***
![](https://i.imgur.com/XIQUZpf.png)

### 41. 
* 找出頭1000檔股票"各自"最大日期的所有欄位資料

```sql=
USE StockDB
CREATE TABLE #HeyYo_Local_table 
(
    股票代號  nvarchar(10),
	 日期 nvarchar(8) ,
);

INSERT INTO #HeyYo_Local_table
SELECT  TOP(1000)股票代號, MAX(日期) 
FROM 日收盤 
GROUP BY 股票代號

SELECT 日收盤.*
FROM 日收盤 INNER JOIN #HeyYo_Local_table  ON 日收盤.股票代號 = #HeyYo_Local_table.股票代號 AND 日收盤.日期 = #HeyYo_Local_table.日期
```
```sql=
SELECT  TOP(1000)股票代號, MAX(日期) 
FROM 日收盤 
GROUP BY 股票代號

```
執行結果
![](https://i.imgur.com/DqZ4CCw.png)


頭1000筆
![](https://i.imgur.com/HubUIgA.png)

```sql=
/*第二種寫法*/
;with cte as (
 Select top(1000)股票代號, max(日期) AS 最大日期
 From 日收盤
 Group by 股票代號
 
)

Select *
From 日收盤 inner join cte 
on (日收盤.股票代號= cte.股票代號) 
and (日收盤.日期= cte.最大日期)

ORDER BY 日收盤.股票代號
```
```sql=
/*第三種寫法*/
SELECT  *

FROM(
	SELECT   * ,ROW_NUMBER() OVER(PARTITION BY 股票代號 ORDER BY 日期 DESC) AS rowNum 
	FROM 日收盤
	WHERE 股票代號 IN (SELECT DISTINCT  TOP(1000) 股票代號 FROM 日收盤)
	) AS TopStockGroup

WHERE TopStockGroup.rowNum =1
ORDER BY 股票代號
```



---
## 42. UNION、UNION ALL差別?
* UNION及UNION ALL兩個指令之差異在於是否會剔除重複的資料
![](https://i.imgur.com/cZjN2Xx.png)

* UNION ALL指令是將各子查詢的資料集直接合併，UNION指令則是合併後排序並剔除重複資料；因UNION需排序動作故將耗用較多之資源




2、UNION中的每个查询必须包含相同的列、表达式或聚集函数（不过各个列不需要以相同的次序列出）。

3、列数据类型必须兼容，类型不必完全相同，但是必须是DBMS可以隐含地转换类型。
*https://learn.microsoft.com/zh-tw/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-ver16

4、UNION从查询结果集中自动去除重复行。

5、若需要每个条件的匹配行全部出现（包括重复行），则必须使用UNION ALL 而不是WHERE。


7、在使用UNION组合查询时，只能使用一条ORDER BY 子句，它必须出现在最后一条SELECT语句之后，即对于结果集不存在用一种方式排序一部分，而又用另一种


## 43. UNION、EXCEPT、INTERSECT差別?畫出交集圖
* UNION 將2個資料表的紀錄垂直結合再一起，有重複的資料只會顯示其中一筆(作垂直結合 (合併多個資料表中的紀錄))

* INTERSECT將兩個資料表相同的紀錄取出來，且有重複的資料只會顯示其中一筆

* EXCEPT 只取出第一個select指令但是不存在第二個select指令的紀錄。

![](https://i.imgur.com/q8ZGoE8.png)



## 44. 解釋 INNER JOIN、RIGHT JOIN、LEFT JOIN、OUTER JOIN

![](https://i.imgur.com/z53fJqg.png)
![](https://i.imgur.com/7qbZK9i.png)
![](https://i.imgur.com/fM6ixaz.png)
![](https://i.imgur.com/VPercxF.png)

## 45. 上市櫃基本資料表、興櫃基本資料表、公開發行基本資料表，取出下列指定欄位後合併為一張表
```text!
名稱(股票名稱) AS 股票名稱
代號(股票代號) AS 股票代號
上市上櫃 AS 市場別
上市上櫃 AS 信評市場別
公司名稱 AS 公司名稱
統一編號 AS 統一編號

取上市櫃基本資料表 的最新年度 且 [掛牌交易中] = 1
取興櫃基本資料表 的最新年度 且 [啟用] = 1
取公開發行基本資料表的 最新年度 且 [啟用] = 1 且 [代號]不等於6536

提示：興櫃基本資料表的[上市上櫃]皆為3
公開發行基本資料表的[上市上櫃]皆為4
上市上櫃 = 市場別 = 信評市場別
```

```sql=
DECLARE @上市櫃最新年度 CHAR(4)
SELECT   @上市櫃最新年度 = MAX(年度) 
FROM 上市櫃基本資料表

DECLARE @興櫃最新年度 CHAR(4)
SELECT   @興櫃最新年度 = MAX(年度) 
FROM 興櫃基本資料表

DECLARE @公開發行最新年度 CHAR(4)
SELECT   @公開發行最新年度 = MAX(年度) 
FROM 公開發行基本資料表

SELECT 股票名稱,                 股票代號, 上市上櫃 AS 市場別, 上市上櫃 AS 信評市場別, 公司名稱, 統一編號 
FROM 上市櫃基本資料表
WHERE 年度 LIKE @上市櫃最新年度 AND 掛牌交易中 = 1

UNION
SELECT 名稱 AS 股票名稱, 代號 AS 股票代號, 上市上櫃 AS 市場別, 上市上櫃 AS 信評市場別, 公司名稱, 統一編號 
FROM 興櫃基本資料表
WHERE 年度 LIKE @興櫃最新年度 AND 啟用 = 1

UNION
SELECT 名稱 AS 股票名稱, 代號 AS 股票代號, 上市上櫃 AS 市場別, 上市上櫃 AS 信評市場別, 公司名稱, 統一編號 
FROM 公開發行基本資料表
WHERE 年度 LIKE @公開發行最新年度 AND 啟用 = 1 AND 代號 != N'6536'
```
## 46. 找出日收盤內2018年正在掛牌交易中的各檔股票的最大日期資料(使用[日收盤]、在[上市櫃基本資料表]中找出該筆資料所符合的年度與股票代號所對應的掛牌交易中)

```sql= 
/*solution1*/
SELECT  DDD.股票代號, MAX (DDD.日期)
FROM 日收盤 AS DDD INNER JOIN 上市櫃基本資料表 AS UUU 
ON (DDD.股票代號 = UUU.股票代號  ) AND (UUU.掛牌交易中 = 1) AND (UUU.年度 = '2018') AND(DDD.日期 like N'2018%')
GROUP BY DDD.股票代號  
```

``` sql= 
/*solution2*/
;WITH on_marketing AS (
SELECT 股票代號 AS stock_id,
       英文名稱 AS company_name
  FROM 上市櫃基本資料表
 WHERE 年度 = 2018 AND 掛牌交易中 = 1
)

SELECT 日收盤.股票代號,
	   日期= MAX(日期),年度 = 2018,  掛牌交易中 = 1
FROM 日收盤
LEFT JOIN on_marketing
ON 日收盤.股票代號 = on_marketing.stock_id
 
WHERE 日收盤.日期 LIKE N'2018%'
GROUP BY 日收盤.股票代號 
ORDER BY 日收盤.股票代號 ASC
```

``` sql= 
/*NEW*/
SELECT* 

FROM 日收盤 AS D INNER JOIN

(
SELECT 股票代號 ,MAX(日期) AS 最大日期
FROM 日收盤 
WHERE 股票代號 IN (
	SELECT 股票代號
	FROM [上市櫃基本資料表]
	WHERE 掛牌交易中 =1 AND 年度 = N'2018') 
	AND 日收盤.日期 BETWEEN N'20180101'AND N'20181231'
	GROUP BY 股票代號
) AS K ON (D.日期 = K.最大日期) AND(D.股票代號 = K.股票代號)
```

## 47. 請找出[日借貸款項擔保品餘額表]中，[券商不限用途借貸前日餘額]不等於[券商不限用途借貸餘額]的資料

```text!
例如股票代號3576在20181218的[券商不限用途借貸前日餘額]為2424
而20181217的[券商不限用途借貸餘額]為2425
請試著找出這種狀況的股票，結果的格式如下。
(注意：要與前一個交易日比較，例如是星期一的資料要與星期五比較)
```

|   日期   | 股票代號 | 券商不限用途借貸餘額 |   日期   | 股票代號 | 券上不限用途借貸前日餘額 |
|:--------:|:--------:|:--------------------:|:--------:|:--------:|:------------------------:|
| 20181217 |   3576   |         2425         | 20181218 |   3576   |           2424           |



```sql=
SELECT 前日.日期,前日.股票代號, 前日.券商不限用途借貸餘額, 今日.日期, 今日.股票代號, 今日.券商不限用途借貸前日餘額

FROM 

(SELECT ROW_NUMBER() OVER(PARTITION BY 股票代號 ORDER BY 日期 DESC) AS 日期資料排序, * FROM dbo.[日借貸款項擔保品餘額表]) AS 今日 

JOIN 

(SELECT ROW_NUMBER() OVER(PARTITION BY 股票代號 ORDER BY 日期 DESC) AS 日期資料排序, * FROM dbo.[日借貸款項擔保品餘額表]) AS 前日

ON (今日 .股票代號 = 前日.股票代號) AND (今日.日期資料排序 = 前日.日期資料排序-1)

WHERE 今日.券商不限用途借貸前日餘額 != 前日.券商不限用途借貸餘額

```
## 48. 找出20161212所有[日收盤_新人訓練_2016]的收盤價不等於[日收盤]的收盤價的資料
```sql=
SELECT N.股票代號,N.股票名稱, N.收盤價,D .股票代號, D .收盤價

FROM [StockDB].[dbo].[日收盤_新人訓練_2016] AS N
  JOIN 日收盤 
  AS D 
  ON D.股票代號= N.股票代號  AND D.日期 = N'20161212' AND N.日期 = N'20161212'
  WHERE (N.收盤價!= D.收盤價)
```
## 49. 請更新所有[港股上市公司基本資料表]的[普通股發行股數(百萬股)]欄位
* 計算方式如下：來源為：New_HK_DB裡的[HK_SHGSTUDTLCOM]資料表[普通股發行股數(百萬股)] = [NUMPUSH]欄位 / 1000000再取到小數第三位
* 取用條件：[REFCSHGTY] = 1 AND [AREAREFCSHG] = 1 			and [NUMPUSH] > 0 	       	and [ISVALID] = 1
* 對應方式： [公司統一編號] = [COMUNIC]取該檔股票每年最新的[CHDATE]對應[港股上市公司基本資料表]的年度如果該檔股票當年沒有對應的[CHDATE]則取小於該年的最大[CHDATE]


備註: NUMPUSH: 200079910，年度2017無對應到CHDATE所以取<2017最大的CHDATE

|  COMYNIC  | 發行股數(百萬股) | years |          CHDATE           |
|:---------:|:----------------:|:-----:|:-------------------------:|
| 200079910 | 3856.24100000000 | 2018  | 2018-09-19 00\:00\:00.000 |
| 200079910 | 3857.67900000000 | 2017  | 2016-09-19 00\:00\:00.000 |
| 200079910 | 3857.67900000000 | 2016  | 2016-09-19 00\:00\:00.000 |
| 200079910 | 3859.67900000000 | 2015  | 2015-09-19 00\:00\:00.000 |
| 200079910 | 2316.16400000000 | 2014  | 2011-09-19 00\:00\:00.000 |
```sql= 

/*CTE_GN  = 港股上市公司基本資料表*/
;WITH CTE_GN AS (
SELECT  ROW_NUMBER() OVER (PARTITION BY 公司統一編號 ORDER BY 公司統一編號 DESC ,年度 DESC)AS rowNum,
 convert (int, [年度]) as 年度
      ,[代號]
      ,[名稱]
      ,[公司統一編號]
      ,[英文名稱]
      ,[公司名稱]
      ,[公司英文名稱]
      ,[地址]
      ,[電話]
      ,[傳真]
      ,[掛牌日期]
      ,[董事會主席]
      ,[董事總經理、行政總裁]
      ,[公司秘書]
      ,[上市板別]
      ,[產業分類代號]
      ,[產業分類]
      ,[產業代號]
      ,[產業名稱]
      ,[主要業務]
      ,[主要股東]
      ,[主要往來銀行]
      ,[律師]
      ,[會計師]
      ,[網址]
      ,[電子信箱]
      ,[股票過戶機構]
      ,[國際證券編碼]
      ,[法定股本(百萬股)]
      ,[流通在外特別股股數(百萬股)]
      ,[流通在外其他股股數(百萬股)]
      ,[買賣單位]
      ,[交易幣別]
      ,[面值(元)]
      ,[面值幣別]
      ,[最新公佈股利(元)]
      ,[最新股利幣別]
      ,[最新股利對應期間]
      ,[指數成份股]
      ,[指數成份股(Eng)]
      ,[相關上市股票]
      ,[補充說明]
      ,[訊息披露網站]
      ,[地域位置]
      ,[股票類型]
      ,[ISVALID]
      ,[CTIME]
      ,[MTIME]
      ,[普通股發行股數]
	  ,[普通股發行股數(百萬股)]

FROM StockDB.DBO.港股上市公司基本資料表
)

/*CTE_HK  = [HK_SHGSTUDTLCOM]資料表*/
,CTE_HKORG AS( 
SELECT COMUNIC, 
	    ROUND([NUMPUSH]/1000000,3) AS STOCKQT,
		YEAR([CHDATE]) AS year_1,
	    [CHDATE]
      ,[REFCSHGTY]
      ,[AREAREFCSHG]
      ,[NUMPUSH]
      ,[ISVALID]
	    
      FROM HK_SHGSTUDTLCOM

	  WHERE [ISVALID]=1 AND [NUMPUSH] > 0 AND [REFCSHGTY] = 1 AND [AREAREFCSHG] = 1 
)

, CTE_HK AS (
SELECT COMUNIC, 
	    STOCKQT,
		 year_1,
	    [CHDATE]
      ,[REFCSHGTY]
      ,[AREAREFCSHG]
      ,[NUMPUSH]
      ,[ISVALID] ,ROW_NUMBER() OVER(PARTITION BY COMUNIC, year_1 ORDER BY CHDATE DESC) AS rowNumber
FROM CTE_HKORG
)

/* 組合 CTE_GN & CTE_HK 對應方式： [公司統一編號] = [COMUNIC]*/
,CTE_yo as(
SELECT 	
		rowNum,
       [年度],
/*
      [代號]
      ,[名稱],

*/
      [公司統一編號]
/*
      ,[英文名稱]
      ,[公司名稱]
      ,[公司英文名稱]
      ,[地址]
      ,[電話]
      ,[傳真]
      ,[掛牌日期]
      ,[董事會主席]
      ,[董事總經理、行政總裁]
      ,[公司秘書]
      ,[上市板別]
      ,[產業分類代號]
      ,[產業分類]
      ,[產業代號]
      ,[產業名稱]
      ,[主要業務]
      ,[主要股東]
      ,[主要往來銀行]
      ,[律師]
      ,[會計師]
      ,[網址]
      ,[電子信箱]
      ,[股票過戶機構]
      ,[國際證券編碼]
      ,[法定股本(百萬股)]
      ,[流通在外特別股股數(百萬股)]
      ,[流通在外其他股股數(百萬股)]
      ,[買賣單位]
      ,[交易幣別]
      ,[面值(元)]
      ,[面值幣別]
      ,[最新公佈股利(元)]
      ,[最新股利幣別]
      ,[最新股利對應期間]
      ,[指數成份股]
      ,[指數成份股(Eng)]
      ,[相關上市股票]
      ,[補充說明]
      ,[訊息披露網站]
      ,[地域位置]
      ,[股票類型]
      ,GN.[ISVALID]
*/
      ,[CTIME]
      ,[MTIME]
      ,[普通股發行股數]
	  ,[普通股發行股數(百萬股)]
      ,HK.STOCKQT 
	  ,rowNumber
	  , year_1
	  ,ROW_NUMBER() OVER(PARTITION BY GN.公司統一編號,GN.年度  ORDER BY CHDATE DESC) AS rN  
	  FROM CTE_GN AS GN LEFT JOIN CTE_HK AS HK ON (GN.公司統一編號 = HK.COMUNIC ) AND rowNumber = 1
)

/* 篩選 --方式： (年度)取等於& 小於 */
, CTE_Year AS(
SELECT  
		*,
		ROW_NUMBER() OVER(PARTITION BY 公司統一編號, 年度  ORDER BY 年度 DESC, year_1 DESC) AS rowNumberIndex
FROM CTE_yo
WHERE  ((CTE_yo.年度 = CTE_yo.year_1) or (CTE_yo.year_1 <  CTE_yo.年度 ))

)

/*取出目標*/
select 
		
		公司統一編號,
		年度,
		STOCKQT AS [普通股發行股數(百萬股)],
		year_1
from CTE_Year 
where  ([公司統一編號] in( N'200079970', N'200079969') ) AND rowNumberIndex = 1

order by 公司統一編號 desc, 年度 desc, year_1 desc 
 

```
## 50.預存程序是什麼？ 
* 預存程序是SQL Server的一個功能，能夠先存下一段SQL的指令以便之後使用。有點類似於把一個函數存在一個變數裡，之後呼叫這個變數的時候就可以跑裡面的程式碼。
* 預存程序，又稱儲存程序（英語：Stored Procedure），是在資料庫儲存複雜程式，以便外部程式呼叫的資料庫物件，可以視為資料庫的一種函式或子程式

優點
預存程序具有下列的好處：

預存程序可封裝，並隱藏複雜的商業邏輯。
預存程序可以回傳值，並可以接受參數。
預存程序無法使用 SELECT 指令執行，因為它是子程式，與檢視表、資料表或使用者定義函式不同。
預存程序可以用在資料檢驗，強制實行商業邏輯等。

缺點
預存程序，往往客製化於特定的資料庫上，因為支援的程式語言不同。當切換到其他廠商的資料庫系統時，需要重寫原有的預存程序。
預存程序的效能調校與撰寫，受限於各種資料庫系統。

## 51. 建立一個預存程序，回傳整數2
``` sql=
use StockDB

CREATE PROCEDURE HEYHEYYOTO
	-- Add the parameters for the stored procedure here
	@TEST INT = NULL OUTPUT
	
AS
BEGIN
	-- SET NOCOUNT ON added to prevent extra result sets from
	-- interfering with SELECT statements.
	SET NOCOUNT ON;
	SET @TEST = 2
    RETURN @TEST
END
GO
```

``` sql=
use StockDB

declare @hey int;
exec @hey = HEYHEYyo
select @hey 
```
![](https://i.imgur.com/PfBHCzB.png)


## 52. 修改、刪除預存程序的語法？
* 修改
``` sql=
ALTER PROCEDURE dbo.HEYHEYyo /*注意: 名稱不能是sp_開頭!("sp_"是預留給系統的))*/
	@TEST INT = NULL OUTPUT
AS
BEGIN
 /*這邊要改成不要讓程序每次結束都回傳影響了多少row的訊息*/
	SET NOCOUNT ON;

 /*從這邊開始輸入要預存的SQL指令*/
	SET @TEST = 888
    RETURN @TEST
END
```
![](https://i.imgur.com/bNB9S0R.png)

* 刪除
```sql=
DROP PROCEDURE dbo.HEYHEYyo  
GO  
```
## 53. 預存程序可以如何回傳值？
要將資料傳回一般有 3 種方式：
1. 使用 SELECT 以表格方式傳回。
2. 設定 Output Parameter 以參數方式傳回。
3. 使用 RETURN 傳回 1 個整數型別的資料。

https://dotblogs.com.tw/walter/2009/07/04/how-to-get-store-procedure-return-value

## 54. 自訂函數是什麼？
WHY : 以利重複使用
WHAT: 由使用者預先使用SQL敘述建立一連串指令
自訂函數:能回傳除了TEXT, NTEXT, IMAGE... 以外的各類型的值/不能用OUTPUT/不允許異動(新刪修)資料庫的資料與狀態/可以用EXEC 或 作為回傳值

## 55. 建立一個自訂函數，回傳日收盤20181214的資料
```sql=
use StockDB

CREATE FUNCTION [dbo].[GetData] (@date nvarchar(8))
RETURNS TABLE
AS
RETURN
(
    SELECT *
    FROM dbo.日收盤
    WHERE 日期 = @date
);
```
```sql=
SELECT * FROM GetData(N'20181214');
```
![](https://i.imgur.com/ItMsBEo.png)

## 56. 建立一個自訂函數，回傳日收盤最大日期(回傳NVARCHAR(8))
```sql=
CREATE FUNCTION [dbo].[GetDataMaxDate]()     
RETURNS nvarchar(8)
AS
-- Returns the stock level for the product.
BEGIN
    DECLARE @date nvarchar(8);
	select @date = MAX(dbo.日收盤.日期)from dbo.日收盤
    RETURN @date;
END;
```
```sql=
use  StockDB

DECLARE @DATE NVARCHAR(8)
SET @DATE = dbo.GetDataMaxDate() 
SELECT @DATE AS 'DATE'
```
![](https://i.imgur.com/tIuI1sX.png)

## 57. 修改、刪除自訂函數的語法？
* 修改
```sql=
ALTER FUNCTION [dbo].[GetDataMaxDate]()     
RETURNS nvarchar(8)
AS
-- Returns the stock level for the product.
BEGIN
    DECLARE  @date nvarchar(8);
	select @date= MIN(dbo.日收盤.日期)  from dbo.日收盤

    RETURN @date;
END;
```
* 刪除

```sql=
DROP FUNCTION [dbo].[GetDataMaxDate]
```

## 58. 自訂函數與預存程序的差別？
自訂函數:能回傳除了TEXT, NTEXT, IMAGE... 以外的各類型的值/不能用OUTPUT/不允許異動(新刪修)資料庫的資料與狀態/可以用EXEC 或 作為回傳值

預存程序:只能回傳整數值/能用OUTPUT/可以修改資料庫的設定及資料表的資料與狀態/只能用EXEC/可以使用TRY&CATCH


## 59. 觸發程序是什麼？
* 觸發程序是一種特殊的預存程序，其會在資料庫伺服器發生事件時自動執行。

如同字面上的意思，在 INSERT、UPDATE、DELETE 等事件發生時，會觸發執行的程式。
分成兩種: 
DDL--觸發於資料庫結構發生變化
DML--觸發於資料表發生變化(新增刪除修改)


## 60. 建立一個觸發程序，當有[日收盤]資料異動時修改MTIME = 0
```sql=
CREATE TRIGGER datatrigger ON dbo.日收盤_新人訓練_陳鵬升
AFTER UPDATE, INSERT, DELETE -- 觸發事件
AS 
BEGIN
    UPDATE [日收盤]
    SET MTIME = 0
    WHERE [日收盤].股票代號 IN (SELECT 股票代號 FROM inserted)
END
```
*BEFORE 
![](https://i.imgur.com/8WXCKpP.png)

*AFTER 
```sql=
UPDATE dbo.日收盤_新人訓練_陳鵬升 
SET [股票名稱] = 'HEY';
```
=>
![](https://i.imgur.com/JMVJ8Kr.png)

## 61. 修改、刪除、停用、啟用觸發程序的語法
* 停用 
```sql=
DISABLE TRIGGER datatrigger ON dbo.日收盤_新人訓練_陳鵬升
```
--NO TRIGGER
![](https://i.imgur.com/YsgC6Tr.png)

* 啟用
```sql=
ENABLE TRIGGER datatrigger ON dbo.日收盤_新人訓練_陳鵬升
```
--TRIGGER SUCCCESSFULLY!
![](https://i.imgur.com/eShd4d1.png)

* 修改

```sql=
ALTER TRIGGER [dbo].[datatrigger] ON [dbo].[日收盤_新人訓練_陳鵬升]
FOR UPDATE, INSERT, DELETE -- 觸發事件
AS 
BEGIN
    UPDATE dbo.日收盤_新人訓練_陳鵬升 
	SET [MTIME] = 0123456;
	SELECT * 
	FROM dbo.日收盤_新人訓練_陳鵬升 
END
```
![](https://i.imgur.com/zqVfFno.png)
* 刪除
```sql=
DROP TRIGGER [dbo].[datatrigger]
```
![](https://i.imgur.com/1KfVd7F.png)

## 62. CTE是什麼？
在SQL的世界中，我們經常需要進行多表的取用，因此會使用子查詢(subquery)，但為了簡化子查詢的易讀性，與達成遞迴查詢，因而有CTE的寫法，透過CTE暫存資料來讓query整體好讀。

CTE 是一個「暫存」的結果集合，透過暫時儲存 AS 括號中的 Query 結果。
定義好的CTE就如暫存表，讓我們在往後進行查詢時，可以簡單透過修改SQL_statement 來改變要進行查詢的資料。不會進入i/o,功能類似暫存表，但必須與查詢語句合併使用

多個CTE中間用逗號(,)分隔。
可以使用遞迴CTE來表示階層

https://medium.com/jimmy-wang/sql-with-as%E9%80%B2%E8%A1%8C%E5%AD%90%E6%9F%A5%E8%A9%A2-cte-sql-004-e045147f0317

## 63. 建立一個CTE，回傳日收盤20181214的資料
```sql=
;WITH CTE AS (
    SELECT * FROM StockDB.dbo.[日收盤] 
    WHERE 日期 = '20181214'
)
SELECT * FROM CTE;
```

## 64. Temp Table 是什麼？
暫存資料表(Temp Table)就是暫存資料的儲存處，於系統執行過程中，
經常需要執行一些複雜的運算與處理，在run-time過程不免會產生
**一些階段性與過渡性for後續處理的資料(資料在整個處理作業完成後便不再需要而必須刪除)**
像此類資料就適合存放於暫存資料表，可以想像成寫在程式中就會是對應為DataTable，用於資料變數傳遞用途、後製處理等等。 

暫存資料表由開發人員自行創建，不管您於任何資料庫中建立暫存資料表，
暫存資料表皆固定存放於系統資料庫的 tempdb中，而且
一旦不使用會自動被刪除。

總共分成三種

1 資料表變數 (儲存於**memory**中)
2 區域暫存表 (儲存在**tempdb**中)
3 全域暫存表 (儲存在**tempdb**中)

## 65. 建立一個Temp Table，裡面有日收盤20181214的資料
```sql=
USE StockDB
CREATE TABLE #HeyYo_Local_table (
    CTIME  datetime DEFAULT getdate(),
	MTIME  int,
	RecordID  bigint,
	 日期 nvarchar(8) ,
	 股票代號  nvarchar(10),
	股票名稱  nvarchar(20),
	 參考價  decimal(9, 2),
	 開盤價  decimal(9, 2),
	 最高價  decimal(9, 2),
	 最低價  decimal(9, 2),
	 收盤價  decimal(9, 2),
	 漲跌    decimal(9, 2),
	 primary key(日期 DESC, 股票代號 ASC),
	 
	 上市櫃        nvarchar(2),
	 產業代號      nvarchar(2),
	 漲停價        decimal(9, 2),
	 跌停價        decimal(9, 2),
	 漲跌狀況      nvarchar(2),
	 最後委買價    decimal(9, 2),
	 最後委賣價    decimal(9, 2),
	 [漲幅(%)]     decimal(9, 2),
	 [振幅(%)]     decimal(9, 2),
	 成交量        bigint,
	 [成交量(股)]  bigint, 
	 成交筆數      bigint, 
	 [成交金額(千)] bigint,
	 [成交值比重(%)] decimal(9, 2),
	 [成交量變動(%)]  decimal(9, 2),
	 [股本(百萬)2]   decimal(19, 2),
	 [均張變動(%)]   decimal(9, 2),
	 均張 decimal(9, 1),
	 [總市值(億)] decimal(9, 1),
	 [市值比重(%)] decimal(9,3),
	 類股本益比 decimal(9, 1),
	 類股股價淨值比 decimal(9, 2),
	 類股股價淨值比2 decimal(9, 2),
	 本益比 decimal(9, 1),
	 [本益比(近四季)] decimal(9, 1),
	 [股東權益(百萬)] decimal(19, 2),
	 股價淨值比 decimal(9, 2),
	 股價淨值比2 decimal(9, 2),
	 [舊股本(百萬)] real,
	 委買張數 int,
	 委賣張數 int,
	 次日參考價 decimal(9,2),
	 [成交金額_元] bigint,
	 均價 decimal(9, 2),
	 [股本(百萬)] decimal(19,2),
	 週轉率 decimal(9, 2),
	 漲跌停  smallint
);


INSERT INTO #HeyYo_Local_table
SELECT * FROM StockDB.dbo.[日收盤] 
WHERE 日期 = '20181214'

/*
SELECT * FROM #HeyYo_Local_table
WHERE 日期 = '20181214'
*/
```
```sql=
/* **全域暫存表** */
CREATE TABLE ##HeyYo_Global_table (
    )
INSERT INTO ##HeyYo_Local_table
SELECT * FROM StockDB.dbo.[日收盤] 
WHERE 日期 = '20181214'

/*
SELECT * FROM #HeyYo_Local_table
WHERE 日期 = '20181214'
*/
```
```sql=
/*資料表變數*/
SELECT * INTO [@table_variable] FROM [日收盤] WHERE [日期] LIKE N'20181214';
```

![](https://i.imgur.com/8Duyylh.png)
---
## 66. Temp Table的生命週期？
* 全域暫存表: 始於建表查詢，含建表動作之查詢關閉之後，其他查詢不能使用，正在執行中的查詢結束後自動消滅

* 區域暫存表: 始於建表查詢，建立表的使用者離線之後自動消滅 (註:官方建議手動刪除)

* 資料表變數: 始於建表查詢，該次SQL指令結束後生命自動消滅

## 67. 執行計畫的用途？如何看執行計畫？
SQL Server执行计划是一种由SQL Server生成的报告，显示了数据库引擎如何执行查询的细节。执行计划包含了查询的所有步骤，并显示了每个步骤的成本、统计信息和其他详细信息。

执行计划的用途包括：

优化查询性能：执行计划可以帮助您识别查询中的瓶颈，如缺少索引、表扫描等。您可以根据执行计划中的信息来优化查询，提高性能。
诊断问题：如果您的查询运行缓慢或出现错误，您可以使用执行计划来诊断问题。执行计划可以帮助您确定哪个步骤导致了问题，以及如何解决问题。
预估查询成本: 执行计划可以给出每个步骤的估算成本，这可以帮助您估计查询的总成本，并且可以比较不同的查询计划，以便选择性能最佳的一个。
SQL Server中可以使用许多工具来生成和查看执行计划，如 SQL Server Management Studio中的“显示执行计划”功能，或者 使用 system stored procedure like sp_executesql, sys.dm_exec_query_plan。
![](https://i.imgur.com/Il5ponN.png)
![](https://i.imgur.com/JIXuKjl.png)
![](https://i.imgur.com/myGeTjg.png)



## 68. 活動監視的用途？如何看活動監視？
[活動監視器] 會在監視的執行個體上執行查詢，以便取得 [活動監視器] 顯示窗格的資訊。 當重新整理間隔的設定小於 10 秒時，用來執行這些查詢的時間就可能會影響伺服器效能。
若要檢視實際活動，您必須具有 VIEW SERVER STATE 權限。 若要檢視活動監視器的 [資料檔案 I/O] 區段，除了 VIEW SERVER STATE 之外，您也必須具有 CREATE DATABASE、ALTER ANY DATABASE 或 VIEW ANY DEFINITION 權限。

![](https://i.imgur.com/C2NawOm.png)


## 69. 當語法執行過久時可能的原因是？如何解決？
資料庫效能瓶頸大多發生在i/o , 當沒有索引值輔助，可能會造成資料零碎的搜索而耗掉許多時間

解決辦法--開啟執行計畫，檢查占用資源的語句，對語句進行優化並且建立索引值以利加速搜索

Index Fragmentation 產生原因
* 在資料庫中，當Index存放資料的Page已經額滿時，若再新增資料就會產生一個新的Page存放資料，而導致Index的資料不連續進而降低Index效能，此時就會產生外部碎裂(External Fragmentation)的問題。

* 在資料庫中，當資料被刪除時，Index存放到Page上的資料也會被刪除，而導致在Page中會有因刪除資料而產生的空間，這些空間會造成Index的資料不連續，此時就會產生內部碎裂(Internal Fragmentation)的問題。
看了以上兩個例子可以得知，不論是對資料庫Insert還是Update都有機會產生Index Fragmentation，而一致的原因為 → Index的資料不連續。

2. 該如何做對應的調整？
為了避免索引碎裂發生，我們就必須定時替資料庫健檢，也就是進行索引重組(reorganize)或索引重建(rebuild)。

https://jerrywu-3165.medium.com/sql-server-index-fragmentation-%E7%94%A2%E7%94%9F%E5%8E%9F%E5%9B%A0-%E8%A9%B2%E5%A6%82%E4%BD%95%E5%81%9A%E5%B0%8D%E6%87%89%E7%9A%84%E8%AA%BF%E6%95%B4-f26e01e7c86c

![](https://i.imgur.com/uvIW0J1.png)




## 70. 請問下列語法的執行順序為何？

```sql =  
SELECT DISTINCT MIN(日期), 股票代號 ,MAX(收盤價)
FROM 日收盤 AS aINNER JOIN 代號表指數 AS i ON a.股票代號 = i.代號
WHERE a.日期 BETWEEN '20170720' AND '20170727'
GROUP BY a.股票代號HAVING MAX(收盤價) > 100
ORDER BY a.股票代號 DESC
```
1.FROM 日收盤 
2.ON a.股票代號 = i.代號
3.收盤 AS aINNER JOIN 代號表指數 AS i	
4.WHERE a.日期 BETWEEN '20170720' AND '20170727'
5.GROUP BY a.股票代號
6.HAVING MAX(收盤價) > 100
7.SELECT 欄位(	MIN(日期)	,股票代號	,MAX(收盤價))
8.DISTINCT
9.ORDER BY a.股票代號 DESC

![](https://i.imgur.com/RUs1QZU.png)

https://zhuanlan.zhihu.com/p/58639733
***
*  with rollup
* ![](https://i.imgur.com/4dOQagJ.png)


* ![](https://i.imgur.com/uz4JnqJ.png)

***
* with cube 
* ![](https://i.imgur.com/074PNnI.png)

***
* grouping
* ![](https://i.imgur.com/TP6OSxF.png)

***
* window function example
* ![](https://i.imgur.com/5Qw67gA.png)
* https://medium.com/%E6%95%B8%E6%93%9A%E4%B8%8D%E6%AD%A2-not-only-data/%E5%A6%82%E4%BD%95%E5%8D%81%E5%88%86%E9%90%98%E5%85%A7%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B%E8%88%87%E4%BD%BF%E7%94%A8-window-function-e24e0a7e75ba




