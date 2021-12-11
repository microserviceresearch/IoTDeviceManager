# IoT Device Manager

This repository contains a Java library that complements an infrastructure to execute IoT-enhanced business proccess (BP) in order to support an interdisciplinary development. Information abot the extended infrastructure can be found in the following Github repository: [iot-enhanced-business-process-infrastructure](https://github.com/microserviceresearch/iot-enhanced-business-process-infrastructure).

According to this architecture, business microservices are in charge of managing IoT devices and publish low-level context data in an Event bus. A Context Monitor is in charge of analyzing this data and apply SPARQL rules in order to indentify and inject high-level events into a BP Controller microservice that is responsible of executing a BPMN model. 

This Java library extends the functionality of business microservices in order to register in a Service Registry (e.g., Eureka) semantic metadata about context information published by IoT devices. This metadata can be used by [CEP experts](https://github.com/microserviceresearch/iot-enhanced-business-process-infrastructure) in order to infer high-level events of low-level context data. 

# Creating an IoT Device Manager

To create an IoT Device Manager you can use Gradle to build the corresponding project in this repository and include it as a dependency of a Spring Boot Application. Then, you just need to annotate the main class with the ```@IoTDeviceManager``` as presented bellow. This annotation must be configured with the name of the IoT device that is managed. Note also that the ```@SpringBootApplication``` annotation must be included to create the Spring Boot main class. In addition the ```@EnableDiscoveryClient``` annotation is used to register the microservice in Eureka.

```java
@EnableDiscoveryClient
@SpringBootApplication
@IoTDeviceManager(name="Container Detector")
public class ContainerDetectorManager {
  public static void main(String[] args) {
    SpringApplication.run(ContainerDetectorManager.class, args);
  } 
}
```
```java
@EnableDiscoveryClient
@SpringBootApplication
@IoTDeviceManager(name="Articulated Robot")
public class ArticulatedRobotManager {
  public static void main(String[] args) {
    SpringApplication.run(ArticulatedRobotManager.class, args);
  } 
}
```
Next, you have two options. If the IoT device plays the role of an actuator, you must create a Java class with the ```@RestController``` annotation to define its REST API. Its methods must be annotated with ```@RequestMapping``` to define the end points of the API as well as the ```@Actuation``` annotation to define the semantic information:

```java
@RestController
public class Articulated RobotAPI {

	 @Actuation (name="Store pallet in the refrigerator room",
	 	     resultType=MovementResult.class,
	  	     description="Moves a pallet to the refrigerator"
		    )
   	 @RequestMapping (value="/movement/pallet/refrigeratorrom",
          		  method=RequestMethod.GET,
          		  produces="application/json"
          )       
	 public String movePalletRefrigeratorRoom(){
		MovementResult result;
      		// Code to interact with the IoT device
      		return result;
	}
	
}
```
If the IoT device plays the role of a sensor, you must create a Java class with the ```@Sensor``` annotation and define the semantic information of the generated context data with the ```@Observation``` annotation:

```java
@Sensor
public class ContainerDetectionSensor {

	@Observation (name="Check Container Location",
		      property="location",
		      resultType=ContainerLocation.class,
		      description="Returns de location of a Container trough its proximity to beacons"
		     )
	public ContainerLocation getContainerLocation(){
		ContainerLocation result;
     	 	// Code to receive data from the sensor
      		return result;
	}
    
}
```
