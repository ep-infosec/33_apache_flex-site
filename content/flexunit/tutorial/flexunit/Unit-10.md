Title:  Unit 10 - Mock Classes

<a href="../code/Unit10.zip"><img src="../images/DownloadIcon.png" alt="Download" /> Download Unit Project Files</a>

<p>In the beginning of this course you learned that unit testing a class requires that it be isolated from other dependencies. In this way, you can test your code free from unnecessary complexity and potentially unknown side effects introduced by other classes.</p>
<p>Mocking is one technique for isolating your class. It works by providing intelligent substitutes for class dependencies.</p>

<h3>Objectives:</h3>

<p>After completing this lesson, you should be able to:</p>
<ul>
	<li>Create test cases using Mock Objects</li>
	<li>Use the Mockolate Rule to automatically create mocks</li>
	<li>Add and validate mock functionality with stubbing, spying, and verification</li>
	<li>Set expectations for mock classes using the Mockolate framework</li>
</ul>

<h3>Topics</h3>

<p>In this unit, you will learn about the following topics:</p>
<ul>
	<li>Understanding the use case for mocks</li>
	<li>Examining available mocking frameworks</li>
	<li>Using Rules</li>
	<li>Working with a mock</li>
	<li>Using the Mockolate Rule</li>
	<li>Creating a test with mock objects</li>
	<li>Individual verifications</li>
</ul>

<h2>Understanding the use case for mocks</h2>

<p>When unit testing a system, it is essential that its components are tested in isolation.</p>
<p>Consider this example:</p>
<ol>
	<li><p>A Schedule class facilitates the storage of appointments in a database.</p></li>
	<li><p>The scheduleAppointment, cancelAppointment, and modifyAppointment methods of the Schedule class need to be tested.</p></li>
	<li><p>As you might imagine, each of these methods takes several parameters and each has several different outcomes requiring many tests.</p></li>
	<li><p>In production, the Schedule class interacts with services that make connections to a remote database to retrieve and store schedule information.</p></li> 
</ol>
<p>Consider the possible dependencies involved in the Schedule class in the system. First, the class interacts with services in some way, making the services a dependency. The services connect to a database, likely through an application server. The request is sent over the network, meaning that the network card, protocol stack and physical connections are all involved.</p>
<p>There are a huge number of problems with testing this system. If a failure occurs in any of the layers mentioned above, the tests for your Schedule Class will fail. Given that situation, how can you possibly determine the cause of the failure?</p>
<p>Additionally, tests written against an entire system like this can take significant time to retrieve data or produce a failure. This extra time discourages you from running the test frequently.</p>
<p>If you attempt to test the entire system, then you are not testing the Schedule class in isolation and it is therefore not a unit test.</p>
<p>You could try to solve this issue in a number of ways. You could create test versions of the database, but this adds extra effort and maintenance to objects that have nothing to do with the class functionality. You could create your application to return fake or stub data in specific circumstances, but this again causes extra maintenance and means that you are testing your system in a slightly different way than it is deployed.</p>
<p>Test substitutes can be used to solve these problems. Test substitutes are essentially fake versions of objects used for testing. In this case, if code is well architected, you can provide a test substitute for the services used by the Schedule class. These substitutes generally come in two flavors: Fake objects and Mock objects.</p> 
<p>Fake objects are object shells that have very little functionality of their own. They may have no functionality, or only functionality that can be verified by simple visual inspection. This lack of functionality means that the Fake object is not suspect when a test failure occurs, thus refocusing the tests back to the Schedule class itself.</p>
<p>The second type of substitute object used is a Mock object. Mocks are similar to Fake objects; however, they are more intelligent and complex internally. Mock objects allow you to set expectations and verify those expectations.</p>
<p>In other words, you could provide a service layer Mock to the Schedule class and indicate that the Mock should expect to see its <code>callTheServer()</code> method called and passed a particular schedule entry.</p>
<p>Mocks are extremely useful and can once again increase the legibility of your test cases. As Mocks are not verifiable via simple visual inspection, they are often built upon a mocking framework that provides the base mock functionality and a suite of tests to prove it works correctly. This again lets you focus on the Schedule class instead of testing all of its external dependencies.</p>

<h2>Examining available Mocking frameworks</h2>

<p>There are many mocking frameworks available to the ActionScript community. This list should not be considered an exhaustive list, but these are among the more popular at the time of writing.</p>
<ul>
	<li>asmock</li>
	<ul>
		<li>Provides a specialized runner for FlexUnit 4</li>
		<li>Creates mock versions of a class's public interface</li>
		<li>Allows creation of required and optional results</li>
	</ul>
	<li>mock-as3</li>
	<ul>
		<li>Older mocking framework that required the creation of static classes that act as mocks.</li>
		<li>The author of mock-as3 has released a newer framework named Mockolate which will be used in this unit.</li>
	</ul>
	<li>mock4as</li>
	<ul>
		<li>Minimalistic framework</li>
		<li>Can use subclassing or composition</li>
		<li>With effort, can return very detailed customized results</li>
		<li>mockito-flexTest spy framework that works with FlexUnit 4.x</li>
		<li>Extension of asmock</li>
	</ul>
	<li>mockolate</li>
	<ul>
		<li>Uses a custom FlexUnit Rule to institute mock behavior</li>
		<li>Can be run alongside other custom runners</li>
		<li>Allows creation of strict or nice mocks</li>
		<ul>
			<li>Nice mocks do not require expectations on all behavior allowing testing of only specific functionality.</li>
			<li>Strict mocks require expectations on all behavior.</li>
		</ul>
	</ul>
</ul>
<p>In this course you will use Mockolate. The developers of Mockolate are active contributors to the FlexUnit project.</p>

<h2>Working with a mock</h2>

<p>Mocks set up a special form of a substitute object which records all method calls and property access of the mock. Each time a method on the mock is called, the arguments supplied are recorded.</p> 
<p>Mocks can also be configured to return known values based on given input. In other words, a mock could be told to return true each time a given method is called providing the minimal necessary logic to test your class.</p>
<p>Setting up a mock requires understanding the expected interaction between the object under test and the mocked dependency as you are simulating the behavior of the real object.</p> 
<p>Unfortunately, the close knit behavior of the mock object and the real object it is mocking does create some additional maintenance on the test system. Failure to maintain the mock conditions can create false positives or false negatives.</p> 
<p>To use a mock, you must first create an instance of the mock object. Once the object is created, you must inject the mock in place of the real object. Effectively, mocks make use of the object oriented concept of polymorphism to ensure that the system under test will not notice any difference between the real and mock object. The mock object will then begin recording any access including method calls, arguments and property access.</p>
<p>The syntax for creating and using a mock depends on the mocking framework used. For this course you will use Mockolate. Mockolate may be used in two ways: either by setting expectations and verifying the expectations were met or by using test spies to replay the calls on a mocked object and verify its expected behavior.</p>
<p>Mock objects can:</p>
<ul>
	<li>Return a value or sequence of values</li>
	<li>Dispatch events</li>
	<li>Call other functions</li>
	<li>Throw Errors</li>
</ul>

<h2>Using Rules</h2>

<p>Rules are objects that implement a specific interface to allow developer extension of the main test runner provided with FlexUnit 4. Using rules, a developer may create reusable test fixtures, evaluate a test differently or add steps into the test evaluation process. You will examine rules in more detail later; however, they are being briefly introduced at this time as the primary way of using Mockolate is through a Rule.</p>
<p>To use a rule, instantiate a public property in a test case, and decorate the property with the [Rule] metadata:</p>

```
[Rule]
public var myRuleThatDoesSomethingCool:MyRule = new MyRule();
```

<h2>Using the MockolateRule</h2>
<p>Included in the Mockolate library is a rule named MockolateRule. It is specifically for use with FlexUnit 4. The Mockolate rule saves many manual steps required to create mocks.</p>
<p>Different mocking frameworks work in different ways. Mockolate works by creating compiled ActionScript code for the required mock object in memory at runtime. This is an extremely effective and useful technique; however, Flash Player does not allow this operation to occur synchronously. Therefore, setting up a Mockolate mock is an asynchronous process. The Mockolate rule manages these details without your intervention.</p>
<p>To use the rule, include the following:</p>

```
[Rule]
public var mockRule:MockolateRule = new MockolateRule();
```

<p>Once the rule is included, you may use a special piece of metadata named [Mock] to create and inject Mockolate mocks.  You will do this in the following walkthrough.</p>

<h2>Walkthrough 1: Adding a Simple Mockolate Rule</h2>

<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Change the Circle class to handle default mock creation.</li>
	<li>Create a new test case to handle mocks.</li>
	<li>Establish a MockolateRule to instantiate mock objects for testing.</li>
</ul>

<h3>Steps</h3>

<ol>
	<li>
		<p>Import the FlexUnit4Training_wt1.fxp project from the Unit 10/Start folder. If you completed the previous walkthrough, choose the option to overwrite your existing project. If this is your first walkthrough, import it as a new project.  Please refer to Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
		<p>The new FlexUnit4Training project contains everything that all the walkthroughs up until this point have accomplished. Additionally, it includes several new files and additional dependencies to help you through the current walkthrough.</p>
		<h3><br />Changing the Circle class</h3>
	</li>
	<li>
		<p>Open the Circle.as file in the src directory in the package net.digitalprimates.math.</p>
	</li>
	<li>
		<p>Take a look at the constructor for the class:</p>

```
public function Circle( origin:Point, radius:Number )
```

<p>Currently, the constructor takes two arguments, a <code>Point</code> and a <code>Number</code>. In this walkthrough, you will be mocking this object. Mock automatically passes default parameters for all arguments. This means origin will be <code>null</code> and the radius will be <code>0</code>. A circle cannot have a <code>null</code> origin or a radius of <code>0</code>, you will need to handle these cases. Later, you will be shown how to pass actual values to the mock.</p>
	</li>
	<li>
		<p>Comment out the range check for radius.</p>

```
// if( ( radius <= 0 || isNaN( radius ) ) {
//	throw new RangeError( "Radius must be a positive Number" );
// }
```

<p>You are commenting this out for simplicity. The default radius of 0 will cause the range error to be thrown. This will prevent that from happening.</p>
	</li>
	<li>
		<p>Immediately after the range check for radius, add a null check for origin. If origin is null, instantiate to a new <code>Point</code> with x and y of 0.</p>

```
if( origin == null ) {
	origin = new Point( 0, 0 );
}
```

<p>Your <code>Circle</code> constructor should now appear as follows:</p>

```
public function Circle( origin:Point, radius:Number ) {
	// if( ( radius <= 0 || isNaN( radius ) ) {
	// 	throw new RangeError( "Radius must be a positive Number" );
	// }
	if( origin == null ) {
		origin = new Point( 0, 0 );
	}
	this._origin = origin;
	this._radius = radius;
}
```

<h3><br />Declare the MockolateRule</h3>
	</li>
	<li>
		<p>Create a new ActionScript class named CircleMockTest in the math.testcases package within the tests directory.</p>
	</li>
	<li>
		<p>Remove the automatically created constructor from the test class.</p>
	</li>
	<li>
		<p>Add and instantiate a public variable named <code>mockRule</code> of type <code>MockolateRule</code> to the CircleMockTest class. Mark the variable with <code>[Rule]</code> metadata.</p>

```
[Rule]
public var mockRule:MockolateRule = new MockolateRule();
```

<p>If you did not use code-completion, add the import for mockolate.runner.MockolateRule at this time.</p>
	</li>
	<li>
		<p>Add a variable named <code>mockCircle</code> of type <code>Circle</code> to the class. Mark the variable with <code>[Mock]</code> metadata.</p>

```
[Mock]
public var mockCircle:Circle;
```

<p><code>[Mock]</code> metadata marks a variable for mock creation and injection. Mock creation is the equivalent of object instantiation within the Mockolate framework. Variables marked with this metadata are prepared for use as Mocks. By using the MockolateRule, this mock will be instantiated with default values prior to any tests being run in the test case.</p>
		<p>If you did not use code-completion, add the import for net.digitalprimates.math.Circle at this time.</p>
	</li>
	<li>
		<p>Add a method named <code>shouldBeNotNull()</code>. It will assert that the <code>mockCircle</code> is not null.</p>

```
[Test]
public function shouldBeNotNull():void {
	assertThat(mockCircle, notNullValue() );
}
```

<p>If you did not use code-completion, add the import statements for org.flexunit.assertThat and org.hamcrest.object.notNullValue at this time.</p>
	</li>
	<li>
		<p>Save CircleMockTest.as.</p>
		<h3><br />Add the CircleMockTest to the CircleSuite</h3>
	</li>
	<li>
		<p>Open the CircleSuite.as file within the math.testcases package. Add a new public variable named <code>test4</code> with a type of <code>CircleMockTest</code></p>

```
[Suite]
[RunWith("org.flexunit.runners.Suite")]
public class CircleSuite {
	public var test1:BasicCircleTest;
	public var test2:CircleConstructorTest;
	public var test3:CircleTheory;
	public var test4:CircleMockTest;
}
```

</li>
	<li>
		<p>Save the CircleSuite.as file.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
		<p>If FlexUnit4Training.mxml ran successfully you should see the following output in your browser window:</p>
		<img alt='TestsPassed' id='shift' src='../images/unit10/image1.png' /> 
		<p class='caption' id='shift'>Figure 1: FlexUnit tests passed</p>
		<p>Notice the failure. This is because you commented out the range check in Circle. The CircleConstructorTest is expecting this error to be thrown. This is an unfortunate side effect of changing the Circle class. This will be corrected when you inject the mock later.</p>
	</li>
</ol>

<h2>Creating a test with mock objects</h2>

<p>Mockolate has logic for two types of mocks: nice and strict.</p>
<ul>
	<li>Nice Mocks only require methods and properties with expectations to be stubbed or mocked. Other methods or properties in the mock object will return fake or generic values that will not throw errors, necessarily.</li> 
	<li>Strict Mocks throw an InvocationError for any method called that is not mocked or stubbed. In other words, the complete functionality of the mock has to be mocked or stubbed in order to test with the mock.</li>
</ul>
<p>By default, Mockolate creates nice, injected mocks.</p>
<p>To create a mock, create the variable as normal and decorate it with the [Mock] metadata.</p>

```
[Mock]
public var myMock:MockableObject;
```

<p>The [Mock] metadata accepts two possible arguments: <code>type</code> and <code>inject</code></p>
<ul>
	<li>type - <code>strict</code> or <code>nice</code></li>
	<li>inject - <code>true</code> or <code>false</code> - If set to false, will prepare (create the ActionScript byte code) the mock but not create it. The mock will need to be instantiated before it is used.  Non-injected mocks are generally only used when the mocked object's constructor has parameters.</li>
</ul>
<p>By default, Mockolate creates a nice, injected mock.</p>
<p>A strict, non-injected mock would appear as:</p>

```
[Mock(type="strict",inject="false")]
public var myMock:MockableObject;
```

<p>Once the mock is created, it can be used in tests.  To use the mock you must <i>stub</i> or <i>mock</i> all methods or properties under test.</p>
<ul>
	<li>stub - specifies optional behaviors or expectations</li>
	<li>mock - specifies required behaviors or expectations</li>
</ul>
<p>Stubbing or mocking is used to define the behavior of the object.  Without stubbing or mocking a method or property will return values of null, false, or some other probably useless value.</p>  
<p>A method or property can be stubbed to:</p>
<ul>
	<li>Return a getter</li>

```
stub(myMock).getter("name").returns("Useful Mock");
```

</ul>
<ul>
	<li>A method without arguments</li>

```
stub(myMock).method("toString").returns("Useful Mock");
```

</ul>
<ul>
	<li>With args</li>

```
stub(myMock).method("add").args(3, 5).returns(8);
```

</ul>
<ul>
	<li>With Hamcrest</li>

```
stub(myMock).method("repeatIt").args(instanceOf(String)).returns(instanceOf(String));
```

</ul>
<p>This list is not exhaustive.  If the real object can do it, the mock can likely simulate the behavior.  All of these approaches can be used with mocks as well to set required behaviors.</p>
<p>Mocks are used to stand-in as complex objects for a given class or methods under test. Furthermore, they are used to isolate the system under test, so extraneous dependencies are not interfering with test execution or expectations.</p>

<h2>Walkthrough 2: Setting Expectations</h2>

<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Create a nice mock expectation.</li>
	<li>Create a strict mock expectation.</li>
</ul>

<h3>Setting nice mock expectations</h3>

<ol>
	<li>
		<p>Open the CircleMockTest.as file from the previous walkthrough.</p>
		<p>Alternatively, if you didn't complete the previous lesson or your code is not functioning properly, you can import the FlexUnit4Training_wt2.fxp project from the Unit 10/Start folder. Please refer to Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
	</li>
	<li>
		<p>Add a new method <code>shouldBeEqualCircleMock()</code> decorated with the test metadata.</p>

```
[Test]
public function shouldBeEqualCircleMock():void {
}
```

</li>
	<li>
		<p>Set an expectation that the <code>origin</code> getter will be called at least once. To set an expectation, you use the <code>mock()</code> method of the <code>MockolateRule</code>. You will also need to use the <code>getter()</code> method.</p>

```
mock( mockCircle ).getter( "origin" ).atLeast( 1 );
```

<p>If you did not use code-completion, import mockolate.mock at this time.</p>
	</li>
	<li>
		<p>Save the CircleMockTest.as file.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
		<p>If FlexUnit4Training.mxml ran successfully you should see the following output in your browser window:</p>
		<img alt='TestsPassed' id='shift' src='../images/unit10/image2.png' /> 
		<p class='caption' id='shift'>Figure 1: FlexUnit tests passed</p>
		<p>You will notice an error. This is because the mock expects the origin getter would be called at least once. However, no call was ever made to the getter. You will need to add this call.</p>
		<p>If you had made this call as a stub rather than a mock, this expectation would have been optional and thus fulfilled.</p>
		<h3><br />Fulfilling the expectation</h3>
		<p>You need to fulfill the expectation. You could make the call directly to origin, but this would defeat the purpose of creating the mock. Mocks require some internal workings of how an object operates. Inside the Circle class is a method called <code>equals()</code>. Equals checks if two circles have the same origin and radius.</p>
	</li>
	<li>
		<p>In <code>shouldBeEqualCircleMock()</code>, but after the mock expectation, create a new <code>Circle</code> called <code>circle</code>. As arguments, pass a new origin at <code>Point( 0, 0 )</code> and a radius of <code>0</code>.</p>

```
var circle:Circle = new Circle( new Point( 0, 0 ), 0 );
```

<p>You are passing this circle because the default mock created an origin at 0, 0 with a radius of 0.</p>
		<p>If you did not use code-completion, add the import for flash.geom.Point.</p>
	</li>
	<li>
		<p>In <code>shouldBeEqualCircleMock()</code>, after the circle is instantiated, make a call to the <code>equals()</code> method of circle. Pass it the <code>mockCircle</code> as a parameter.</p>

```
circle.equals( mockCircle );
```

</li>
	<li>
		<p>Save the CircleMockTest.as file.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
 		<img alt='TestsPassed' id='shift' src='../images/unit10/image4.png' />
		<p class='caption' id='shift'>Figure 2: FlexUnit tests passed</p>
		<p>The expectation has now been fulfilled and the test is now passing. Next you will attempt to use a strict mock.</p>
		<h3><br />Using a strict mock</h3>
	</li>
	<li>
		<p>Add the <code>type="strict"</code> annotation to the Mock metadata of <code>mockCircle</code>.</p>

```
[Mock( type="strict" )]
```

</li>
	<li>
		<p>Save the CircleMockTest.as file.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
		<img alt='TestsPassed' id='shift' src='../images/unit10/image2.png' /> 
		<p class='caption' id='shift'>Figure 3: FlexUnit tests passed</p>
		<p>You will notice another error. Changing the mock to a strict mock requires you to set expectations on all methods and properties that are called as part of the method call.</p>
		<p>If you look at the stack trace you will be able to see the error.</p>
		<img alt='StackTrace' id='shift' src='../images/unit10/image5.png' />
		<p class='caption' id='shift'>Figure 4: Stack Trace</p>
		<p>This error was thrown because no expectation was set for a getter named <code>radius</code>. You will need to add this expectation.</p>
	</li>
	<li>
		<p>At the top of the <code>shouldBeEqualCircleMock()</code>, add the expectation for <code>radius</code> to be called at least once.</p>

```
mock( mockCircle ).getter( "radius" ).atLeast( 1 );
```

</li>
	<li>
		<p>Save the CircleMockTest.as file.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
		<img alt='TestsPassed' id='shift' src='../images/unit10/image4.png' /> 
		<p class='caption' id='shift'>Figure 5: FlexUnit tests passed</p>
	</li>
</ol>

<h2>Individual Verifications</h2>

<p>You may have noticed a problem with verifying a strict mock's behavior. In a strict mock, all properties and methods need to have an expectation set. If a method is called or a property accessed without an expectation the mock will throw an error. Frequently, this will force you to set expectations outside of the scope of the test, and, while this will produce a correct result it may create unnecessary overhead in test production. This also ties the test to a specific implementation of the mocked object; if the object implementation changes but the result of the test remains the same this test will need to be refactored.</p>
<p>In this case, you may use a nice mock. A nice mock is friendly and places none of the requirements on mocking or stubbing every called method. In fact, it will accept any calls placed on it and simply record the behavior. Nice mocks can be tested by setting expectations and verifying results.</p>

<h2>Injecting a Mock Manually</h2>

<p>When injected automatically, mocks pass default parameters to any constructor args, 0 for Number, the empty string for Strings, and null for most other objects. In order to set these constructor arguments to anything else you need to inject the mock manually. Mockolate has a special [Mock] annotation called <code>inject</code> that may be applied to modify mock creation. <code>Inject</code> has two values: <code>true</code> or <code>false</code>. A non-injected mock would appear as:</p>

```
[Mock( inject="false" )]
public var mockPoint:Point;
```

<p>There are two methods to inject a mock, depending on the type, <code>nice()</code> or <code>strict()</code>. The method used must match the mock or an error will be thrown. Both of these take three parameters: the mock, an optional name and optional arguments. Arguments must be specified as an ordered array.</p>
<p>For Point:</p>

```
mockPoint = nice( Point, "myPoint", [ 0, 0 ] );
```

<h2>Walkthrough 3: Injecting mocks</h2>

<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Inject a mock with parameters.</li>
	<li>Set a getter to return a value.</li>
	<li>Revert the changes made to the <code>Circle</code> class in Walkthrough 1.</li>
</ul>

<h3>Steps</h3>

<ol>
	<li>
		<p>Open the CircleMockTest.as file from the previous walkthrough.</p>
		<p>Alternatively, if you didn't complete the previous lesson or your code is not functioning properly, you can import the FlexUnit4Training_wt3.fxp project from the Unit 10/ Start folder. Please refer to Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
		<h3><br />Injecting a mock manually</h3>
	</li>
	<li>
		<p>Modify the current mock <code>Circle</code> to create the mock but not inject. To do so, add the inject annotation to the Mock metadata decorating the <code>mockCircle</code> variable.</p>
		<p>Replace:</p>

```
[Mock( type="strict" )]
Public var mockCircle:Circle;
```

<p>With</p>

```
[Mock( inject="false", type="strict" )
Public var mockCircle:Circle;
```

</li>
	<li>
		<p>Create a new method named <code>setup()</code> decorated with the Before metadata.</p>

```
[Before]
public function setup():void {
}
```

</li>
	<li>
		<p>In the before method, inject a strict mock using the <code>strict()</code> method. Pass this method the <code>Circle</code> class, "<code>mockCircle</code>", and the array <code>[ new Point( 0, 0 ), 1  ]</code>. Assign the return for <code>strict()</code> to the <code>mockCircle</code> instance.</p>

```
[Before]
public function setup():void {
mockCircle = strict( Circle, "mockCircle", [ new Point( 0, 0 ), 1 ] );
}
```

<p>Since the constructor arguments are now being passed, the range test in the <code>Circle</code> constructor is now satisfied. However, the getters will still return default values. Mocks do not use any of the instance properties. You will need to mock the getters for <code>radius</code> and <code>origin</code> to return values.</p>
		<p>If you did not use code-completion, add the import for mockolate.strict.</p>
	</li>
	<li>
		<p>Replace the lines:</p>

```
mock( mockCircle ).getter( "origin" ).atLeast( 1 );
mock( mockCircle ).getter( "radius" ).atLeast( 1 );
```

<p>with</p>

```
mock( mockCircle ).getter( "origin" ).returns( new Point( 0, 0 ) ).atLeast( 1 );
mock( mockCircle ).getter( "radius" ).returns( 1 ).atLeast( 1 );
```

<p>This will force the getter and setter to return the required values.</p>
		<p>If you were to run the test now, it would pass with no errors. However, this does not fulfill the test "shouldBeEqualCircleMock"</p>
	</li>
	<li>
		<p>Change the radius of the circle you instantiate, from a value of 0, to a value of 1.</p>

```
var circle:Circle = new Circle( new Point( 0, 0 ), 1 );
```

<p>We want to compare that this circle is equal to our mock circle, so they should both have the same radius.</p>
	</li>
	<li>
		<p>Replace the line:</p>

```
circle.equals( mockCircle );
```

<p>With</p>

```
assertTrue( circle.equals( mockCircle ) );
```

<p>If you did not use code-completion, add the import for org.flexUnit.asserts.assertTrue</p>
		<h3><br />Revert the changes to the Circle class.</h3>
	</li>
	<li>
		<p>Open the net.digitalprimates.math.Circle.as file in the src directory.</p>
		<p>In Walkthrough 1, you modified the <code>Circle</code> class to accept invalid parameters so that you could use mocks. You will now need to revert these changes.</p>
	</li>
	<li>
		<p>Uncomment the following section:</p>

```
// if ( ( radius <= 0 ) || isNaN( radius ) ) {
//	throw new RangeError("Radius must be a positive Number");
// }
```

</li>
	<li>
		<p>Remove the origin null check. This section is no longer required.</p>
		<p>Remove:</p>

```
if( origin == null ) {
	origin = new Point( 0, 0 );
}
```

</li>
	<li>
		<p>Save the Circle.as file.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
		<img alt='TestsPassed' id='shift' src='../images/unit10/image6.png' />
		<p class='caption' id='shift'>Figure 1: FlexUnit tests passed</p>
		<p>The mock is now acting as a stand in of a Circle, accepting parameters and returning the values you have requested. In this way, any object can be mocked to operate exactly as expected, or to test that the expected behaviors are called on the object. In the next walkthrough, you will learn how to mock methods to accept parameters and return values.</p>	
	</li>
</ol>

<h2>Walkthrough 4: Method Mocking</h2>

<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Mock the <code>Point</code> class.</li>
	<li>Mock the <code>add()</code> method of the <code>Point</code> class.</li>
</ul>

<h3>Steps</h3>

<ol>
	<li>
		<p>Create a new ActionScript class named DistanceTest in the math.testcases package within the tests directory.</p>
		<p>Alternatively, if you didn't complete the previous lesson or your code is not functioning properly, you can import the FlexUnit4Training_wt4.fxp project from the Unit 10/ Start folder. Please refer to Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
		<h3><br />Create the test fixture</h3>
	</li>
	<li>
		<p>Remove the automatically created constructor from the test class.</p>
	</li>
	<li>
		<p>Create a private static constant named <code>TOLERANCE</code> as a <code>Number</code>, with a value of <code>.01</code>.</p>

```
private static const TOLERANCE:Number = .01;
```

</li>
	<li>
		<p>Add and instantiate a public variable named <code>mockRule</code> of type <code>MockolateRule</code> to the <code>LayoutTest</code> class. Mark the variable with <code>[Rule]</code> metadata.</p>

```
[Rule]
public var mockRule:MockolateRule = new MockolateRule();
```

<p>If you did not use code-completion, add the import for mockolate.runner.MockolateRule at this time.</p>
	</li>
	<li>
		<p>Add a variable named <code>mockPoint</code> of type <code>Point</code> to the class. Mark the variable with <code>[Mock]</code> metadata.</p>

```
[Mock]
public var mockPoint:Point;
```

<p>If you did not use code completion, add the import for flash.geom.Point at this time.</p>
		<h3><br />Mocking a method</h3>
	</li>
	<li>
		<p>Create a new test method named <code>shouldBeEqualDistance()</code>. Decorate it with <code>[Test]</code> metadata.</p>

```
[Test]
public function shouldBeEqualDistance():void {
}
```

</li>
	<li>
		<p>Create a new <code>Point</code> called <code>testPoint</code> with x and y values of 5 and 5, respectively.</p>

```
var testPoint:Point = new Point( 5, 5 );
```

</li>
	<li>
		<p>Create a new <code>Point</code> called <code>endPoint</code> with x and y values of 10 and 10, respectively.</p>

```
var endPoint:Point = new Point( 10, 10 );
```

</li>
	<li>
		<p>Create one last <code>Point</code> called <code>summedPoints</code>. Do not instantiate it at this time.</p>

```
var summedPoints:Point;
```

</li>
	<li>
		<p>Add an expectation for the <code>add()</code> method of <code>mockPoint</code> to take an argument, <code>testPoint</code>, and return a new <code>Point</code> with x and y values of 10 and 10.</p>

```
mock( mockPoint ).method( "add" ).args( testPoint ).returns( new Point( 10, 10 ) );
```

<p>The mock will now expect the <code>add()</code> method of <code>mockPoint</code> to be called with the <code>testPoint</code> parameter. When it does, it will return a <code>Point</code> with x and y values of 10 and 10.</p>
	</li>
	<li>
		<p>Make a call to <code>mockPoint.add()</code> passing it the parameter <code>testPoint</code> and assigning the return to <code>summedPoints</code>.</p>

```
summedPoints = mockPoint.add( testPoint );
```

</li>
	<li>
		<p>Add a call to <code>assertThat()</code> passing it the parameters <code>endPoint</code> and a new <code>CloseToPointMather</code> with parameters <code>summedPoints</code> and <code>TOLERANCE</code>.</p>

```
assertThat( endPoint, new CloseToPointMatcher( summedPoints, TOLERANCE ) );
```

<p>If you did not use code completion, add the imports for org.flexunit.assertThat and matcher.CloseToPointMatcher at this time.</p>
	</li>
	<li>
		<p>Save the DistanceTest.as file.</p>
		<h3><br />Add DistanceTest to the test suite</h3>
	</li>
	<li>
		<p>Open the CircleSuite.as file within the math.testcases package. Add a new public variable named <code>test5</code> with a type of <code>DistanceTest</code></p>

```
[Suite]
[RunWith("org.flexunit.runners.Suite")]
public class CircleSuite {
	public var test1:BasicCircleTest;
	public var test2:CircleConstructorTest;
	public var test3:CircleTheory;
	public var test4:CircleMockTest;
	public var test5:DistanceTest;
}
```

</li>
	<li>
		<p>Save the CircleSuite.as file.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
		<img alt='TestsPassed' id='shift' src='../images/unit10/image7.png' />
		<p class='caption' id='shift'>Figure 1: FlexUnit tests passed</p>
	</li>
</ol>

<h2>Summary</h2>

<ul>
	<li><p>Mocks are created and plugged in to test cases to further isolate test cases and classes validating their functionality with minimal external dependency.</p></li>
	<li><p>The mocking framework that this course uses is Mockolate. Mockolate features:</p></li>
	<ul>
		<li><p>Nice mocks</p></li>
		<li><p>Strict mocks</p></li>
		<li><p>Mocks instantiated with a custom FlexUnit rule</p></li>
		<li><p>Can be run alongside other FlexUnit custom runners</p></li>
	</ul>
	<li><p>When a MockolateRule is used, mocks are instantiated as <code>nice</code> by default.</p></li>
	<li><p>Setting expectations for <code>strict</code> mocks requires an expectation for every method that will be run.</p></li>
	<li><p>Expectations can be verified individually on nice mocks, which allows for greater specificity.</p></li>
</ul>

<h2>Navigation</h2>
<ul>
    <li><a href="Unit-9.html">Unit 9 - External Data</a></li>
    <li><a href="Unit-11.html">Unit 11 - Parameterized Tests</a></li>
    <li><a href="../index.html">Table of Contents / Introduction</a></li>
</ul>