中断的优势：cpu和低速外设进行并行工作 ，实时性好 SRAM中通信采用 IIC 组织协议 ，LCD 直接用芯片即可，连接多，有单独的数据位。 
# TBS模块代码：
 extern Tal\_Config tal\_config Tal\_Config 联合体 变量包含 ：
  typedef union { ​ WWDT\_CONFIG\_T wwdt； ​ TBS\_CONFIG\_T tbs; } Tal\_Config; 上下位机主要通过两个变量进行控制交互和数据共享： 1. TBS\_CONFIG\_T 结构体变量 tal\_config.tbs a.各个测试用例中设置这个变量中的各个数值 b.设置完毕后调用下位机的 tbs\_enable() api ，下位机将按照 a 中的参数设置 tbs 模块相应的寄存器 2. TBS\_EVENT\_T 结构变量 tal\_event.tbs a.下位机运行到 main 函数时， 上位机需要设置相应的中断响应回调函数 TBS\_IRQHandler\_Callback b.在中断响应回调函数中会记录一次 tbs 事件 c.测试最后上位机需要读取该变量，根据这个变量内容判断 tbs 的功能 ​ i. idx值是否符合预期 ​ ii.data\[\] 各路的转换值 ​ iii.time\[\] 各路的转换时间 !\[111\](https://typora-bucket-1304106066.cos.ap-shanghai.myqcloud.com/typora111.png) 
# ADC检测：
 1. 如何配置引脚的LCD功能？
 2.  IO 口复用为 ADC 我们要设置模式为模拟输入，而不是复用功能， 也不需要调用 GPIO\_PinAFConfig 函数来设置引脚映射关系。 使能GPIOA 时钟和 ADC1 时钟都很简单， RCC\_AHB1PeriphClockCmd(RCC\_AHB1Periph\_GPIOA, ENABLE);// 使能 GPIOA 时钟 RCC\_APB2PeriphClockCmd(RCC\_APB2Periph\_ADC1, ENABLE); // 使能 ADC1 时钟 初始化 GPIOA5 为模拟输入，方法也多次讲解，关键代码为： GPIO\_InitStructure.GPIO\_Mode = GPIO\_Mode\_AN;// 模拟输入 ​
 3.  2.配置好之后，如何验证分时模式下时能正常输出数据？ 
 4. 直接配置好LCD 和 ADC 中断检测，LCD不工作，ADC工作时正常？ 测试方式：使能TBS 模块后 直接两层循环使能和使能中断，看 下位机检测的路数 index 是否 > 0 还是改成ADC0DAT 使能ADC ，使能中断： idx > 0 , 正常判断 是否一样 不使能ADC: 直接判断转换失败，数据无效。 使能ADC ，不使能中断： 采用直接读取adc0 dat 进行判断是否一样就行。 # TBS 基本功能 1. 为什么需要配置滑动滤波 和 OSR 挡位 配置输出 和 采样精度 ​ 2.遍历各层参数 下位机有HT\_TBS\_PeriodSet() ht6xxxx\_tb.c 函数 or 操作寄存器 3.下位机操作寄存器可以，上位机不行。 4.虽然上位机禁止了ADC0，但是ADCDAT0没清零，还是上一次使能后留下的数据， 直接根据标志TBSIF会有些问题，没配置时，突然变成1，配置好了，迟迟不为1，猜测下位机触发响应后就立刻清除中断标志了 ​ 有点小疑问： NI平台上：禁止ADC后，每次读取数据都是0v ，但是最小系统上是上一次的值（只有掉电数据才清0，因为供电是VCC和VRTC） 不提供电压时，NI平台上读数为0 （理论上正确）， 最小系统是65535左右（具体可能是硬件的原因） 写OSR = 01 时， 另一个寄存器 TBSPRD.vcc = 2 写OSR = 10 时， TBSPRD.adc3 = 1 赵威哥，这个 写精度OSR 时 有点问题，我向TBSCON 中的OSRSEL 位写 1写不进去，反而将TBSPRD 中的 vcc 写成了2。!\[img\](C:\\Users\\kxliu\\AppData\\Local\\Temp\\86d083f1-4e75-489c-88c6-a8d3da48aac5.png) # 分时开启 1.为什么分时开启需要观察gpio的反转？ # 实时触发 低优先级触发过程中，完成后再进行高级优先级触发？ 代码： dut\_reset\_and\_run\_to\_main()的作用？进入main函数 jlink没见到打开 ，必须加上 为什么时钟直接一行 HT\_CMU\_ConfigAPB1CLKCtrl ，配置tbs模块的时钟 不知道测试方法对不对，优先级测试？
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA2Mzg0NDI3NV19
-->