ch4 行程控制 (process management)
======
### process跟program比較
+ ``` process :正在執行的程式，一個process主要有幾個組成```
    
     ``` 
     1.Code Section(程式碼、程式碼區間)

     2.Data Section(資料區間)

     3.program counter(程式計數器)

     4.Cpu Register(Base/Limit Register)

     5.Stack(多個process之間互相call來call去從事遞迴工作，用以存放位置) 
     ```

    
+ ```program :儲存在Disk中的資料``` 

### 行程狀態圖(Process State Transition Diagram)
![markdown-viewer](pstd.jpg)


### 行程排班佇列(Process Scheduling Queue)

    A. Job Queue :由一群位於Disk中，等待進入Memory之Program所集合

    B. Ready Queue :由一群位於Memory中等待執行process所集合，此Queue一般都是用Link-list儲存

    C. Device Queue :由一群等待I/O裝置的Process所形成集合，每個Device本身都有Device Queue來Record不同process請求

![markdown-viewer](psq.jpg)
```
1. 當Process待在Memory時間太長，或有其他高Priority的Process來搶資源

2. 所等待Long-Time Evevt發生，或花費長時間事情做完了(Long-Time I/O Complete)

3. 將Process從Disk中引入Memory的Ready Queue

```
**此STD是針對Memory這項資源，且Data都在Disk內**

+ 一個Process的執行時間是一連串I/O等待時間和CPU執行時間所組成(因為一個Process除了交給CPU執行外，也可能需要由I/O Device進行資料傳送)

+ 幾乎在每個電腦系統執行Process會在兩種狀態切換
    + CPU Burst Time(CPU Bound Job)
    + I/O Burst Time(I/O Bound Job)

![markdown-viewer](S__44294214.jpg)

```
Process一開始都是CPU Burst，也就是交給cpu處理Process，接著是I/O Burst，即I/O資料傳送，正常情況下Process會在這兩個

state循環，最後CPU會呼叫System Call來結束Process

```

### 行程控制表(Process Control Block;PCB)   
+ OS為了執行Process Management(行程管理)，將每個Process所相關資訊聚集建立一個Block稱PCB，每個Process都有自己的PCB

+ PCB資訊包含
    + Process ID
    + 處理行程狀態(Process位於Process STD哪個狀態)
    + 程式計數器(指名Process下一個指令位址)
    + CPU暫存器
    + CPU排班資訊(Process priority)
    + Memory管理資訊(Base/Limit Register、Page Table)
    + 帳號資訊(用掉多少CPU，使用CPU最大時間量)
    + I/O狀態資訊(尚未完成I/O Request，I/O Queue中排隊之Process之編號)

+ PCB存在User Area 或Monitor Area?
    + ANS :OS為了管理Process方便，會存一份PCB在OS所在Monitor Area中


