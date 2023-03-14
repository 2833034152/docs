\# wwdt 学习 
### 新的测试方法：
 系统里面找一个timer0 , 4096分频 ， 产生中断 读取cnt值 arm内核： cpu + Sys Tick timer + System control block（SCB) + NVIC + MPU + FPU 1.上位机调用下位的函数：（已有的函数地址） dut\_wwdt\_test\_enable(self, step\_name = '配置WWDT测试参数') 中直接 （self.peripherals\['WWDT'\].call('wwdt\_enable\_and\_systick\_enable') 2.为什么会从本身的中断响应函数跳转到tal\_it\_callback.c中的响应函数？ （已有的函数地址替换成现在的） 调用 dut\_it\_callback\_set(self, set\_name = '设置DUT中断回调函数', fp = 'fp\_systick\_irq\_callback', f = 'SysTick\_IRQHandler\_Callback\_for\_Wwdt') 3. 单片机重启后首先进入 startup\_ht655x.s 中的 Reset\_Handler ， 之后进入 SystemInit , 然后main ，上位机直接获取下位机掉点前的数据如何做？：（ 全局变量） get\_history\_wwdt\_event(self , step = '获取所有WWDT事件信息并检查是否正确') 直接 data = self.memory\_read(data\_addr, sizeof(wwdt\_event))
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQxMjgxNDkyOV19
-->