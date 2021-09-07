# Spring DI에 대해서 알아보자.. (XML을 통한 방법)

## 1. Spring Framework이란 무엇인가?
- Java 엔터프라이즈 개발을 편하게 해주는 Framework이다. 스프링에는 2가지 큰 특징이 있다.
    1. DI(Dependency Injection) bean 객체생성
        * DI(Dependency Injection)란? 단어 그대로 판단해보면 '의존성주입'이라는 의미이다. 그렇다면 기존의 방식은 어떠했는지 먼저 알아보는게 중요하다. 
        Ioc container를 통해서 개발자가 직접 객체를 생성하는게 아니라 Spring Framework가 그러한 역할을 대신 해준다고 이야기 했었는데요. Ioc가 객체를 생성할 때 Spring Framework가 Dependency를 대신 주입해준다.(의존성을 줄여주면서 유지보수가 용이하다.) 먼저 생성자를 통한 DI를 코드로 알아보자. (Spring Framework을 썻을 때와 아닐 때를 비교하면서 살펴보겠다.)

        * 생성자를 통한 값 DI(Java로 직접할 경우)
        * TestBean1.class
        ```
        package sample.beans;

        public class TestBean1 {
            private int data1;
            private double data2;
            private String data3;
            
            public TestBean1(int data1) {
                System.out.println("TestBean1의 생성자 입니다.");
                this.data1 = data1;
                this.data2 = 0.0;
                this.data3 = null;
            }
            public void printData() {
                System.out.printf("data1 : %d\n", data1);
                System.out.printf("data2 : %f\n", data2);
                System.out.printf("data1 : %s\n", data3);
            }
        }
        ```        
        * MainClass.class
        ```
        package sample.main;
        import sample.beans.TestBean1;

        public class MainClass {
            public static void main(String[] args) {
                TestBean1 testbean1 = new TestBean1(100);
                testbean1.printData();
            }
        }
        ```
        * 생성자를 통한 DI(Spring Framework을 통해서)
        * beans.xml (contructor-arg가 Spring Framework가 DI해주는 부분)
        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd">

            <bean id="testbean1" class="sample.beans.TestBean1" lazy-init="true">
                <constructor-arg value="100"></constructor-arg>
            </bean>
        </beans>
        ```
        * TestBean1.class
        ```
        package sample.beans;

        public class TestBean1 {
            private int data1;
            private double data2;
            private String data3;
            
            public TestBean1(int data1) {
                System.out.println("TestBean1의 생성자 입니다.");
                this.data1 = data1;
                this.data2 = 0.0;
                this.data3 = null;
            }
            public void printData() {
                System.out.printf("data1 : %d\n", data1);
                System.out.printf("data2 : %f\n", data2);
                System.out.printf("data1 : %s\n", data3);
            }
        }
        ```
        * MainClass.class
        ```
        package sample.main;
        import org.springframework.context.support.ClassPathXmlApplicationContext;

        import sample.beans.TestBean1;

        public class MainClass {
            public static void main(String[] args) {
                ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("sample/config/beans.xml");
                TestBean1 testbean1 = ctx.getBean("testbean1",TestBean1.class);
                testbean1.printData();
                ctx.close();
            }
        }
        ```
        TestBean1.class에 생성자가 매개변수로 'public TestBean1(double data2)'를 받아서 초기화를 해준다고 가정을 해보자. beans.xml에서는 매개변수에 100을 넣으라는 주입하라는 의미가 되는데 100이라는 수는 double타입에도 들어갈 수 있고 int타입에도 들어 갈 수 있는데 어떻게 해야되지 이렇게 고민이 된다. 해결 방법은 beans.xml에서 bean에 type이라는 속성을 통해 지정할 수 있다.
        (지정을 안할 경우 String >  double > int으로 우선순위를 가진다.)
        코드를 보자.
        * beans.xml
        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd">

            <bean id="testbean1" class="sample.beans.TestBean1" lazy-init="true">
                <constructor-arg value="100"></constructor-arg>
            </bean>
            <bean id="testbean2" class="sample.beans.TestBean1" lazy-init="true">
                <constructor-arg value="100" type="double"></constructor-arg>
            </bean>
        </beans>
        ```
        * TestBean1.class
        ```
        package sample.beans;

        public class TestBean1 {
            private int data1;
            private double data2;
            private String data3;
            
            public TestBean1(int data1) {
                System.out.println("TestBean1의 int생성자 입니다.");
                this.data1 = data1;
                this.data2 = 0.0;
                this.data3 = null;
            }
            public TestBean1(double data2) {
                System.out.println("TestBean1의 double생성자 입니다.");
                this.data1 = 0;
                this.data2 = data2;
                this.data3 = null;
            }
            public void printData() {
                System.out.printf("data1 : %d\n", data1);
                System.out.printf("data2 : %f\n", data2);
                System.out.printf("data1 : %s\n", data3);
            }
        }
        ```
        * MainClass.class
        ```
        package sample.main;
        import org.springframework.context.support.ClassPathXmlApplicationContext;

        import sample.beans.TestBean1;

        public class MainClass {
            public static void main(String[] args) {
                ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("sample/config/beans.xml");
                TestBean1 testbean1 = ctx.getBean("testbean1",TestBean1.class);
                testbean1.printData();
                System.out.println("----------------------------------------");
                TestBean1 testbean2 = ctx.getBean("testbean2",TestBean1.class);
                testbean2.printData();
                ctx.close();
            }
        }
        ```
        * 생성자를 통한 객체 주입 DI(Java로 직접할 경우)
        * DataBean.class
        ```
        package sample.beans;

        public class DataBean {
            private int databean1;
        }
        ```
        * TestBean2.class
        ```
        package sample.beans;

        public class TestBean2 {
            private DataBean data1;
            private DataBean data2;
            
            public TestBean2(DataBean data1, DataBean data2) {
                this.data1 = data1;
                this.data2 = data2;
            }
            public void printData() {
                System.out.printf("data1 : %s\n", data1);
                System.out.printf("data2 : %s\n", data2);
            }
        }
        ```
        * MainClass.class
        ```
        package sample.main;
        import org.springframework.context.support.ClassPathXmlApplicationContext;

        import sample.beans.DataBean;
        import sample.beans.TestBean2;

        public class MainClass {
            public static void main(String[] args) {
                ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("sample/config/beans.xml");
                DataBean d1 = new DataBean();
                DataBean d2 = new DataBean();
                TestBean2 testbean1 = new TestBean2(d1, d2);
                testbean1.printData();
                ctx.close();
            }
        }
        ```
        * 생성자를 통한 객체 주입 DI(Spring Framework 통해서)
        * beans.xml
        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd">
            <bean id="objectbean1" class="sample.beans.TestBean2" lazy-init="true">
                <constructor-arg>
                    <bean class="sample.beans.DataBean"/>
                </constructor-arg>
                <constructor-arg>
                    <bean class="sample.beans.DataBean"/>
                </constructor-arg>
            </bean>
        </beans>
        ```
        * DataBean.class
        ```
        package sample.beans;

        public class DataBean {
            private int databean1;
        }
        ```
        * TestBean2.class
        ```
        package sample.beans;

        public class TestBean2 {
            private DataBean data1;
            private DataBean data2;
            
            public TestBean2(DataBean data1, DataBean data2) {
                this.data1 = data1;
                this.data2 = data2;
            }
            public void printData() {
                System.out.printf("data1 : %s\n", data1);
                System.out.printf("data2 : %s\n", data2);
            }
        }
        ```
        * MainClass.class
        ```
        package sample.main;
        import org.springframework.context.support.ClassPathXmlApplicationContext;

        import sample.beans.DataBean;
        import sample.beans.TestBean2;

        public class MainClass {
            public static void main(String[] args) {
                ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("sample/config/beans.xml");
                DataBean d1 = new DataBean();
                DataBean d2 = new DataBean();
                TestBean2 testbean1 = new TestBean2(d1, d2);
                testbean1.printData();
                System.out.println("-------------------------------------------");
                TestBean2 testbean2 = ctx.getBean("objectbean1", TestBean2.class);
                testbean2.printData();
                ctx.close();
            }
        }
        ```
        * 생성자를 통한 객체 주입 DI (Spring Framework 통해서) 이미 만들어진 객체주입
        * beans.xml
        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd">
            <bean id="databean" class="sample.beans.DataBean" scope="prototype"/>
            <bean id="objectbean1" class="sample.beans.TestBean2" lazy-init="true">
                <constructor-arg ref="databean"></constructor-arg>
                <constructor-arg ref="databean"></constructor-arg>
            </bean>
        </beans>
        ```
        여기서 id속성에 databean이라고 적힌 bean객체 중 scope="prototype"을 적은게 있는게 사용한 이유는 우리가 저번에 배웠다 싶이 scope="prototype"을 작성하게 되면 이 속성이 적힌 bean 참조할 때 마다 객체가 새로 생성된다.이러한 특징을 이용해서 constructor-arg가 databean이라는 id를 가진 bean을 참조해서 주입을 해주기 때문에 결과적으로 d1과 d2는 다른 객체이다. prototype을 작성하지 않을 경우 같은 객체가 주입된다. 중요 !!

        * Setter 메서드를 통한 값 DI(Java를 이용해서 직접할 경우)
        * TestBean1.class
        ```
        package sample.beans;

        public class TestBean1 {
            private int data1;

            public TestBean1() {
            }
            
            public int getData1() {
                return data1;
            }
            
            public void setData1(int data1) {
                this.data1 = data1;
            }
        ```
        * MainClass.class
        ```
        package sample.main;

        import sample.beans.TestBean1;

        public class MainClass {
            public static void main(String[] args) {
                TestBean1 t1 = new TestBean1();
                t1.setData1(100);
                System.out.printf("t1.data1 : %s\n", t1.getData1());
            }
        }
        ```
        * Setter 메서드를 통한 값 DI를 통해
        * beans.xml
        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd">
            <bean id="testbean1" class="sample.beans.TestBean1" lazy-init="true">
                <property name="data1" value="100"/>
            </bean>
        </beans>
        ```
        * TestBean1.class
        ```
        package sample.beans;

        public class TestBean1 {
            private int data1;
            
            public TestBean1() {
            }

            public int getData1() {
                return data1;
            }
            
            public void setData1(int data1) {
                this.data1 = data1;
            }
        }
        ```
        * MainClass.class
        ```
        package sample.main;

        import org.springframework.context.support.ClassPathXmlApplicationContext;

        import sample.beans.TestBean1;
        
        public class MainClass {
            public static void main(String[] args) {
                ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("sample/config/beans.xml");
                TestBean1 testbean1 = ctx.getBean("testbean1", TestBean1.class);
                System.out.printf("testbean1.data1 : %s\n", testbean1.getData1());
                ctx.close();
            }
        }
        ```
        동작원리는 Ioc container에서 getBean()메소드를 호출하면 객체를 생성하는데(bean속성 lazy-init때문) TestBean.class의 필드가 private 접근자이기에 데이터를 초기화 하기 위해서는 setter를 이용해서 초기화해야된다. 그것을 개발자가 직접할 수도 있지만 Spring Framework라이브러리를 이용해서 xml을 통해서 할 수 있다. bean에서 property에서 name속성을 사용하면 된다. 그러면 name="data1"속성에 들어가 데이터를 이용해서 setData1로 바꿔서 해당 메소드에 property에 작성된 value 값을 넣는다. (name 속성 앞에 set+대문자로해서 해당메소드를 찾아간다.)        
        * Setter 메서드를 통한 객체 DI(Java를 이용해서 직접할 경우)
        * DataBean.class
        ```
        package sample.beans;

        public class DataBean {
            private String name;

            public DataBean() {
            }

            public String getName() {
                return name;
            }

            public void setName(String name) {
                this.name = name;
            }
            
        }
        ```
        * TestBean1.class
        ```
        package sample.beans;

        public class TestBean1 {
            private int data1;
            private DataBean data2;
            
            public TestBean1() {
            }
            
            public DataBean getData2() {
                return data2;
            }

            public void setData2(DataBean data2) {
                this.data2 = data2;
            }

            public int getData1() {
                return data1;
            }
            
            public void setData1(int data1) {
                this.data1 = data1;
            }
        }
        ```
        * MainClass.class
        ```
        package sample.main;

        import sample.beans.DataBean;
        import sample.beans.TestBean1;

        public class MainClass {
            public static void main(String[] args) {
                TestBean1 t1 = new TestBean1();
                DataBean d1 = new DataBean();
                t1.setData2(d1);
                System.out.printf("t1.data2 : %s\n", t1.getData2());
            }
        }
        ```
        * Setter 메서드를 통한 객체 DI를 통해
        * beans.xml
        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd">
            <bean id="testbean1" class="sample.beans.TestBean1" lazy-init="true">
                <property name="data2">
                    <bean class="sample.beans.DataBean"></bean>
                </property>
            </bean>
        </beans>
        ```
        * DataBean.class
        ```
        package sample.beans;

        public class DataBean {
            private String name;

            public DataBean() {
            }

            public String getName() {
                return name;
            }

            public void setName(String name) {
                this.name = name;
            }
        }
        ```
        * TestBean1.class
        ```
        package sample.beans;

        public class TestBean1 {
            private int data1;
            private DataBean data2;
            
            public TestBean1() {
            }
            
            public DataBean getData2() {
                return data2;
            }

            public void setData2(DataBean data2) {
                this.data2 = data2;
            }

            public int getData1() {
                return data1;
            }
            
            public void setData1(int data1) {
                this.data1 = data1;
            }
        }
        ```
        * MainClass.class
        ```
        package sample.main;

        import org.springframework.context.support.ClassPathXmlApplicationContext;

        import sample.beans.TestBean1;

        public class MainClass {
            public static void main(String[] args) {
                ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("sample/config/beans.xml");
                TestBean1 testbean1 = ctx.getBean("testbean1", TestBean1.class);
                System.out.printf("testbean1.data2 : %s\n", testbean1.getData2());
                ctx.close();
            }
        }
        ```
        * Setter 메서드를 통한 객체 DI를 통해 이미 만들어진 객체 주입
        * beans.xml
        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd">
                
            <bean id="databean" class="sample.beans.DataBean"></bean>
                
            <bean id="testbean1" class="sample.beans.TestBean1" lazy-init="true">
                <property name="data2" ref="databean"></property>
                <property name="data3" ref="databean"></property>
            </bean>
        </beans>
        ```
        * TestBean.class
        ```
        package sample.beans;

        public class TestBean1 {
            private int data1;
            private DataBean data2;
            private DataBean data3;
            
            public TestBean1() {
            }
            
            public DataBean getData3() {
                return data3;
            }

            public void setData3(DataBean data3) {
                this.data3 = data3;
            }

            public DataBean getData2() {
                return data2;
            }

            public void setData2(DataBean data2) {
                this.data2 = data2;
            }

            public int getData1() {
                return data1;
            }
            
            public void setData1(int data1) {
                this.data1 = data1;
            }
        }
        ```
        * MainClass.class
        ```
        package sample.main;

        import org.springframework.context.support.ClassPathXmlApplicationContext;

        import sample.beans.TestBean1;

        public class MainClass {
            public static void main(String[] args) {
                ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("sample/config/beans.xml");
                TestBean1 testbean1 = ctx.getBean("testbean1", TestBean1.class);
                System.out.printf("testbean1.data2 : %s\n", testbean1.getData2());
                System.out.printf("testbean1.data3 : %s\n", testbean1.getData3());
                ctx.close();
            }
        }
        ```
        MainClass에서 보다 싶이 객체2개를 넣어서 출력해보면 같은 객체인 것을 확인 할 수 있다.
        이미 만들객체를 사용할 때 scope속성을 사용 안할 경우 기본적으로 싱글톤이기 때문이다.
        다른 객체를 넣으려면 bean에 scope="prototype"을 넣으면 된다.
        코드를 통해 알아보자.
        * beans.xml
        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd">
                
            <bean id="databean" class="sample.beans.DataBean" scope="prototype"></bean>
                
            <bean id="testbean1" class="sample.beans.TestBean1" lazy-init="true">
                <property name="data2" ref="databean"></property>
                <property name="data3" ref="databean"></property>
            </bean>
        </beans>
        ```
        * MainClass.class
        ```
        package sample.main;

        import org.springframework.context.support.ClassPathXmlApplicationContext;

        import sample.beans.TestBean1;

        public class MainClass {
            public static void main(String[] args) {
                ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("sample/config/beans.xml");
                TestBean1 testbean1 = ctx.getBean("testbean1", TestBean1.class);
                System.out.printf("testbean1.data2 : %s\n", testbean1.getData2());
                System.out.printf("testbean1.data3 : %s\n", testbean1.getData3());
                ctx.close();
            }
        }
        ```




















    




