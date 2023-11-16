# Index

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
### ExpressionNode
Expression里的item
toperation_t定义操作类型，加减乘除
==没看懂compression==
compression的支持是递归的，如果子节点都支持compression且本节点为加减乘，则支持compression

有八个东西继承ExpressionNode：
![[Pasted image 20231115210155.png]]


### ExpressionNodeType
五种继承于ExpressionNode的子类
#### Operation
内部有一个或者多个operand(ExpressionNode*) 初始化时设置type,然后使用evaluate_()获得值
getStats_会调用并累加总和
```cpp
virtual uint32_t getStats_(std::vector<const StatisticInstance*>& results) const override {
	uint32_t added = 0;
	for(auto& op : operands_){
		added += op->getStats(results);
	}
	return added;
}
```
#### Contant
放data
#### UnaryFunction/BinaryFunction/TernaryFunction
包装了一个`fxn_t = RetT (* const)(ArgT)`指针（以UnaryFunction为例），evaluate的时候会调用这个函数，类似于自定义了一个较为复杂的operation


### ExpressionNodeVariables
里面有三种继承于ExpressionNode的类
#### StatVariable
内有一个SI
getStats_会有实际作用，会向列表内push进新的SI并返回1（代表推入了1）
```cpp
virtual uint32_t getStats_(std::vector<const StatisticInstance*>& results) const override {
	results.push_back(&stat_);
	return 1;
}
```

#### SimVariable
simulation中用到的变量，存放那些可能在仿真中改变的值，但在一个expression的lifetime是const。
创建的时候传入一个返回double的函数指针
```cpp
typedef double (*getter_t)();
// ...
/*!
* \brief Construct with a getter function
* \param which Name of the variable
* \param getter Pointer to function for getting the variable as a double
*/
SimVariable(const std::string& which, const getter_t getter) :
	which_(which),
	getter_(getter)
{ }
```
#### ReferenceVariable
和SimVariable，只是这个的值是变量，构造时传入的是一个引用


### Expression
一个ExpressionNode的Proxy，`std::unique_ptr<ExpressionNode> content_;`。类里只有这么一个私有成员
可以有很多构造方式，比如string，比如函数。
还支持更多运算符，可以直接往后加
#### parse_()
parse_函数会根据一个string来返回一个expression，其中会用到ExpressionParser
#### getStats() / getStats_()
Gets the statistics present in this expression
==我实在搞不懂statistics是个啥==


### ExpressionParser
里面包裹了一个ExpressionGrammar，
```cpp
ExpressionParser(TreeNode* n,
	std::vector<const TreeNode*>& already_used) :
	grammar_(n, already_used)
{ }
```
实现了一个parse，~~~~具体咋实现的我不管了，~~总之传入的如果parse碰到already_used中node后会throw exp
### ExpressionGrammar
用到了boost::spirit::qi，~~懒得继续深入看了~~

### StatisticDef
==deregisterAggregationFcnUponDestruction_()没懂==
存放着 prebuilt_expr_ 和 expr_str_ 。前者是一个Expression,后者是可以转换成Expression的string
realizeExpression可以获得StatisticDef对应的Expression。优先使用prebuilt_expr_，如果没有再用expr_str_和used vec build一个Expression
```cpp
sparta::statistics::expression::Expression
realizeExpression(std::vector<const TreeNode*>& used) const {
	if(prebuilt_expr_){
		return *prebuilt_expr_; // Returns a copy
	}
	// This is deferred until this point because the expression can
	// contain variables populated using the 'used' vector
	return sparta::statistics::expression::Expression(expr_str_, context_, used);
}
```
### StatisticInstance
Instance of either a StatisticDef or CounterBase or an Expression
隐藏对这三者的区分，对三者统一操作
以下是其默认构造函数
```cpp
StatisticInstance(const StatisticDef* sd,
	const CounterBase* ctr,
	const ParameterBase* par,
	const TreeNode* n,
	std::vector<const TreeNode*>* used) :
	StatisticInstance()
{
	const StatisticDef* stat_def;
	if(!sd){
		stat_def = dynamic_cast<const StatisticDef*>(n);
		sdef_ = stat_def;
	}else{
		sdef_ = stat_def = sd;
	}
	const CounterBase* counter;
	if(!ctr){
		counter = dynamic_cast<const CounterBase*>(n);
		ctr_ = counter;
	}else{
		ctr_ = counter = ctr;
	}
	const ParameterBase* param;
	if(!par){
		param = dynamic_cast<const ParameterBase*>(n);
		par_ = param;
	}else{
		par_ = param = par;
	}
	
	// Find the non-null argument
	const TreeNode* node = n;
	if(!node){
		node = sd;
		if(!node){
			node = ctr;
			if(!node){
				node = par;
				sparta_assert(node,
				"StatisticInstance was constructed with all null arguments. "
				"This is not allowed");
			}
		}
	}
	// ...
	if(sdef_){
		node_ref_ = stat_def->getWeakPtr();
		//..
	}
	}else if(ctr_){
		node_ref_ = counter->getWeakPtr();
	}else if(par_){
		node_ref_ = param->getWeakPtr();
	}else{
		// Should not have been able to call constructor without 1 or
		// the 3 args being non-null
		throw SpartaException("Cannot instantiate a StatisticInstance without a statistic "
			"definition or counter pointer");
	}
	start();
	sparta_assert(false == node_ref_.expired());
}
```
SI可以有一下几种构造方式
- Expression左值
- 已有的counter或者StatisticDef
	- 直接传入TN
	- TN+used

#### accumulateStatistic
==accumulateStatistic 没懂==
accumulateStatistic会递归地调用stat_expr_.getStats()
```cpp
void accumulateStatistic() const {
	initial_.setIsCumulative(true);
	std::vector<const StatisticInstance*> stats_in_expr;
	stat_expr_.getStats(stats_in_expr);// 当前stat_expr_调用，会填充stats_in_expr
	for (const auto & stat : stats_in_expr) {// 递归调用
		stat->accumulateStatistic();
	}
}
```


如果si是statistickDef，stat_expr_是其代表的exp；如果si是expression初始化的，则是expression

#### start() / end() / computeValue_() / getValue()
start开始统计 end结束统计
computeValue计算统计数据
getValue 获得值

### Histogram
#### HistogramBase
三个关键值：
- lower_val：直方图横轴min，低于min的进入underflow bin
- upper_val：直方图横轴max，高于max的进入overflow bin
- num_vals_per_bin：每个bin里的值
直方的数量： number_of_bins = (upper_limit - lower_limit) / values_per_bin + 1
每个bin都是一个counter `std::vector<sparta::Counter> bin_; //!< Regular bins`
用`initializeStats_()`挂在一个StatisticSet下。
underflow bin和overflow bin被挂在StatisticSet下，其余的放在histogram里
#### HistogramStandalone / HistogramTreeNode
前者不作为一个treenode，需要传入一个挂靠的StatisticSet；后者是一个tn，直接挂在parent上，自己作为自己的StatisticSet
HistogramTreeNode的别名是Histogram、`typedef HistogramTreeNode Histogram;`

## collection
### Collector / CollectableTreeNode
Collector提供了基类。
CollectableTreeNode提供了多个函数接口

## pipeview
### 