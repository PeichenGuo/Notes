![[Pasted image 20231020153841.png]]
![[Pasted image 20231031151530.png]]

# Interface
## Port
### DataPort
#### Dataport 数据传递方式
当一个DataOut需要向DataIn传递数据时，需要调用DataOut::send。DataOut::send会调用DataIn的send_
```cpp
void send(const DataT & dat, sparta::Clock::Cycle rel_time = 0)
{
	// ....
	for(DataInPort<DataT>* itr : bound_in_ports_) {
		itr->send_(dat, rel_time); 
	}
}
```
DataIn::send_会调用user_payload_delivery_的preparePayload来获取一个ScheduleableHandle，这个handle实际上是一个PayloadDeliveringProxy，这个proxy是用来帮助传达信息的。随后会schedule一个PhasedPayloadEvent
```cpp
void send_(const DataT & dat, sparta::Clock::Cycle rel_time)

{

	const uint32_t total_delay = rel_time + port_delay_;
	// .....
	user_payload_delivery_->preparePayload(dat)->schedule(total_delay, receiver_clock_);

}
```
user_payload_delivery_是在InPort创建时创立的，将receivePortData_绑定到了PhasedPayloadEvent上，相当于上面的schedule，schedule的是datain的receivePortData_函数，其参数是传入的payload
```cpp
DataInPort(TreeNode* portset, const std::string & name,
	sparta::SchedulingPhase delivery_phase, sparta::Clock::Cycle delay) :
	InPort(portset, name, delivery_phase),
	DataContainer<DataT>(getClock()),
	data_in_port_events_(this),
	port_delay_(delay)
{
// ....
	user_payload_delivery_.reset(new PhasedPayloadEvent<DataT>(&data_in_port_events_, name + "_forward_event", delivery_phase, CREATE_SPARTA_HANDLER_WITH_DATA(DataInPort<DataT>, receivePortData_, DataT)));
}
```
receivePortData_则是调用了explicit_consumer_handler_是inport绑定的comsumer（这里inport相当于producer)
```cpp
void receivePortData_(const DataT & dat)
{
	DataContainer<DataT>::setData_(dat);// 存储data
	
	if(SPARTA_EXPECT_TRUE(explicit_consumer_handler_)) {
		explicit_consumer_handler_((const void*)&dat);
	}
	//...
}
```
explicit_consumer_handler_是inport里的对象：相当于一个inport要把data给consumer。在初始化inport的时候，要设置consumer
```cpp
void registerConsumerHandler(const SpartaHandler & handler)

{
	// ....
	explicit_consumer_handler_ = handler;
	// Let subclasses check out the handler...
	registerConsumerHandler_(handler);
}
```
在设置handler后，会调用registerConsumerHandler_，这个是由每个子类自己实现的。dataport里在设置handler名称
```cpp
void registerConsumerHandler_(const SpartaHandler & handler) override final
{
	// .....
	handler_name_ = getName() + "<DataInPort>[" + handler.getName() + "]";// 
	user_payload_delivery_ -> getScheduleable().setLabel(handler_name_.c_str());
}
```
### SyncPort
这个port有两个作用：
- valid-ready握手
- 不同clk field间通信
valid-ready握手的原理是，outport能从inport那里读到一个getReady()函数来看是否握上手
而inport在不ready时遇到了data会reschedule到下一拍重发

### SignalPort
区别是用的PhasedUniqueEvent

### ExportedPort
![[Pasted image 20231114112027.png]]
解决这个问题，简而言之很多顶层的接口都是submodule的forwarding，可以直接用一个假接口来转发，避免开销。下面是exportport的构造函数，可以看到，单纯把一个Port绑定到exportedport上
```cpp
ExportedPort(sparta::TreeNode * portset,
	const std::string & exported_port_name,
	sparta::Port * internal_port) :
	Port(portset, sparta::notNull(internal_port)->getDirection(), 
		exported_port_name),
	internal_port_(internal_port),
	internal_port_name_(internal_port->getName())
{}
```

## functional
### ArchDataSegment
字面意思，就是ArchData的片段，不存储数据，只负责管理layout。layout（）函数会根据设置的segment或者一个size来给size_赋值。
内部有一个ArchData的指针，指向拥有这个seg的ArchData

### ArchData
管理一片内存的结构
#### Line
一个方便赋值取用的结构,负责存储数据。
Line的边际由layout决定：
```cpp
Line& getLine(offset_type offset) {
	sparta_assert(containsAddress(offset),
		"Cannot access this ArchData at offset: 0x"
		<< std::hex << offset << " ArchData size= "
		<< size_ << " B.");
//...
}
// ...
bool containsAddress(offset_type offset) const noexcept {
	return offset < size_;
}
```
#### layout()
这个函数是用来把散乱的segments排列好的。
registerSegment可以注册新的segment，把所有需要的seg注册之后使用layout排列
layout会递归地按照注册顺序紧密排列seg

### DataView
用来看data的。
在place时会绑定一个line到line_
```cpp
virtual void place_(offset_type offset) override {
	adata_->checkSegment(offset, getSize());
	line_ = &adata_->getLine(offset);
	offset_ = offset - line_->getOffset(); // Store locally for faster 'read' calls
}
```
这之后dataview的操作实际上是对这个line的操作。
#### ~~Unsafe问题~~
==这里有问题，忽略==
safe和unsafe的区别就是查一下读的内容是不是超过了这个dataview的size
```cpp
template <typename T, ByteOrder BO=LE>
T readPadded(index_type idx=0) const {
	sparta_assert(sizeof(T) * ((uint64_t)idx) <= getSize(),
		
		"readPadded index " << idx << " and type " << 
			demangle(typeid(T).name())
		<< " (size " << sizeof(T) << ") is invalid for this DataView of size " 
		<< getSize());
	return readPaddedUnsafe<T, BO>(idx);
}
```

### RegisterBits
Register中存data的数据结构

### Register
#### Field
reg里的field，方便访问
通过Definition struct创建：
```cpp
Definition(const char* _name,
		const char* _desc,
		size_type _low_bit,
		size_type _high_bit,
		bool _read_only) :
	name(_name),
	desc(_desc),
	low_bit(_low_bit),
	high_bit(_high_bit),
	read_only(_read_only)
{;}
```

#### Poke / Peek
可以绕开notification的write
#### read/write callback
需要注册一个callback，值得注意的事，这个callback不是spartahandler，采用的是其他函数指针，因为要有return值（必然）
```cpp
typedef std::function<sparta::utils::ValidValue<uint64_t>(RegisterBase*)> register_read_callback_type;
```

### RegisterSet
#### RegisterProxy
用来隐藏需要switching dynamically between bank的register的访问，如果不需要bank switch可以不用proxy

## statistics
### InstrumentationNode
Visibility
Class
Type
### CounterBase
CounterBehavioru有三个
- COUNT_NORMAL。 正常累加
- COUNT_INTEGRAL。 跟踪某个整数累加。比如某个queue的值，如果在5cycle里是3，6，1，1，1，那么在5cycle里就分别累加3，6，1，1，1
- COUNT_LATEST。可set可clear。类似一个寄存器，而非counter
- 
### Counter
==我没看出COUNT_NORMAL和COUNT_INTEGRAL的区别==

#### CycleCounter
completely passive and not checkpointable
用来数cycle的。startCounting()开始，stopCounting()结束

### Expression
#### ExpressionNode
toperation_t定义操作类型，加减乘除
==没看懂compression==
compression的支持是递归的，如果子节点都支持compression且本节点为加减乘，则支持compression
#### ExpressionNodeType
##### Operation
内部有一个或者多个operand(ExpressionNode*) 初始化时设置type,然后使用evaluate_()获得值
##### Contant
放数
##### UnaryFunction/BinaryFunction/TernaryFunction
包装了一个`fxn_t = RetT (* const)(ArgT)`指针（以UnaryFunction为例），evaluate的时候会调用这个函数，类似于自定义了一个较为复杂的operation
#### Expression
一个ExpressionNode Tree的Proxy，`std::unique_ptr<ExpressionNode> content_;`
可以有很多构造方式，比如string，比如函数。
还支持更多运算符，可以直接往后加

### StatisticDef
