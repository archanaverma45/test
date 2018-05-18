1. Introduction: We will understand few basics of OSGI
	- What is OSGI: We will find out what we can do by using osgi during our development.
	- Installation: Osgi brings many benefits when we deploy our application in such environment.
	- Code: We will write out osgi bundle
	- Features of osgi: Osgi promotes modularity. we will see how to structure our application in moduler way. we will see some good modular code practices.
	
2. What is osgi:
	- Bascically osgi is a runtime for applications which require to be deployed in the container and requires a life cycle inside the container. 
	- These applications are generaly modular applications.
	- As a developer osgi matters to me if i want to write code where i can version my functionally encapsulated code (jar), code requirements strictly enforcing by importing and exporting in/from my
		jar exactly what is needed at runtime, after the deployment of my code a life cycle management.
    - By having a life cycle management we get dynamic applications control in container.
	
    - Osgi implement these requirements by having three key aspects: bundles, life cycle and services.
	- Bundle:From developer perspective bundle is a JAR with additional information. So you encapsulat your functional code into single unit code called JAR and explicitly mention imported and exported packages in a file in jar
	   Just like java class we have to mention import package before using a class outside it.
	   - By writing this code we build jars that does one responsiblity in a clear defined manner.
	- Life cycle: When bundles are deployed in the osgi container, container provides them with lifecycle and by using this you can dynamically load unload start stop and update dynamically.
	  - Under the hood osgi runtime handles all this for us.
	- Services: When we write bundle we should think of them as writing services for other bundles or services. We call them microservices as they do one thing well. 
		These services are provided dynamically. When a service is started or stoped the wiring between them is managed by the osgi runtime.
		When we build and deploye our applications in this fashion, our application architecture keeps evovling after the initial deployment. 
		
3. OSGI for existing applications: 
	- Osgi works with application developed using modular programming principles. 
	- But we will see that it need very little or no code change to adapt our existing non-osgi jar to an osgi friendly bundle.
	- All it takes is to include some osgi specific headers in manifest file in jar. These headers indentifies the bundle, its version and imported/exported dependencies.
	
4. Installation: 
	- Apache Felix is the framework which implementation of osgi core. Eclipse equinox is another implementation of osgi core.
	- Apache karaf is a full osgi environment which provides out of the box experience to developers. Internally it uses felix as osgi core framework.
	- Few features of apache karaf are Hot Deployment, dynamic configuration, shell console, remote access etc.
	
	- BND tool (maven bundle plugin)
	- Its a tool which create manifest file for you with osgi headers as part of your packaging when done using apache maven build tool.
	- Blueprint: it is a dependency injection framework for osgi. it job is to handle wiring of java beans and its lifecycle.
	- Blueprit is expressed using xml where you defines how components are assembled together.
	- Pax Exam: Pax exam is used to test your osgi applications in osgi container. 
	- It launches osgi core framework, build bundles and inject them to container.
	
5. 
