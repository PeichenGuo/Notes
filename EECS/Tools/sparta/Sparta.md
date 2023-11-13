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