# Index

![[Pasted image 20231020153841.png]]
![[Pasted image 20231031151530.png]]

# Interface
## Kernel
### Scheduleable
#### Scheduleable
A class that defines the basic scheduling interface to the Scheduler.
constructor如下。需要传入一个handler作为callback对象，一个delay作为schedule延迟，一个phase和是否是unique event（是否重新schedule）
```cpp
Scheduleable(const SpartaHandler & consumer_event_handler,
		Clock::Cycle delay, SchedulingPhase sched_phase,
		bool is_unique_event = false);
```

schedule四种重载，最后都会调用scheduleRelativeTick。区别在于，如果没有指定delay，用delay_；如果没指定clock，用local_clk_
```cpp
void schedule(Clock::Cycle delay, const Clock *clk)

{
	sparta_assert(clk != nullptr);
	this->scheduleRelativeTick(clk->getTick(delay),
								clk->getScheduler());
}
```
scheduleRelativeTick会调用传入的schedular的schedule event，将this进行schedule
```cpp
virtual void scheduleRelativeTick(const Scheduler::Tick rel_tick,
	Scheduler * const scheduler)
{
	sparta_assert(scheduler != nullptr);
	scheduler->scheduleEvent(this, rel_tick, pgid_,
			continuing_, is_unique_event_);
}
```

reclaim函数主要由payload event调用

#### ScheduleableHandle
A light-weight reference counting handle for Scheduleables
connect()增加计数，disconnect减少计数，如果计数清空，则调用schelable对象的reclaim，类似引用计数。
```cpp
//! Disconnect
void disconnect_() {
	if(scheduleable_ && --scheduleable_->scheduleable_handle_count_ 
		== 0) 
	{
		scheduleable_->reclaim_();
	}
}
//! Connect
void connect_() {
	if(scheduleable_) {
		++scheduleable_->scheduleable_handle_count_;
	}
}
```
构造的时候connect，析构的时候disconnect。

### Scheduler
The sparta::Scheduler class simply schedules callback methods for some time in the future
继承的RootTreeNode
#### scheduleEvent/scheduleAsyncEvent
scheduleEvent可以由本线程调用：
```cpp
void scheduleEvent(Scheduleable * scheduleable,
		Tick rel_time,
		uint32_t dag_group=0,
		bool continuing=true,
		bool add_if_not_scheduled=false);
```
scheduleAsyncEvent可以由其他线程调用。具体原理是锁住一个list，加event，然后run的时候临时添加到time_quantum里
scheduleEvent中，会自动把Event排到下一个group里：
```cpp
const uint32_t firing_group =
		(dag_group != 0) ? dag_group + 1 : group_zero_;
```

#### TickQuantum
The internal structure the Scheduler uses to maintain event lists
一个TickQuantum代表一个tick，或者一个tick的一部分
里面有一个ScheduleableGroup，就是个std::vector<Scheduleable \*>数组
TickQuantum里管理了一个ScheduleableGroup的成员作为firing_group。每次加入event都按照firing_group作为index加入
```cpp
void addEvent(uint32_t firing_group, Scheduleable * scheduleable) {
	sparta_assert(firing_group > 0);
	sparta_assert(firing_group < groups.size());
	groups[firing_group].addScheduleable(scheduleable);
	first_group_idx = std::min(first_group_idx, firing_group);
}
```
有一个next指针指向后续的的TickQuantum，构成一个列表

在schedular中，有一个tick_quantum_allocator_来维护所有的tickquantum。在别人调用determineTickQuantum_时，会获得一个新的TickQuantum。
determineTickQuantum_的实现很简单，就是列表顺着找，找到了就是，没找到就插入一个新的。
determineTickQuantum_会在scheduleEvent中调用。

#### run
run可以开始跑events直到跑空或者跑到num_ticks。定时结束的方式是插入一个stop_event：`stop_event_(new Scheduleable(CREATE_SPARTA_HANDLER(Scheduler, stopRunning), 0, SchedulingPhase::Trigger))`
跑的时候是按照current_tick_quantum_里的跑，逐个firing group遍历，然后group内也遍历。跑完一个group就将该group clear掉，然后跑下一个
```cpp
while(current_group_firing_ < grp_cnt)
{
	TickQuantum::ScheduleableGroup & events 
		= quantum->groups[current_group_firing_]; // ref to this group
	// The design of this for loop is important to keep as is.
	// The events array can grow in size after firing the
	// current event.
	//
	// XXX To do this for loop backwards, have
	// current_group_firing_ = size() - 1 and count down to 0.
	// Once at zero, reset it to size and have it count down
	// to the _previous_ size(). If neither have changed, the
	// loop will exit.
	for(current_event_firing_ = 0;
		current_event_firing_ < events.size();
		++current_event_firing_)
	{
		const Scheduleable * sched = events[current_event_firing_];
		current_scheduling_phase_ 
			= sched->getSchedulingPhase();//update phase
		// some debug code, omit it.
		if(SPARTA_EXPECT_FALSE(call_trace_logger_)) {
			call_trace_stream_ << sched->getLabel() << " ";
		}
		sched->getHandler()(); // callback the handler
		++events_fired_;
	}
	events.clear();
	++current_group_firing_;
}
```
一个quantum跑完会跑下一个，并free掉空间
### DAG
DAG里包含很多vertex和edge，是一个图。其目的是分析event间依赖关系，分成不同的group，确保各个group之间无依赖关系。
#### sort()
sort函数会对其中所有vertex进行遍历，区分其依赖性，然后添加group id
首先是初始化，reset会把所有的groupid都设成1（最小），然后把入度为0的点放入遍历的list里
```cpp
for (auto & vi : alloc_vertices_) {
	vi->reset();
	// If this vertex has no producers (sources, i.e. nothing
	// coming into it), add it to the zlist
	if (vi->degreeZero()) {
		zlist.push_back(vi);
	}
}
```
其中reset会把所有的
```cpp
void reset()
{
	sorted_num_inbound_edges_ = num_inbound_edges_;
	// sorting_edges_ = outbound_edge_map_;
	setGroupID(1);
	resetMarker();
}
```





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

### PipelineCollector
必须用PipelineCollector::getPipelineCollector()->init(...)初始化
在结束程序前需要用destroy()
#### CollectablesByClock
在一个phase创建一个unique event,delay是1，存放在ev_collect_里。event里是performCollection的handler
enable的时候会在下一拍schedule ev_collect_，并把传入collectableTN加到enabled_ctns_里：
disable会从enabled_ctns_里删掉传入的collectableTN

performCollection()：
```cpp
void performCollection() {
	for(auto & ctn : enabled_ctns_) {
		if(ctn->isCollected()) {
			ctn->collect();
		}
	}
	if(!enabled_ctns_.empty()) {
		ev_collect_->schedule(); // ? 应该也是到下一拍
	}
}
```

#### HeartBeat机制
ev_heartbeat_ 是一个UniqueEvent`UniqueEvent<SchedulingPhase::PostTick> ev_heartbeat_;`,其phase是posttick
其包含了一个这个collector的performHeartBeat_handler:`ev_heartbeat_(&collector_events_, Collector::getName() + "_heartbeat_event", CREATE_SPARTA_HANDLER(PipelineCollector, performHeartBeat_), 0)` 
heartbeatgop在构造的时候被移到了post_tick_gop之后，确保收尾
```cpp
//...
DAG* dag = scheduler_->getDAG(); // Get a handle to the DAG
sparta_assert(dag);
Vertex* post_tick_gop = dag->getGOPoint("PostTick"); // Get a handle to the PostTick GOP
sparta_assert(post_tick_gop);
dag->unlink(ev_heartbeat_.getVertex(), post_tick_gop); // Undo the ev_heartbeat_ >> heartbeat_gop link
post_tick_gop->precedes(ev_heartbeat_); // Set post_tick_gop >> heartbeat_gop 
//...
```
performHeartBeat_():
```cpp
void performHeartBeat_()
{
	if(collection_active_) {
	// Close all transactions
		for(auto & ctn : registered_collectables_) {
			if(ctn->isCollected()) {
				ctn->restartRecord();
			}
		}
		// write an index
		writer_->writeIndex();
		// Remember the last time we recorded a heartbeat
		last_heartbeat_ = scheduler_->getCurrentTick();
		// Schedule another heartbeat
		ev_heartbeat_.schedule(heartbeat_interval_);
	}
}
```


#### startCollection/stopCollection
从某个TN开始collect`void startCollection(sparta::TreeNode* starting_node)`
递归地collect
```cpp
// ....
std::function<void (sparta::TreeNode* starting_node)> recursiveCollect;
recursiveCollect = [&recursiveCollect, this] (sparta::TreeNode* starting_node)
	{
	// First turn on this node if it's actually a CollectableTreeNode
		CollectableTreeNode* c_node = dynamic_cast<CollectableTreeNode*>(starting_node);
		if(c_node != nullptr) {
			c_node->startCollecting(this);
			registered_collectables_.insert(c_node);
		}
		// Recursive step. Go through the children and turn them on as well.
		for(sparta::TreeNode* node : sparta::TreeNodePrivateAttorney::getAllChildren(starting_node))
		{
			recursiveCollect(node);
		}
	};
recursiveCollect(starting_node);
//...
```

stopCollection也是从开始的点结束。类似start的实现
#### addToAutoCollection/removeFromAutoCollection
通过clock_ctn_map_ pair来enable disable collection
```cpp
std::map<const sparta::Clock *,
	std::array<std::unique_ptr<CollectablesByClock>,
		sparta::NUM_SCHEDULING_PHASES>> clock_ctn_map_;
```

### IterableCollector
A collector of any iterable type (std::vector, std::list, sparta::Buffer, etc)




## pipeview
### transaction_t
操作的基类

### Outputter
管理输出的工具，writeTransaction可以写record file。

### ClockfileWriter
输出clk信息，recursWriteClock_可以递归输出

### LocationFileWriter
写location信息

### InformationWriter
A class that allows the simulation developer to write data to an information file stored near pipeline collection output files about the simulation.

## log
### NotificationSource
#### NotificationSourceBase
##### ObservationStateChange
ObservationStateChange是一个enum，可以注册一个ObservationStateChange type的callback，
用registerObservationStateChangeCallback_和deregisterObservationStateChangeCallback_注册和取消注册ObservationStateChangeCallback
在调用`void invokeObservationStateChangeCallbacks_(ObservationStateChange to_call)`时会根据to_call来callback对应type的ObservationStateChangeCallback
##### obs_nodes_
所有observe的node，可以通过`notificationObserverAdded_`和`notificationObserverRemoved_`添加或移除
##### dels_
delegate的vec，delegate类似Spartahandler，在添加ob对象的时候会添加这个cb。

#### NotificationSource
##### registerForThis
调用TreeNode的registerForNotification
```cpp
template <typename T, void (T::*TMethod)(const TreeNode&, const TreeNode&, const_data_type&)>
void registerForThis(T* obj) {
	sparta::TreeNode::registerForNotification<data_type, T, TMethod>(obj, *noti_id_);
}
```
registerForNotification的实现：
```cpp
template <typename DataT, typename T, void (T::*TMethod)(const TreeNode&, const TreeNode&, const DataT&)>
void registerForNotification_(T* obj, const std::string& name, bool ensure_possible=true, bool allow_private=false)
{
	(void)allow_private;
	const std::type_info& data_type = typeid(DataT);
	if(true == ensure_possible && false == canSubtreeGenerateNotification(data_type, name)){
		// throw a exception
	}
	DelegateVector& observers = obs_local_[data_type]; // Create notification map
	
	if(findDelegate_<DataT, T, TMethod>(observers, obj, name) != observers.end()){
		// throw a exception
	}
	delegate d = delegate::from_method<DataT, T, TMethod>(obj, *this, name);
	observers.push_back(std::move(d));
	// Let children know
	broadcastRegistrationForNotificationListStringToChildren_(typeid(DataT), name, this, &observers.back(), allow_private);
}
```
其中obs_local_是一个<type_info, DelegateVector>的map：`typedef std::map<type_info_container, DelegateVector> NotificationObserverMap;`
在`TreeNode::invokeDelegates_`中可以被invoke
在`TreeNode::propagateNotification_`中`TreeNode::invokeDelegates_`会被调用，起到传播notification的作用

### Destination/DestinationManager
#### Destination
Generic Logging destination stream interface which writes sparta::log::Message structures to some output (file)stream.
members：
```cpp
uint64_t num_msgs_received_; //!< Total messages received
uint64_t num_msgs_written_; //!< Total messages written to the destination (received - duplicates)
uint64_t num_msg_duplicates_; //!< Total number of messages which were already written
std::map<thread_id_type, seq_num_type> last_seq_map_; //!< Mapping of thread IDs to latest sequence IDs
std::mutex write_mutex_; //!< Mutex for writing and checking/setting sequence numbers within this destination onl
```
#### Formatter
File writer formatting interface
内涵一个ostream：`std::ostream& stream_;`
用write来写message，writeHeader写simulationInfo
有几个继承Formatter的实现。没什么差别，多写一点少写一点的差别
- VerboseFormatter：
- DefaultFormatter：
- BasicFormatter：
- RawFormatter：
#### DestinationInstance
```cpp
template <typename DestType>
class DestinationInstance : public Destination
{
};
```
有多个实例，分别对应了不同的destination：
- DestinationInstance<std::ostream>
- DestinationInstance<std::string>: filename,直接输出到文件

#### DestinationManager
用来管理Desination，防止重复打开。
有DestinationVector：`typedef std::vector<std::unique_ptr<Destination>> DestinationVector;`
有一个static成员管理所有的destination`static DestinationVector dests_;`、
调用getDestination()可以不重复地获得destination对象



### Tap
Attach to a TreeNode to intercept logging messages from any NotificationSource nodes in the subtree of that node. 拦截器 窃听器
有一个指向监听node的weakptr `TreeNode::WeakPtr node_wptr_;`
内部还有一个Destination指针指向一个DestinationInstance
```cpp
Destination* d = DestinationManager::getDestination(dest); // Can instantiate new
sparta_assert(d != nullptr);
dest_ = d;
```



## trigger
### Trigerable
base class of trigger
```cpp
class Triggerable
{
public:
	//! Virtually destroy
	virtual ~Triggerable(){}
	//! The method called when the trigger fires a turn on.
	virtual void go() {}
	//! The method called when a trigger fires a turn off.
	virtual void stop() {}
	//! The method to call on periodic repeats of the trigger.
	virtual void repeat() {}
	//! Has been triggered?
	bool isTriggered() const { return triggered_; }
protected:
	bool triggered_ = false;
};
```

### SingleTrigger
This method accepts a SpartaHandler to use as a callback to fire when requested.
```cpp
SingleTrigger(const std::string& name, const SpartaHandler & callback) :
	callback_(callback),
	name_(name),
	has_fired_(false)
{;}
```
==和uniqueevent有啥区别==
几种继承：
- CounterTrigger：超过一个数fire
- cycleTrigger：几个cycle后fire。还是通过插入event的方法
- TimerTrigger：一定simulation time后trigger。通过scheduleRelativeTick方法

### Trigger
A class that can be used to set scheduled callback events, such as an event to trigger logging or pipeline collection on the scheduler.
有一个SingleTrigger数组:`std::array<std::unique_ptr<SingleTrigger>, 3> triggers_;`，3的原因是因为有一个enum代表三种trigger状态：```
```cpp
enum SubTriggers {
	START,
	STOP,
	REPEAT
};
```
初始化的时候会设置两个CycleTrigger:
```cpp
Trigger(const std::string& name, const Clock * clk = nullptr) :
	name_(name)
{
// No initial start trigger
	triggers_[STOP] .reset(new CycleTrigger(name, CREATE_SPARTA_HANDLER(Trigger, onStopTrigger_), clk));
	triggers_[REPEAT].reset(new CycleTrigger(name, CREATE_SPARTA_HANDLER(Trigger, onRepeatTrigger_), clk));
}
```
