 Using Beans with Camel:
 - Historycally software frameworks such as corba, ejb required you to model your classes and packages in some sort of certain way to be able to use these frameworks.
 - This is called restrictive programming model as they restrict you to design your application in certain way. 
 - With growing complexity in software application there was a need for a simpler programming model call POJO 
 - Camel knows the power of this simple programming model and supports it a lot. 
 - Because by using beans you reduce coupling in your applicaton. 
 - So you get benefits of reduce coupling as well as get loose coupling with camel routes. 
 
 - The service activator pattern:
	- The service activator pattern is an EIP which says there should be a mediation engine that invokes service (POJO ) based on the incoming request and returns the reponse.
	- Bean component in camel is the implementation of this pattern because it calls the pojo based on the incoming messge from the route.
	- Flow diagram depcting the SAP.
	- Notice that at compile time there is no direct mapping for bean and its methods. Camel has to figure it out at runtime.
	- Camel has to choose the method because even if you specify the method name(sometimes you even dont) it can be overloaded.
	- If we invoke bean in processor than our code become tedious to understand, we should leverage camel bean calling feature by writing our methods in a way that makes it easy
		for camel to invoke it runtime. Show an example with bean invoked from processor vs direct bean invocation from route.
	- Method selection algorithm: 1. If camelMethodName header present (yes=>2), no -> method name given(yes->2), no ->2
		2. If more than 1 memthod present check for @Handler annotated 
			Or some other camel annotations like @Body, @Header 
		2.1 If only one method with one parameter 
		2.2 Otherwise find best method
	- Potential Problems in method selection: If specific method not then camel throws MethodNotFoundException
		- If camel cant find best match method then camel throws AmbigousMethodCallException (when 2 methods with same String param but no method name specified)
		- when camel can not convert body type to the required by the method it will throw NoTypeConversionAvailableException
		
	- Bean parameter binding: How camel adapts to the method parameters
	- When bean has only one parameter in method that method is expected to be method body
	- When bean have multiple parameter first one should be body or explicitly @Body in case other position. 
	- Second onwards it should be either Camel builtin types (Exchange, Message, CamelContext, TypeConverter etc) or annotated with camel annotations (eg @Body, @Header, @Headers (Map) etc)
	- exampels: public String (String body, CamelContext context, @Header("customerId") String custId)
				public String (@Headers Map headers, @Body String body)
        
        
        6. Testing with camel: Testing is needed to ensure if your application is working when everything goes well as well as if you are handling error and negative scenario in your application.
	- A good way to test camel application is to start the route, send the message to the route and assert if the route output is as expcted or not.
	- Camel Test Kit: camel includes a test kit which allows to you write test cases in a familier way in junit.
	- Camel-test library has 6 junit extension classees which you can use for testing your route (3 for junit 3.x and 4.x each)
	- TestSupport (with additinal assertions), CamelTestSupport(base class to test route), CamelSpringTestSupport(in case you define your routes in spring dsl)
	- CamelSpringTestSupport is used when you define your route in spring context file.
	
 5. Error Handling:
	- Error handling should not be an afterthought but a key thing to think about right from the start of your design.
	- Ing single system you have much more control where you can recover from errors easily. It becomes more challenging when you integrate with remote systems as it brings more risk of unexpected events
	- Problem in network, remote system not responding in time or fail. Even on local your server might run out of memory. You should be able to handle them gracefully.
	- Camel errors in recoverable and irrecoverable. Irrecoverable error is an error that remains ane error no matter how many times you try to perform the same operation again. eg. rying to access a adatabase table
		taht doesn't exist.
	-A recoverable error is a temporay error that might not come back on next try of the same operation. Eg netowrk connection that can result in IOException.
	- In came recoverable error is Throwable or Exception. we can get it from Exchange. getException and exchange.setException(Throwable)
	- Irrecoverable error is represented as a flag in message. Message msg = Exchange.getOut(); message.etFault(true). message.setBody("Unknown status");
	- It was designed like this because whenever an error happens in camel route during exchange, camel changes it and set in exchange so that we can try to recover from it.
	- After catching the exception camel will try to retry, propagate error back to caller or do other things as we want and asked camel to do.

	Where camel error handling applies: It doesnt apply everywhere. Its applicable only during the lifecycle of exchange. If anything outside of that happens then its responsiblity of component.
	
	- Error handlers in camel: We know that camel treats every exception as recoverable and store them in the exchange (by calling setException).
		- Camel error handlers will only come into picture in case of exceptions. It will not come into picture for irrecoverable errors set by 
			setFault(true)
		- Default Error Hanlder: This is automatically enabled and this default.
		- Dead Letter channel: This implements Dead Letter channel EIP
		- Transaction Error Handler: 
		- To understand camel error handling we need to understand the channel's role in a camel route graph.
			Consumer ---> Channel-1 ---> Processor ---> Chanel-2 ---> Processor
			As seen in every camel route a channel sits between every node.
			The channel allows camel to monitor and control the route by having error handling, intercepting and much more. 
			When en exception is thrown by any Processor in this route graph then error handler will catch it and react as per our 
			configuraion eg. redelivery or route to another path etc.
			In case no error handler specified by default camel will propagate error to the caller. 
			For eg. if route starts with file component then file component will choose how to react on it eg log error, rollback file etc.
		- Dead Letter channel error handler:It enables us to move the failed messages to a dedicated error queue known as dead letter channel
		- Unlike default error handler, dead letter channel will by default move the failed messages to the queue
		- You can move original message to queue if exception happens after enriching the original message.
		
	-Feature of Error Handler: Redelivery Policies, Scop(context or route), Exception Policies(exception specific logic), error handling(further processing)
	-Using error handler with redelivery: When we communicate with external systems over network then this communication can not reliable. 
		This might break temporarily because of many reasons (network failure, remote system restart etc)
	- Camel helps us to recover from this kind of temporary problems by offering redelivery options as part of its error handling mechanis.
	-Using redelivery: Camel offers several configuration to tune the redelivery. eg, MaximumRedeliveries, RedeliveryDelay, AsyncDelayedRedelivery etc
	-Using asynchronous delayed redelivery: In previus example we saw that same thread tries to redeliver the failed message. This blocks the thread.
		and if there are further messages to processed then they have to wait. 
		- In case of async redelivery the thread is not blocked but a new thread is used to process another message. This improves the scalability of our	application. 
		
	- Error handlers and scopes: You can define an error handler which is applicable for camel context level as well as error handler for a
		route specific as well.
		
	- Using Exception policies: Exception policies define the perticular handling of one or more specific exception which may change from one exception	
		to other. Here also we can define redelivery etc. We use onException() fluent API to define them.
	- When any exception occures during route execution, the camel will do a big instance of test starting with the thrown exception and up towards the
		its super hierarchy until it finds the instance of success.
	- If retries are configured on both errorHandler and onException level then onException will overrride it.
			onException(XPathException.class, TransformerException.class).to("log:xml?level=WARN");
			onException(IOException.class, SQLException.class, JMSException.class).maximumRedeliveries(5).redeliveryDelay(3000);
