# Spring Application: Restaurant Bill (Paradise)

## Question

Paradise restaurant has a bill counter. Customers can pay by **Credit Card, Debit Card or UPI**. Make a **PaymentMode interface**, create a class for each payment type, and connect them using **Spring (config.xml)**. Show a message when each object is created.

## ⭐ Spring application steps (remember this order)

Every XML-based Spring application follows these 5 steps:

1. Create the **Maven project**
2. Add the **Spring dependency** in `pom.xml`
3. Create the **classes as per the question**
4. Create **config.xml** and the **beans**
5. Create the **Main class** (container) and run

---

## Step 1: Create the Maven project

- File > New > **Maven Project**
- Give the package name and file name (`com.excelr`)

## Step 2: Add the Spring dependency in pom.xml

- Open `pom.xml`
- Google: **Maven Repository** > click the first website
- Search: **Spring Context**
- Pick a version > copy its dependency code
- Paste it inside `<dependencies>` in `pom.xml` > Save

---

## Step 3: Create the classes as per the question

**3.1 Interface**
- Right click `src/main/java` > New > **Interface** > name: `PaymentMode`
- Add one method:
```java
void paymentType();       // rule: every payment class must have this method
```

**3.2 Payment classes**
- Right click `com.excelr` package > New > **Class**
- Create 3 classes: `CreditCard`, `DebitCard`, `Upi`
- In each, after the class name, write `implements PaymentMode`
```java
public class CreditCard implements PaymentMode {
```
- A red error shows until the method is written. Click the error > **Add unimplemented methods**

**3.3 Constructors in all 3 classes**
- *Why:* to show a message when the object is created
- Open the class > place the cursor on a new line **after the closing brace of `paymentType()`**
- Top menu: **Source > Generate Constructors from Superclass...** (not the "using fields" option)
- Under `super();`, write the message:
```java
public CreditCard() {
    super();                                              // calls parent constructor
    System.out.println("Credit Card object is created successfully");
}
```
- `DebitCard` and `Upi`: same, with their own message
- **What is `super()`?** (It calls the parent class constructor. Eclipse adds it by default. Our classes only implement an interface, so the parent is Java's top class, `Object`. Just keep it.)

**3.4 ⭐ Billcounter class**
- Right click `com.excelr` > New > **Class** > `Billcounter`
- Write the variable: `PaymentMode p;`
- Click inside the class > **Source > Generate Constructor using Fields...** > tick `p` > Generate
- Write the `Display()` method that calls `p.paymentType()`
```java
PaymentMode p;                           // can hold any payment object

public Billcounter(PaymentMode p) {      // requires one PaymentMode object p when creating a Billcounter object
    super();
    this.p = p;                          // saves the given object
}

void Display() {
    p.paymentType();                     // runs paymentType() of the given object
}
```

**⭐ Understand your comment line:** *"requires one PaymentMode object p when creating a Billcounter object"*
- The brackets `(PaymentMode p)` mean Java **will not create a `Billcounter` unless you give it one payment object**.
- Why: the counter can't pay by itself. It needs a payment to work with.
- The type is the interface `PaymentMode`, so you can give **any** of `CreditCard`, `DebitCard` or `Upi`.
- Without Spring you would write: `new Billcounter(new CreditCard())`.
- With Spring, `config.xml` gives that object (Step 4.3).
- `this.p = p;` saves it (left `p` is the class variable, right `p` is the value that came in).
- `Display()` then uses whatever was saved.

---

## Step 4: Create config.xml and the beans

**4.1 Create the file**
- Right click `src/main/resources` > New > **File** > `config.xml`

**4.2 Add the header code**
- Google: **XML configuration** (Spring) > first website
- Copy the newer version code > paste in `config.xml` > Save

**4.3 ⭐ Create the beans**

*Theory:*
- A **bean** is an object that **Spring creates and manages**. We don't write `new` ourselves.
- `id` is the name of the object. `class` is the full class name with the package.
- `Billcounter` needs a payment object, so its bean has a `constructor-arg`. `ref="cc"` means: "give it the bean named cc."

*Code (inside `<beans> ... </beans>`):*
```xml
<bean id="cc" class="com.excelr.CreditCard"/>   <!-- object cc of CreditCard -->
<bean id="dc" class="com.excelr.DebitCard"/>    <!-- object dc of DebitCard -->
<bean id="u" class="com.excelr.Upi"/>           <!-- object u of Upi -->

<bean id="bc" class="com.excelr.Billcounter">   <!-- object bc of Billcounter -->
    <constructor-arg ref="cc" name="p"/>        <!-- gives cc to the constructor parameter p -->
</bean>
```

---

## Step 5: Create the Main class (container) and run

- Right click `com.excelr` > New > **Class** > name: `Main` (capital M)
- Tick **public static void main(String[] args)** > Finish

**⭐ Theory:**
- The Main class **starts Spring and uses the beans**.
- The **container** is `ApplicationContext`. It reads `config.xml`, creates all the beans and holds them.
- `ClassPathXmlApplicationContext("config.xml")` loads `config.xml` from the classpath (`src/main/resources`).
- `getBean("bc", Billcounter.class)` asks the container for the object with id `bc`.

**Code inside main:**
```java
ApplicationContext con = new ClassPathXmlApplicationContext("config.xml");  // create container
Billcounter bc1 = con.getBean("bc", Billcounter.class);                     // get the bc object
bc1.Display();                                                              // show the payment
```
- Imports needed: `ApplicationContext` and `ClassPathXmlApplicationContext` (Eclipse adds them with Ctrl + Shift + O)
- Run: right click `Main.java` > Run As > **Java Application**

**Output (messages as you wrote them):**
```
Credit Card object is created successfully
Debit Card object is created successfully
UPI object is created successfully
Payment done using Credit Card
```
All three "created" messages print because Spring creates **every bean** when the container starts.

---

## Recap of the last step

Main creates the container, the container reads `config.xml` and creates `cc`, `dc`, `u` and `bc` (giving `cc` to `bc`). Then `getBean("bc")` gets the counter and `Display()` runs the credit card payment. **The project is complete.**

## ⭐ How the whole flow runs (for revision)

1. `Main` starts the container with `config.xml`.
2. Spring creates the beans: `cc`, `dc`, `u` (their constructors print messages), then `bc` with `cc` passed in.
3. `getBean("bc")` returns the `Billcounter`.
4. `Display()` calls `p.paymentType()`, and `p` is `cc`, so the credit card message prints.
5. **To switch to UPI, change `ref="cc"` to `ref="u"` in `config.xml`. No Java code changes.** This is the main point of Spring. It is called **Dependency Injection** (here, constructor injection).

---

## Final code of all files

**PaymentMode.java**
```java
package com.excelr;

public interface PaymentMode {
    void paymentType();
}
```

**CreditCard.java** (`DebitCard` and `Upi` are the same with their own names and messages)
```java
package com.excelr;

public class CreditCard implements PaymentMode {

    @Override
    public void paymentType() {
        System.out.println("Payment done using Credit Card");
    }

    public CreditCard() {
        super();
        System.out.println("Credit Card object is created successfully");
    }
}
```

**Billcounter.java**
```java
package com.excelr;

public class Billcounter {
	PaymentMode p;
	public static void main(String[] args) {


	}
	public Billcounter(PaymentMode p) {   // requires one PaymentMode object p	 when creating a Billcounter object.
		super();
		this.p = p;
	}
	void Display() {
		p.paymentType();
	}

}
```

**config.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <!-- bean definitions here -->
	<bean id="cc" class="com.excelr.CreditCard"/>

	<bean id="dc" class="com.excelr.DebitCard"/>

	<bean id="u" class="com.excelr.Upi"/>

	<bean id="bc" class="com.excelr.Billcounter">
    	<constructor-arg ref="cc" name="p"/>
</bean>

</beans>
```

**Main.java**
```java
package com.excelr;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {

	private static ApplicationContext con;

	public static void main(String[] args) {
		ApplicationContext con =new ClassPathXmlApplicationContext("config.xml");

			Billcounter bc1 = con.getBean("bc", Billcounter.class);
			bc1.Display();
	}

}
```

## Two small cleanups (optional)

- In `Main`, the line `private static ApplicationContext con;` is not used, because the `con` inside `main` is the one that works. You can delete that line.
- In `Billcounter`, the empty `main` method is not used. You can delete it too.

Your earlier red marks in `config.xml` should be gone now that the beans use the correct syntax.