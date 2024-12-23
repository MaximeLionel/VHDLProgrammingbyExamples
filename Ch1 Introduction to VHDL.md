* VHDL has been at the heart of electronic design productivity since initial ratification by the IEEE in 1987.

# VHDL Terms
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

# Entity
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