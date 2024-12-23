* VHDL has been at the heart of electronic design productivity since initial ratification by the IEEE in 1987.

# 1.1 VHDL Terms
* Basic VHDL building blocks:
	* Entity - An entity is the most basic building block in a design.
	* Architecture:
		* The architecture describes the behavior of the entity.
		* A single entity can have multiple architectures.
		* One architecture might be behavioral while another might be a structural description of the design.
	* Configuration:
		* A configuration statement is used to bind a component instance to an entity-architecture pair.
		* A configuration can be considered like a parts list for a design. It describes which behavior to use for each entity, much like a parts list describes which part to use for each part in the design.
	* Package - A package is a collection of commonly used data types and subprograms used in a design.
	* Driver:
		* Driver is a source on a signal.
		* If a signal is driven by two sources, then when both sources are active, the signal will have 2 drivers.
	* Bus - a bus is a special kind of signal that may have its drivers turned off.
	* Attribute - An attribute is data that are attached to VHDL objects or predefined data about VHDL objects.
	* Generic - A generic is VHDL’s term for a parameter that passes information to an entity.
	* Process - A process is the basic unit of execution in VHDL.
* VHDL Descriptions consist of primary design units and secondary design units. 
	* The primary design units are the Entity and the Package. 
	* The secondary design units are the Architecture and the Package Body. 
	* Secondary design units are always related to a primary design unit. 
	* Libraries are collections of primary and secondary design units.

# 1.2 Entity
* A VHDL entity specifies the name of the entity, the ports of the entity, and entity-related information.
* Entity example:
	```vhdl
	ENTITY mux IS
		PORT ( a, b, c, d : IN BIT;
			s0, s1 : IN BIT; 
			x, : OUT BIT);
	END mux;
	```
	* Keyword **ENTITY** signifies that this is the start of an entity statement.
	* The name of the entity is mux.
	* The entity has 7 ports in the **PORT** clause. 
		* 6 ports are of mode **IN**.
		* 1 port is of mode **OUT**. 
		* 4 data input ports (a, b, c, d) are of type **BIT**. 
		* 2 multiplexer select inputs, s0 and s1, are also of type **BIT**. 
		* 1 output port is of type **BIT**.
	* The entity describes the interface to the outside world. It specifies the number of ports, the direction of the ports, and the type of the ports.

# 1.3 Architecture
* The architecture describes the underlying functionality of the entity and contains the statements that model the behavior of the entity.
* An entity can have multiple architectures describing the behavior of the entity.
	* 1 architecture could be a behavioral description, and another could be a structural description.
* Example of architecture added:
	```vhdl
	ENTITY mux IS
		PORT ( a, b, c, d : IN BIT;
			s0, s1 : IN BIT; 
			x, : OUT BIT);
	END mux;
	
	ARCHITECTURE dataflow OF mux IS
		SIGNAL select : INTEGER;
	BEGIN
		select <= 0 WHEN s0 = ‘0’ AND s1 = ‘0’ ELSE
			1 WHEN s0 = ‘1’ AND s1 = ‘0’ ELSE
			2 WHEN s0 = ‘0’ AND s1 = ‘1’ ELSE
			3;
			
		x <= a AFTER 0.5 NS WHEN select = 0 ELSE
			b AFTER 0.5 NS WHEN select = 1 ELSE
			c AFTER 0.5 NS WHEN select = 2 ELSE
			d AFTER 0.5 NS;
			
	END dataflow;
	```
	* Keyword **ARCHITECTURE** signifies that this statement describes an architecture for an entity.
	* The architecture name is dataflow.
	* The entity the architecture is describing is called mux.
	* The textual area between the keyword **ARCHITECTURE** and the keyword **BEGIN** is where **local signals** and components are declared for later use.

# 1.4 Concurrent Signal Assignment
* Inside a VHDL architecture, there is no specified ordering of the assignment statements. The order of execution is solely specified by **events** occurring on signals that the assignment statements are sensitive to.
* Example:
	```vhdl
	select <= 0 WHEN s0 = ‘0’ AND s1 = ‘0’ ELSE
			1 WHEN s0 = ‘1’ AND s1 = ‘0’ ELSE
			2 WHEN s0 = ‘0’ AND s1 = ‘1’ ELSE
			3;
	```
	* Signal **select** will get a numeric value assigned to it based on the values of s0 and s1. 
	* This statement is executed whenever either signal s0 or signal s1 has an event occur on it. An event on a signal is a change in the value of that signal. A
signal assignment statement is said to be sensitive to changes on any signals that are to the right of the <= symbol.

		














