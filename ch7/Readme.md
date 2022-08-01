CH7 行程間溝通(Process Management)
======
### 為何Process之間需要溝通
+ 在現行的作業系統中，通常不會只有一種Process存在OS內，一般會有很多個Process同時存在System中並行進行(Concurrent Execution)

+ 存在System中並行執行的Process分成2類

    + 獨立行程(Independent Process) :如果一個Process無法影響其他Process執行，同時它不受其他Process影響，該Process為獨立行程，這類Process不會有任何共享資料

    + 合作行程(Cooperating Process) :如果一個Process能影響其他Process，或受到其他Process影響，它就屬合作行程，這些Process會共同完成一份工作，這類Process會共享資料，彼此需要資料交換和協調管道

+ 獨立的行程因為行程間不會互相影響，且行程間也是個自行完成各自工作，合作行程比較有討論空間

### Process Communication的方式

+ Share Memory方法 :

    + 各Process利用對共享Memory(共享變數)的存取來達到彼此溝通交換資訊

    + 提供對共享Memory之互斥存取控制的責任是由Programmer負責，OS只負責共享Memory空間，不提供額外Resource

+ Message Passing方法 :2個Process要溝通要遵守以下步驟

    + 建立Communication Link(溝通鏈接)

    + 互傳訊息

    + 傳輸完畢，Release Link(發佈鏈接)
    **此方法需要OS提供額外支援，(Link Management、Message Lost、Link Capacity容量控管)，而Programmer不須額外負擔**
    ```markdown
    |         Share Memory                     Message Passing
    | :----------------------------     :----------------------------                 
    |  Process彼此透過共享變數的存取       Process之間溝通須
    |  ，藉由其值改變達到溝通目的          1.建立Link
    |                                    2.建立後互傳Message
    |                                    3.Free Link
    | :----------------------------     :---------------------------- 
    |  共享變數所有Process都可存取        Link是屬於溝通雙方，不會被其他人使用                                 
    | :----------------------------     :---------------------------- 
    |  OS只提供Share Memory空間，不提供   OS會提供如Link Message等功能
    |  額外資源
    | :----------------------------     :---------------------------- 
    |  Programmer負擔重，OS輕             Programmer負擔輕，OS負擔重
    | :----------------------------     :---------------------------- 
    |  須提供對共享變數之互斥控制，否則    須提供Link Capacity、Link Creation
    |  會有問題                           Message Lost等處理
    ```

 ![markdown-viewer](S__44326933.jpg)

 ### 共享記憶體(Share Memory)

 ```
 一組Process在Share Memory中，若未對共享變數提供互斥存取等同步機制，則會造成共享變數最終結果值會因Process
 執行順序不同，造成不可預期錯誤
 ```
+ Share Memory方式可能有Race Condition(競爭狀況)

![markdown-viewer](S__44326934.jpg)

+ 需要對共享變數之存取進行管控，即同一時間只有一個Process可以使用

+ 解決Race condition的策略
    
    + Disable Interrupt(停止使用中斷) **鎖定CPU**

    + Circular Section Design(臨界區間設計) **鎖定共享變數**

### Disable Interrupt

```
為了確保對共享變數進行存取時之相關指令描述可以在執行過程中不會被Interrupt，所以在該指令敘述執行前先
Disable Interrupt，完成後再Enable Interrupt
```

![markdown-viewer](S__44326935.jpg)

**優點 :簡單、易實施**

**缺點 :只適用於Single Processor環境，不適用於Multiprocessor(表現差、風險增加)**

+ 在Multiprocessor環境下，關掉單顆CPU的Interrupt功能沒用，要關掉所有CPU的Interrupt

+ Process需發出Disable Interrupt給所有CPU，且須等待所有CPU均回覆Interrupt已Disable訊息才可執行，工作完成後需發送Enable Interrupt給所有CPU

+ Disable Interrupt的做法會導致其他緊急工作無法執行

### Critical Section Design(臨界區間設計)

```
提供對共享變數之存取動作的互斥控制，確保資料正確性
```

+ 在程式中，針對共享變數進行存取指令敘述所集合，非C.S之指令敘述集合稱Remainder Secction(剩餘空間R.S)

![markdown-viewer](S__44326936.jpg)

+ 臨界區間設計，主要設計其入口和出口的程式片段

+ 程式架構

    ```
    Repeat:
        Entry Section //進入臨界區間 :管制Process可否進入C.S的程式片段
        C.S
        Exit Section //離開臨界區間 :離開C.S後解除入口管制程式片段
        R.S
    Until False

    ```
![markdown-viewer](S__44326937.jpg)

### Critical Section Design須滿足3個特性

+ Mutual Exclusion(互斥) :在一個時間點最多只能有一個Process進入自己的C.S內活動，不允許多個Process同時進入

![markdown-viewer](S__44326938.jpg)

+ Progress(行進) :
    
    + 不想進入C.S的Process不可以阻礙其他Process進入C.S(即不可參與進入C.S的決策過程)

    + 必須在有限時間內，從那些想進入C.S的Process中挑選一個進入(防止Dead Lock)

+ Bounded Waiting(有限等待) :從Process提出進入C.S申請，到獲准C.S等待時間有限的，假設有5個Process要進入C.S，P1先進入自己的C.S，若想再進入，要等到其他4個Process都進去過才行(防止Starvation)

![markdown-viewer](S__44326939.jpg)

### Critical Section Design設定方法

+ Algorithm Base(自行設計演算法)

    + 2個Process設計方案 :3種Algorithm

    + N個Process設計方案 :1種Algorithm

### 2個Process設計方案(假設有兩個Process叫 Pi Pj)

+ **Algorithm 1** :共享變數宣告如下(Turn整數變數，初始可放 i和j )，設定Turn = i

```
    設計Pi程式設計                                  設計Pj的程式設計
    Repeat:                                         Repeat:
        while(Turn != i)do no-op //Entry                while(Turn != j)do no-op //Entry 
            C.S                                               C.S
        Tuen = j //Exit                                  Tuen = j //Exit
    Until False                                     Until False
```

+ 滿足Mutual Exclusion :Turn值不會同時擁有i或j，所以Pi和Pj不會同時進入自己的C.S

+ 不滿足Progress(不滿足條件1) :假設Turn在i，但Pi不想進入C.S，若Pj想進自己C.S無法進入，Pj被Pi阻擋

+ 滿足Bounded Waiting :若Pi和Pj都想進入C.S，此時Turn值決定，若Pi執行完想在重新進入C.S，此時Turn值 = j，要等Pj執行完
