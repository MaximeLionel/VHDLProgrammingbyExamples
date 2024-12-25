* VHDL has been at the heart of electronic design productivity since initial ratification by the IEEE in 1987.

# 1.1 VHDL Terms
* Basic VHDL building blocks:
	* Entity (实体) - An entity is the most basic building block in a design.
	* Architecture (架构):
		* The architecture describes the behavior of the entity.
		* A single entity can have multiple architectures.
		* One architecture might be behavioral while another might be a structural description of the design.
	* Configuration (配置):
		* A configuration statement is used to bind a component instance to an entity-architecture pair.
		* A configuration can be considered like a parts list for a design. It describes which behavior to use for each entity, much like a parts list describes which part to use for each part in the design.
	* Package (包) - A package is a collection of commonly used data types and subprograms used in a design.
	* Driver:
		* Driver is a source on a signal.
		* If a signal is driven by two sources, then when both sources are active, the signal will have 2 drivers.
	* Bus - a bus is a special kind of signal that may have its drivers turned off.
	* Attribute (属性) - An attribute is data that are attached to VHDL objects or predefined data about VHDL objects.
	* Generic (通用参数) - A generic is VHDL’s term for a parameter that passes information to an entity.
	* Process (进程) - A process is the basic unit of execution in VHDL.
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
	* This statement is executed whenever either signal s0 or signal s1 has an event occur on it. An event on a signal is a change in the value of that signal. A signal assignment statement is said to be **sensitive** to changes on any signals that are to the right of the <= symbol.
	* This signal assignment statement is sensitive to s0 and s1. 
	* The other signal assignment statement in architecture dataflow is sensitive to signal select.
* Case 1 of the example:
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
	* Suppose that we have a steady-state condition where both s0 and s1 have a value of 0, and signals a, b, c, and d currently have a value of 0.
	* Signal x wil have a 0 value because it is assigned the value of signal a whenever signals s0 and s1 are both 0.
	* **Event  1** - signal a changes its value to 1.
		* The first assignment below will not execute because this statement is **not sensitive** to changes to signal a.
		```vhdl
		select <= 0 WHEN s0 = ‘0’ AND s1 = ‘0’ ELSE
				1 WHEN s0 = ‘1’ AND s1 = ‘0’ ELSE
				2 WHEN s0 = ‘0’ AND s1 = ‘1’ ELSE
				3;
		```
		* The second signal assignment statement will execute because it is **sensitive** to events on signal a.
		```vhdl
		x <= a AFTER 0.5 NS WHEN select = 0 ELSE
				b AFTER 0.5 NS WHEN select = 1 ELSE
				c AFTER 0.5 NS WHEN select = 2 ELSE
				d AFTER 0.5 NS;
		```
		* Output port x will now change to 1.
	* **Event 2** - port s0 changes the value from 0 to 1.
		* The first signal assignment statement is **sensitive** to signal s0 and will therefore execute.
		```vhdl
		select <= 0 WHEN s0 = ‘0’ AND s1 = ‘0’ ELSE
				1 WHEN s0 = ‘1’ AND s1 = ‘0’ ELSE
				2 WHEN s0 = ‘0’ AND s1 = ‘1’ ELSE
				3;
		```
		* With the value of s0 equal to 1 and s1 equal to 0, signal select will receive a new value of 1.
		* This new value of signal select will cause an event to occur on signal select, causing the second signal assignment statement to execute.
		* The new assignment will cause port x to change from a 0 to a 1.

# 1.5 Event Scheduling
* The mechanism for delaying the new value is called **scheduling an event**.
	* By assigning port x a new value, an event was scheduled 0.5 nanoseconds in the future that contains the new value for signal x. 
	* When the event matures (0.5 nanoseconds in the future), signal x receives the new value.

# 1.6 Statement Concurrency
* The first assignment is the only statement to execute when events occur on ports s0 or s1. 
* The second signal assignment statement does not execute unless an event on signal select occurs or an event occurs on ports a, b, c, d.
* The two signal assignment statements in architecture behave form a behavioral model (行为模型), or architecture, for the mux entity

# 1.7 Structural Designs
* Another way to write the mux design is to instantiate subcomponents (子组件) that perform smaller operations of the complete model, which is a structural description of the mux entity.
* Example:
```vhdl
ARCHITECTURE netlist OF mux IS
	COMPONENT andgate 
		PORT(a, b, c : IN bit; c : OUT BIT);
	END COMPONENT;
	COMPONENT inverter
		PORT(in1 : IN BIT; x : OUT BIT);
	END COMPONENT;
	COMPONENT orgate 
		PORT(a, b, c, d : IN bit; x : OUT BIT);
	END COMPONENT;
	
SIGNAL s0_inv, s1_inv, x1, x2, x3, x4 : BIT;

BEGIN
	U1 : inverter(s0, s0_inv);
	U2 : inverter(s1, s1_inv);
	U3 : andgate(a, s0_inv, s1_inv, x1);
	U4 : andgate(b, s0, s1_inv, x2);
	U5 : andgate(c, s0_inv, s1, x3);
	U6 : andgate(d, s0, s1, x4);
	U7 : orgate(x2 => b, x1 => a, x4 => d, x3 => c, x => x);
END netlist;
```

* Declaration of lower-level components:
	```vhdl
	COMPONENT andgate 
		PORT(a, b, c : IN bit; c : OUT BIT);
	END COMPONENT;
	COMPONENT inverter
		PORT(in1 : IN BIT; x : OUT BIT);
	END COMPONENT;
	COMPONENT orgate 
		PORT(a, b, c, d : IN bit; x : OUT BIT);
	END COMPONENT;
	```
	* This description uses a number of lower-level components to model the behavior of the mux device: an **inverter** component, an **andgate** component and an **orgate** component.
	* Each of these components is declared in the architecture declaration section, which is between the architecture statement and the **BEGIN** keyword.

* Declaration of local signals:
	```vhdl
	SIGNAL s0_inv, s1_inv, x1, x2, x3, x4 : BIT;
	```
	* A number of local signals are used to connect each of the components to form the architecture description. These local signals are declared using the **SIGNAL** declaration.

* The architecture statements:
	```vhdl
	U1 : inverter(s0, s0_inv);
	U2 : inverter(s1, s1_inv);
	U3 : andgate(a, s0_inv, s1_inv, x1);
	U4 : andgate(b, s0, s1_inv, x2);
	U5 : andgate(c, s0_inv, s1, x3);
	U6 : andgate(d, s0, s1, x4);
	U7 : orgate(x2 => b, x1 => a, x4 => d, x3 => c, x => x);
	```
	* In this example are a number of component instantiation statements (组件实例化语句), which are labeled U1-U7.
	* Notice component instantiation statement U7:
		```vhdl
		U7 : orgate(x2 => b, x1 => a, x4 => d, x3 => c, x => x);
		```
		* Check the component declaration:
		```vhdl
		COMPONENT orgate 
			PORT(a, b, c, d : IN bit; x : OUT BIT);
		END COMPONENT;
		```
		* This statement uses **named association** (命名关联) to match the ports and signals to each other. 
		* For instance port x2 of the orgate is connected to port b of the entity with the first association clause. 
		* The last instantiation clause connects port x of the orgate component to port x of the entity。

# 1.8 Sequential Behavior
* Another way to describe the functionality of the mux is to use a process statement to describe the functionality in an algorithmic representation.
```vhdl
ARCHITECTURE sequential OF mux IS
	(a, b, c, d, s0, s1 )
	VARIABLE sel : INTEGER;

	BEGIN
		IF s0 = ‘0’ and s1 = ‘0’ THEN
			sel := 0;
		ELSIF s0 = ‘1’ and s1 = ‘0’ THEN
			sel := 1;
		ELSIF s0 = ‘0’ and s1 = ‘0’ THEN
			sel := 2;
		ELSE 
			sel := 3;
		END IF;
		
		CASE sel IS
			WHEN 0 =>
				x <= a;
			WHEN 1 =>
				x <= b;
			WHEN 2 =>
				x <= c;
			WHEN OTHERS =>
				x <= d;
		END CASE;
	END PROCESS;
END sequential;
```
* The architecture contains only one statement, called a process statement. 
* It starts at the line beginning with the keyword **PROCESS** and ends with the line that contains **END PROCESS**. 
* All the statements between these 2 lines are considered part of the process statement.

# 1.9 Process Statements
* The process statement consists of a number of parts: 
	* the sensitivity list (敏感信号列表); 
	* the process declarative part; 
	* the statement part.

# 1.10 Process Declarative Region
* The process declarative part consists of the area between the end of the sensitivity list and the keyword **BEGIN**.
```vhdl
VARIABLE sel : INTEGER;
```
* The declarative part contains a variable declaration that declares local variable `sel`. 
* This variable is used locally to contain the value computed based on ports `s0` and `s1`.

# 1.11 Process Statement Part
* The statement part of the process starts at the keyword **BEGIN** and ends at the **END PROCESS** line.
* The order of the statements in the architecture did not make any difference; however, this is not true inside the process. The order of execution is the order of the statements in the process statement.

# 1.12 Process Execution
* Take the example above:
```vhdl
ENTITY mux IS
	PORT ( a, b, c, d : IN BIT;
		s0, s1 : IN BIT; 
		x, : OUT BIT);
END mux;

ARCHITECTURE sequential OF mux IS
	(a, b, c, d, s0, s1 )
	VARIABLE sel : INTEGER;

	BEGIN
		IF s0 = ‘0’ and s1 = ‘0’ THEN
			sel := 0;
		ELSIF s0 = ‘1’ and s1 = ‘0’ THEN
			sel := 1;
		ELSIF s0 = ‘0’ and s1 = ‘0’ THEN
			sel := 2;
		ELSE 
			sel := 3;
		END IF;
		
		CASE sel IS
			WHEN 0 =>
				x <= a;
			WHEN 1 =>
				x <= b;
			WHEN 2 =>
				x <= c;
			WHEN OTHERS =>
				x <= d;
		END CASE;
	END PROCESS;
END sequential;
```
* Assume that `s0` changes to 1:
	* Because `s0` is in the **sensitivity list** for the process statement, the process is invoked.
	* Each statement in the process is then executed **sequentially**.
* The first check is to see if s0 is equal to a 0. 
	* This statement fails because s0 is equal to a 1 and s1 is equal to a 0. 
	* The signal assignment statement that follows the first check will not be executed. 
* Instead, the next check is performed. This check succeeds and the signal assignment statements following the check for s0 = 1 and s1 = 0 are executed.
	```vhdl
	sel := 1;
	```

# 1.13 Sequential Statements
* This statement will execute sequentially:
	* For `IF` statement, whenever a check succeeds, no other checks are done.
	* The `IF` statement has completed and now the `CASE` statement will execute.
	* The `CASE` statement will evaluate the value of `sel` computed earlier by the `IF` statement and then execute the appropriate statement that matches the value of `sel`.
		```vhdl
		x <= b;
		```








