# Project Source Code

### `pom.xml`
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.excelr</groupId>
  <artifactId>RestuarantBillinspring</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <dependencies>
  
  <!-- Source: https://mvnrepository.com/artifact/org.springframework/spring-context -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>7.1.0-RC1</version>
    <scope>compile</scope>
</dependency> 
  
  </dependencies>
</project>
```

### `PaymentMode.java`
```java
package com.excelr;

public interface PaymentMode {
    void paymentType();
}
```

### `CreditCard.java`
```java
package com.excelr;

public class CreditCard implements PaymentMode {

    @Override
    public void paymentType() {
        System.out.println("payment done through credit card");
        
    }

    public CreditCard() {
        super();
        System.out.println("CreditCard object created successfully!");
    }
    
}
```

### `DebitCard.java`
```java
package com.excelr;      
public class DebitCard  implements PaymentMode{

    @Override
    public void paymentType() {
        System.out.println("Payment done through debit card")    ;    
    }

    public DebitCard() {
        super();
        System.out.println("DebitCard object created successfully!");
    }
    
    
}
```

### `Upi.java`
```java
package com.excelr;
public class Upi  implements PaymentMode{

    @Override
    public void paymentType() {
        System.out.println("payment done through upi");
        
    }

    public Upi() {
        super();
        System.out.println("UPI object created successfully!");
    }
    
}
```

### `Billcounter.java`
```java
package com.excelr;

public class Billcounter {
    PaymentMode p;
    public static void main(String[] args) {
        
        
    }
    public Billcounter(PaymentMode p) {   // requires one PaymentMode object p when creating a Billcounter object.
        super();
        this.p = p;
    }
    void Display() {
        p.paymentType();
    }

}
```

### `config.xml`
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

### `Main.java`
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
