### Abstract ABC vs Protocol and interface segregation

What is a Protocol class since Python 3.8 and when is it advantageous to us this? Interface segregation is part of the SOLID design principals proposed by Robert Martin, and this particular option is enforced by with what is called duck typing. If it walks like a duck and quacks like a duck...

### Abstract Classes

First of all both are designed to help a developer build better software. In that process we also use IDE's to help achieve this with a typechecking tool. In VScode we can see when we comment out an entire abstract method inherited from an ABC- say for example in a subclass of a base class- then try to use that abstract method in a passed aroud object- this will break other areas of that application we rely on and are working with outside of that subclasses definition. More on that in a moment.

In implementation of an ABC we might create an object in main() as the subclass of the base class, or when passing that subclassed object to a method and trying to call the function that no longer exists. This doesn't have to be in main()- we may find it elsewhere at runtime- but the key is that the necessity of implementing that method has been lost and might be required for the application to be able to use that commented out method, hence the type checking with a red underline. An example is soon to come, but let's also describe what Protocols look like.

#### Basic red underlines in VScode and how relationships happen 

ABC's rely on Nominal typing. So if we want a typing relationship to be there like A is of type B, then we explicitly write that down in code, like by using inheritance. With an ABC we create a subclass and inherit from the base class which establishes the relationship. The interpreter uses the relationship to see if the types match.

With an ABC we have an abstract method we implement it in the subclass. The typechecker will underline if the abstract method is missing for the class in red, then at runtime the interpreter attempts to make the object and realizes the method is not there that needs to be implemented, and we have a runtime error. 

#### How ABC defines the cohesion

With the @abstractmethod decorator and the method definition/declaration being in the base class ( it may be a parent class or more deeply nested in inheritance), we typically use the `pass` keyword, and allow the subclass to handle the different details.

#### Protocol as of Python 3.8
With a Protocol we may use `...` instead of a pass to define the methods of similar use as the ABC does. The point of the example given here is to break apart coupling between the diagnostics and the service that exists with the ABC implementation, and only have methods that pertain directly to what the application is doing here, mainly the connect,disconnect, and send as part of only the services, and the status update only for diagnostics, which makes sense that the two do not nee to know much about each other as a whole defined class with all the methods first given in ABC. 

This is part of creating more maintainable code over time, reducing the number of imports and more. The only trick here is that we need to make sure each device implement the right methods.

Protocol's use Structural typing, which are different than Nominal typing. We do not have to explicitly say something is of a certain type of the protocol, the interpreter will look at the way the structure of the objects exist. Do they have the same methods? The same properties? If so, it is assumed that the types match. This also affects how we use them.

We do not inherit from the Protocol class, but it defines the interface that is expected for the part of the program that refers to it. So if we have a function or a method in a class that gets an argument of a particular Protocol type, then anything that implements those methods that has those properties can be passed as an argument to that function or method. 

##### Duck typing 

With structural typing this will do the actual comparison of the structure of those objects to make sure the program works as expected. This works with pythons runtime type checking that treats two objects the same if they have the same methods and properties. This is what is referred to as duck typing.
 
#### Commenting out the connect in devices.py

The class HueLightDevice defines the connect method now directly.

We notice a difference at the point at which we see the problem occuring. With the Protocol we can create an instance of the HueLightDevice no problem,it does not inherit from an ABC now, and the error shifts to where the actual method that will try to call the connect happens in main.

#### Different approaches

With Protocol we define the interface to the other parts of the program that use the protocol in some way. The refactoring here involves grouping the methods that pertain to each of the services and the diagnostics and get rid of the need for a Device.py that originally had all of them in one place. ABC can do this in principal but require multiple inheritance structures.

We have a method that has an argument of the type Protocol now which helps define what the method expects.

With class IOTService in the service.py we have a def register_device which will call connect.

We are aiming at separating coupling between service and diagnostics.

#### Reduce coupling with collect_diagnostics 

In diagnostics.py, the collect_diagnostics(device: DiagnosticsSource) method does not need to know anything about a device that connects or disconnects or send messages, it only needs to know there is a status_update method. Therefore we can completely remove the file for device.py from the equation by defining within the diagnostics.py a class DiagnosticsSource(Protocol): and now the collect_diagnostics device is of type DiagnosticsSource instead of Device. 

#### Iot services similarly does not need to know about the status update method

The Iot services similarly only needs to know about the status_update, it just needs to know all the rest- connect disconnect and sennd message, because that is what it is using.

#### Protocols limit the interface and definitions of the interfaces needed

In principal diagnostics and devices are diifferent things, the diagnostics now only implents the DiagnosticSource Protocol with a retrieve status_update, and we do not need to connect or disconnect with- it doesn't adhere to the device protocol. We use it only in the diagnostics part, without it being part of the iot service.

Again we could use multiple inheritance to achieve this, but it introduces more imports that are not typically needed and has a lot more coupling.

Now with Protocol in this example we are defining the interface with only the parts we need for the other parts of the system and what it expects, and pass it the objects that adhere to that interface that implements that protocol.

#### Exploit conceptual differences

ABC's belong to their subclasses, creating a class heirarchy that can be used in other places of the program, whereas Protocols belong more closely to where they are used.

Also ABC's are not obsolete because of protocols either. ABC's are still good if we control who has creation of the subclasses and all of that might be in a separate module and we wish to keep that nice and clean together. Protocols might be better for a huge project that might have lots of classes that need to implement a particular protocol. Sometimes we are not even allowed to change those classes like part of a thrid party library that cannot change. 

We could use the protocol mechanism to decouple the third party tooling from our application without having to change the library and implement some kind of abstract class that we have to define then. Protocols are good to connect libraries in this manner and reduce coupling. We can define the same protocol multiple times liek if we have several modules that have to be completely independent from one another but still overlap in the kinds of objects they expect, we could define the same protocol, the same class in multiple files, and those files we could use everywhere without having to rely on the other file.

But now we talk about how this loses the handy ability to create methods in a superclass that can be used in all the subclasses. Inheritance helps also if we define a subclass and explicitly inherit from the abstract base class, then at that point the IDE and typechecking system in the IDE will help you identify mistakes while typing. With protocols this is not possible as there is no nominal relationship between the protocol and the class that implements the methods that the protocol defines.

Overall using protocolswe may lose some advantages of inheritance, but might feel like a more pythonic way to define the relationships between the different parts of program. The protocol way is the natural way to split up interfaces into only the parts that are used in the immediate area of the application. This is called interface segregation.
