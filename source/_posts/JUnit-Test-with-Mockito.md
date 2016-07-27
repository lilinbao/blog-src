title: JUnit Test with Mockito
date: 2016-07-27 14:39:07
alias: 
tags: 
 - Mockito
 - JUnit
categories: 语言学习
---
## Prerequisites
Requires an understanding of unit testing with the JUnit framework.

## Target and challenge of unit testing
 - A unit test should test a class in isolation.
 - Side effects from other classes or the system should be eliminated if possible

## Classifications of different test classes

 - A `dummy` object is passed around but never used, i.e., its methods are never called. Such an object can for example be used to fill the parameter list of a method.
 - A `fake` objects have working implementations, but are usually simplified. For example, they use an in memory database and not a real database.
 - A `stub` class is an partial implementation for an interface or class with the purpose of using an instance of this stub class during testing. Stubs usually do responding at all to anything outside what’s programmed in for the test. Stubs may also record information about calls
 - A `mock` object is a dummy implementation for an interface or a class in which you define the output of certain method calls.Mock objects are typically configured. Mock objects typically require less code to configure and should therefore be preferred.
 

## Using Mockito for mocking objects

Mockito allows you to create and configure mock objects. 
If you use Mockito in tests you typically:
 - Mock away external dependencies and insert the mocks into the code under test
 - Execute the code under test
 - Validate that the code executed correctly
![image](http://note.youdao.com/yws/res/1463/WEBRESOURCE854b853d666db799b40d7e623b2eba31)

## Adding Mockito as dependencies to a project

 - Gradle 
 ```json
repositories { jcenter() }
dependencies { testCompile "org.mockito:mockito-core:2.0.57-beta" }
```
 - Maven
```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-all</artifactId>
    <version>2.0.2-beta</version>
</dependency>
```

## Using the Mockito API

1.   ### Static imports
     
        If you add a static import for `org.mockito.Mockito.\*;`, you can access Mockitos methods like `mock()` directly. Static imports allows you to call static members, i.e., methods and fields of a class directly without specifying the class.

2.   ### Creating and configuring mock objects with Mockito
     * Mockito supports the creation of mock objects. For this you can use the static `mock()` method.
Mockito also supports the creation of mock objects based on the `@Mock annotation`.

     * If you use this annotation, you must initialize the mock objects. The MockitoRule allows this. It invokes the static method `MockitoAnnotations.initMocks(this)` to populate the annotated fields. Alternatively you can use `@RunWith(MockitoJUnitRunner.class)`.

    The usage of `@Mock` 
	```java
	import static org.mockito.Mockito.*;
	import com.company.demo.IUserDao;

	public class TestDemo  {
	        
	    ClassToTest testTarget = null;
	        
	    @Mock //Tells Mockito to mock the userDao instance
	    IUserDao userDao; 
	        
	    @beforeClass
	    public void setup(){
	        testTarget = new ClassToTest(userService);
	    }
	    @Test
	    public void testQuery()  {
	        boolean check = testTarget.query("select * from t_user");
	        assertTrue(check); 
	        verify(userDao).query("select * from t_user"); 
	    }
	}
	```

3.   ### Configuring mocks

        The `when(...).thenReturn(...)` method chain is be used to specify a condition and a return value for this condition. If you specify more than one value,	they are returned in the order of specification until the last one is used. Afterwards the last	specified value	is returned. Mocks can also return different values depending on arguments passed into a method. You also use methods like `anyString()` or `anyInt()` to define that independent of the input value a certain return value should be returned.
The `doReturn(...).when(...)`methodCall call chain works similar as `when(...).thenReturn(...)` but is useful for void methods. The `doThrow()` variant can be used for methods which return void to throw an exception. 
4.   ### Verify the calls on the mock objects
    
     * Verify that the specified conditions are met - checks the result of a method call 
     * Behavior testing - checks that a method is called with the right parameters
5.   ### Wrapping Java objects with Spy
    
     `@Spy` or the `spy()` method can be used to wrap a real object. Every call, unless specified otherwise, is delegated to the object.
6.   ### Using @InjectMocks for dependency injection via Mockito

        `@InjectMocks` creates an instance of the class and injects the mocks that are created with the @Mock (or @Spy) annotations into this instance. 
        
        `Constructor Injection > Property Setter Injection > Field Injection`
        
7.   ### Capturing the arguments
        
        The ArgumentCaptor class allows to access the arguments of method calls during the verification. This allows to capture these arguments of method calls and to use them for tests.
	```java
	public class MockitoTests {

	        @Rule public MockitoRule rule = MockitoJUnit.rule();

	        @Captor
	    private ArgumentCaptor<List<String>> captor;


	        @Test
	    public final void shouldContainCertainListItem() {
	                List<String> asList = Arrays.asList("someElement_test", "someElement");
	        final List<String> mockedList = mock(List.class);
	        mockedList.addAll(asList);

	        verify(mockedList).addAll(captor.capture());
	        final List<String> capturedArgument = captor.<List<String>>getValue();
	        assertThat(capturedArgument, hasItem("someElement"));
	    }
	}
	```
8.   ### Limitations 

     - Final classes
     - Anonymous classes
     - Primitive types

9. ### Legacy 

	 - MockitoAnnotations.init(target) vs @Rule
	 - new Object vs @InjectMock
	 - @Spy vs @Mock