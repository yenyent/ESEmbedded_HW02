HW02
===
This is the hw02 sample. Please follow the steps below.

# Build the Sample Program

1. Fork this repo to your own github account.

2. Clone the repo that you just forked.

3. Under the hw02 dir, use:

	* `make` to build.

	* `make clean` to clean the ouput files.

4. Extract `gnu-mcu-eclipse-qemu.zip` into hw02 dir. Under the path of hw02, start emulation with `make qemu`.

	See [Lecture 02 ─ Emulation with QEMU] for more details.

5. The sample is designed to help you to distinguish the main difference between the `b` and the `bl` instructions.  

	See [ESEmbedded_HW02_Example] for knowing how to do the observation and how to use markdown for taking notes.

# Build Your Own Program

1. Edit main.s.

2. Make and run like the steps above.

# HW02 Requirements

1. Please modify main.s to observe the `push` and the `pop` instructions:  

	Does the order of the registers in the `push` and the `pop` instructions affect the excution results?  

	For example, will `push {r0, r1, r2}` and `push {r2, r0, r1}` act in the same way?  

	Which register will be pushed into the stack first?

2. You have to state how you designed the observation (code), and how you performed it.  

	Just like how [ESEmbedded_HW02_Example] did.

3. If there are any official data that define the rules, you can also use them as references.

4. Push your repo to your github. (Use .gitignore to exclude the output files like object files or executable files and the qemu bin folder)

[Lecture 02 ─ Emulation with QEMU]: http://www.nc.es.ncku.edu.tw/course/embedded/02/#Emulation-with-QEMU
[ESEmbedded_HW02_Example]: https://github.com/vwxyzjimmy/ESEmbedded_HW02_Example

--------------------

- [x] **If you volunteer to give the presentation next week, check this.**

--------------------

Please take your note here.
1.實驗題目
{push}和{pop}中暫存器的順序是否會影響結果?
```cpp=
//這兩指令執行結果會一樣嗎?
push {r0, r1, r2} 
push {r2, r0, r1}
```
那哪個暫存器會先被推入stack?

2.實驗步驟
（1）從上課講義了解到arm的stack堆疊順序

![](https://i.imgur.com/fpHJ6h5.png)



(2)利用 si 一步一步執行程式並用 x 觀察該記憶體位置數值的變化

3.實驗結果
```cpp=
.syntax unified

.word 0x20000100
.word _start

.global _start
.type _start, %function
_start:
	
	//
	//mov # to reg
	//
	movs	r1,	#1
	movs	r2,	#2
	movs	r3,	#3
    movs    r4, #4
	
	//
	//push
	//
	//push	{r1, r2, r3, r4}
    
	  push    {r4, r3, r2, r1}
    //
	//pop
	//
	//pop     {r5, r6, r7, r8}
      pop     {r8, r7, r6, r5}
	//
	//clean the register data
	//
	movs    r5, #0
	movs    r6, #0
	movs    r7, #0
	movs    r8, #0
    
    //
	//mov # to reg
	//
	movs	r1,	#1
	movs	r2,	#2
	movs	r3,	#3
    movs    r4, #4
	//
	//push
	//

	//
	//pop
	//
	pop     {r8, r7, r6, r5}

	//
	//b bl
	//
	bl	label01

sleep:
	b	sleep

label01:
	nop
	nop
	bx	
```
r1 r2 r3 r4 分別push 1 2 3 4 並用 x 觀察記憶體數質變化
![](https://i.imgur.com/1LG026D.png)



執行 pop 觀察到pop將從低到高位置的記憶體位置依序讀取

![](https://i.imgur.com/fJpgKwp.png)


改變暫存器的順序從原來的push{r1, r2, r3, r4} 與 push{r4, r3, r2, r1} 變成 pop{r5, r6, r7, r8}  與 pop{r8, r7, r6, r5} 執行後卻發現暫存器的順序並不影響結果

![](https://i.imgur.com/l394oBz.png)

![](https://i.imgur.com/R8Tto6a.png)

查閱手策（ARM®v7-M Architecture Reference Manual ）registers的相關設定發現{push}會自動將暫存器按照編號小到大的值存入記憶體位置低到高而且{pop}亦是如此

![](https://i.imgur.com/W46W9AS.png)

![](https://i.imgur.com/L0zpokF.png)
