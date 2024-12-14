# -
这是一个简易的手表项目
描述：此项目通过提供三个按键：确认、返回、下一个以实现功能选择。目前实现的功能有，温湿度检测：通过DHT11检测；游戏：目前只有一个谷歌小恐龙游戏，显示当前和最高分数、跳跃；播放器：可添加/切换歌曲，调节音量，显示播放音量，播放进度：设置选项：空；信息选项：空。
硬件：1个dht11模块、1个蜂鸣器、1个0.96寸OLED显示屏模块、2个LED灯、3个按键、1块stm32f103c8t6最小系统板。
软件：
1. 按键模块：构造按键对象，使用硬件定时器进行按键扫描，实现短按、长按等功能。
2. 输入模块：构造输入事件对象，输入设备对象。将按键作为输入设备，将按键输入转换为输入事件，并放入输入事件消息队列。
3. 菜单模块：构造结点对象，选项对象。结点对象类似双向链表：记录父结点、子结点、兄弟结点的位置，选项对象由结点对象派生。在添加菜单选项时，只需要确定结点的父结点,子结点,兄弟结点的位置，然后注册到选项管理器中。
4. 播放器模块：移植自RT-Thread文档中心的蜂鸣器播放器Demo。构造歌曲对象，音乐播放器对象，播放器包括一个解码器和一个音频设备：官方Demo中使用的是PWM设备，RT-Thread Nano中没有设备接口，构造Beep设备对象，以实现PWM的开关。需要提供根据曲谱得到的song_data，并将歌曲注册到播放器中。播放器线程负责将song_data放到解码器中解码，得到提供给PWM设备的周期和脉宽。
5. 游戏模块：目前只有谷歌小恐龙一个游戏。游戏模块构造了一个角色对象，以及使用迭代器模式，遍历所有的角色。
6.消息传输模块：采用订阅者发布者模式，由一个消息发布表和一个消息订阅者表以及一个消息处理线程构成。消息发布表记录当前话题以及话题消息，消息订阅者表记录当前消息的订阅者。任何发布者都可以发布任意消息，但是如需要读取消息，则需要订阅该消息。发布者发布消息后，订阅者会立刻进行相应处理，并将消息保存。类似于RT-Thread中的邮箱。
6. 业务模块：从输入事件消息队列中读取输入事件，并判断是哪个按键，以及是长按还是短按。使用访问者模式实现在不同功能下按下按键时要进行的操作，目前共有6个访问者：按键1短按、长按，按键2短按、长按，按键3短按、长按。3种元素：菜单，游戏，播放器。业务模块通过消息传输模块接受来自其他模块的信息，如菜单模块当前的选项，播放器模块的播放状态，RTC模块发送的时钟显示请求。
7.其他：通过硬件定时器进行屏幕刷新。