Title:  Unit 4 - FlexUnit Basics

<a href="../code/Unit4.zip"><img src="../images/DownloadIcon.png" alt="Download" /> Download Unit Project Files</a>

<p>FlexUnit 4.x tests are simply methods that are decorated with special metadata. They setup conditions, execute code and make assertions about expected results.</p> 
<p>These methods are grouped together by common themes and executed often to ensure consistent, functional code.</p>

<h3>Objectives:</h3>

<p>After completing this lesson, you should be able to:</p>
<ul>
	<li>Explain the basics of unit tests and test metadata</li>
	<li>Perform basic assertions in multiple unit tests</li>
</ul>

<h3>Topics</h3>

<p>In this unit, you will learn about the following topics:</p>
<ul>
	<li>Testing a unit</li>
	<li>Examining a test</li>
	<li>Creating tests</li>
</ul>

<h2>Testing a Unit</h2>

<h3>Understanding a Unit</h3>

<p>A unit is the smallest block of code that can be isolated and tested independently.</p> 
<p>Suppose you had a table lamp that was not working properly. You might first try changing the light bulb to see if that fixes the issue. Perhaps you would try that light bulb in another fixture or move the lamp to another room to try another outlet. These are all steps to isolate a problem and realizations of the idea behind testing units.</p>
<p>When the lamp is fully assembled with light bulb in place and plugged into the wall it is not possible to definitively see where an issue might exist. There could be an issue with the outlet, cord, switch, wiring or bulb. You can only begin to determine the problem by isolating one system at a time and testing it. If you could not change the light bulb, move the lamp, or disassemble it, your ability to test would only be at the whole system level. This likely means you throw away your lamp each time the bulb burns out.</p>
<p>While a lamp is a relatively simple example, the average class in your application is likely not. To be able to test a class properly, you need to ensure it is capable of being isolated and then test each piece in an isolated fashion.</p>
<p>To successfully unit test a class, you will:</p>
<ul>
	<li>Generally write at least one test for each method of a class.</li>
	<li>Write one test for every possible outcome of a method with conditional logic.</li>
	<li>Test both valid/expected and invalid/unexpected input to your methods.</li>
</ul>
<p>This is only possible if you can test one isolated object at a time.</p>

<h3>The Need for Seams</h3>

<p>We call the point where objects can be separated from each other seams. Continuing with the lamp example, the light bulb socket and wall plug are both seams. Seams allow us to isolate an object and ensure that we are only testing one object at a time.</p>
<p>Let's examine the following piece of code:</p>

```
public class TestSeams {
	private var someMathObj:SomeMathClass = new SomeMathClass();
	public function findDistance( a:Number, b:Number ):Number {
		var difference:Number = ( b - a );
		var distance:Number = someMathObj.abs( difference );
		return distance;
	}
}
```

<p>Unfortunately, you cannot test the <code>findDistance()</code> method alone. That method depends upon <code>SomeMathClass</code> and its <code>abs()</code> function. Therefore, it cannot be isolated. Any attempt to test this method results in testing the code in the TestSeams class, an unknown amount in the SomeMathClass, and the integration between the classes.</p>
<p>When creating new code you must keep the need for seams in mind. Not only does it facilitate testable code, it also has the side effect of creating loosely coupled code with a minimum of interwoven dependencies.</p>

<h3>Examining the Circle Class and the Circle Layout</h3>

<p>Let's take a look at the Circle class and the <code>getPointOnCircle()</code> method.</p>

```
public function getPointOnCircle( t:Number ):Point {
	var x:Number = origin.x + ( radius * Math.cos( t ) );
	var y:Number = origin.y + ( radius * Math.sin( t ) );
	return new Point( x, y );
}
```

<p>In this unit of code:</p>
<ul>
	<li>The method takes a single argument, <code>t</code>.</li>
	<li>The method calculates the point on a circle using the <code>origin</code> and <code>radius</code> properties.</li>
	<li>The <code>origin</code> and <code>radius</code> properties are set in the Circle class constructor.</li>
	<li>The method depends upon the static Math class, which is unfortunate. It could use better seams but is typical of methods you might encounter in real world classes.</li>
</ul>
<p>A unit test for <code>getPointOnCircle()</code> would need to test:</p>
<ul>
	<li>For a given t, origin, and radius, does the returned point match what is expected?</li>
	<li>What happens when invalid values of t, origin, and radius are provided to this method?</li>
</ul>
<p>As you can see, most units will need more than one test to fully validate the unit's function.</p>
<p>Here is a unit from the CircleLayout class:</p>

```
public function getLayoutRadius( contentWidth:Number, contentHeight:Number ):Number {
	var maxX:Number = (contentWidth/2)*.8;
	var maxY:Number = (contentHeight/2)*.8;
	var radius:Number = Math.min( maxX, maxY );
	return Math.max( radius, 1 );
}
```

<p>While not perfect, this unit has a couple testing advantages over the previous method:</p>
<ul>
	<li>All the required values for the computation are passed as arguments.</li>
	<li>Save for the Math class, this method has no external dependencies.</li>
</ul>
<p>A unit test for this method would need to test:</p>
<ul>
	<li>For a given set of <code>contentWidth</code> and a <code>contentHeight</code>, is the proper value returned?</li>
	<li>If an invalid <code>contentWidth</code> or <code>contentHeight</code>, does the expected behavior occur?</li>
</ul>

<h3>Examining a Test Case</h3>

<p>As you learned in the previous unit, a test case is merely a collection of individual test methods that test related behaviors. Each of the methods of a test case is decorated with [Test] metadata to signal to the FlexUnit framework that a method is a test. The [Test] metadata tag can also accept user defined attributes for descriptive and organizational purposes</p>

```
[Test(description="Test is supposed to fail",issueID="0012443")]

public function thisTestFails():void {}
```

<p>or system-defined attributes that provide further instructions to FlexUnit when it runs the test.</p>
<p>A test fixture comprises all of the state required to evaluate your test methods. For example, in the following test:</p>

```
[Test]
public function shouldComputeCorrectDiameter():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 ); 
	assertEquals( 10, circle.diameter );
}
```

<p>The existence of the Circle is necessary for the assertion and can therefore be considered part of your test fixture.</p>

<h2>Walkthrough 1: Adding Tests to a Case</h2>

<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Add tests to the BasicCircleTest case.</li>
	<li>Run the case with the new tests.</li>
</ul>

<h3>Steps</h3>

<ol>
	<li>
		<p>Open the BasicCircleTest.as file from the previous exercise.</p>
		<p>Alternatively, if you didn't complete the previous lesson or your code is not functioning properly, you can import the FlexUnit4Training_wt1.fxp project from the Unit 4/Start folder. Please refer to Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
	</li>
	<li>
		<p>Add seven new public functions to the class right under the <code>shouldReturnProvidedRadius()</code> function. They should be named <code>shouldComputeCorrectDiameter()</code>, <code>shouldReturnProvidedOrigin()</code>, <code>shouldReturnTrueForEqualCircle()</code>, <code>shouldReturnFalseForUnequalOrigin()</code>, <code>shouldReturnFalseForUnequalRadius()</code>, <code>shouldGetPointsOnCircle()</code>, and <code>shouldThrowRangeError()</code>.</p>
		<p>Mark each function with <code>[Test]</code> metadata.</p>
		```
		[Test]
public function shouldComputeCorrectDiameter():void {
}
[Test]
public function shouldReturnProvidedOrigin():void {
}
[Test]
public function shouldReturnTrueForEqualCircle():void {	
}
[Test]
public function shouldReturnFalseForUnequalOrigin():void {
}
[Test]
public function shouldReturnFalseForUnequalRadius():void {
}
[Test]
public function shouldGetPointsOnCircle():void {	
}
[Test]
public function shouldThrowRangeError():void {	
}
```

</li>
	<li>
		<p>Although you have written out seven methods, only the first five are going to be used in this walkthrough. Within <code>shouldComputeCorrectDiameter()</code>, <code>shouldReturnProvidedOrigin()</code>, <code>shouldReturnTrueForEqualCircle()</code>, <code>shouldReturnFalseForUnequalOrigin()</code>, and <code>shouldReturnFalseForUnequalRadius()</code> declare a local variable named <code>circle</code> of type <code>Circle</code>. Instantiate <code>circle</code> with an origin Point at <code>(0, 0)</code> and a radius of <code>5</code>.</p>
		```
		[Test]
public function shouldComputeCorrectDiameter():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 ); 
}
[Test]
public function shouldReturnProvidedOrigin():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 ); 
}
[Test]
public function shouldReturnTrueForEqualCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );	
}
[Test]
public function shouldReturnFalseForUnequalOrigin():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );	
}
[Test]
public function shouldReturnFalseForUnequalRadius():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );	
}
```

</li>
	<li>
		<p>Just below the circle instantiation in the <code>shouldComputeCorrectDiameter()</code> method, add a line that calls to the <code>assertEquals()</code> method with arguments <code>10</code> and <code>circle.diameter</code>.</p> 
		```
		[Test]
public function shouldComputeCorrectDiameter ():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 ); 
	assertEquals( 10, circle.diameter );
}
```

</li>
	<li>
		<p>Just below the circle instantiation in the <code>shouldReturnProvidedOrigin()</code> method, add two lines that each call the <code>assertEquals()</code> method. This test requires two assertion statements because it will check the x and y coordinates' equality to 0, each on a different line.</p>
		```
		[Test]
public function shouldReturnProvidedOrigin():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 ); 
	assertEquals( 0, circle.origin.x );
	assertEquals( 0, circle.origin.y );
}
```

</li>
	<li>
		<p>Add a new local variable named <code>circle2</code> of type <code>Circle</code> to the <code>shouldReturnTrueForEqualCircle()</code> method. Instantiate <code>circle2</code> with an origin of <code>(0, 0)</code> and radius of <code>5</code>.</p>
		```
		[Test]
public function shouldReturnTrueForEqualCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var circle2:Circle = new Circle( new Point( 0, 0 ), 5 );
}
```

</li>
	<li>
		<p>Add a new line to the <code>shouldReturnTrueForEqualCircle()</code> function that calls to the <code>assertTrue()</code> method with the argument <code>circle.equals( circle2 )</code>.</p>
		```
		[Test]
public function shouldReturnTrueForEqualCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var circle2:Circle = new Circle( new Point( 0, 0 ), 5 );
	assertTrue( circle.equals( circle2 ) );	
}
```

<p>If you did not use code-completion, add the import for org.flexunit.asserts.assertTrue at this time.</p>	
	</li>
	<li>
		<p>Add a variable named <code>circle2</code> of type <code>Circle</code> to the <code>shouldReturnFalseForUnequalOrigin()</code> method. Instantiate <code>circle2</code> with an origin of <code>(0, 5)</code> and a radius of <code>5</code>.</p>
		```
		[Test]
public function shouldReturnFalseForUnequalOrigin():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var circle2:Circle = new Circle( new Point( 0, 5 ), 5 );
}
```

</li>
	<li>
		<p>Add a call to the <code>assertFalse()</code> method. The statement <code>circle.equals( circle2 )</code> should be passed in as its argument.</p>
		```
		[Test]
public function shouldReturnFalseForUnequalOrigin():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var circle2:Circle = new Circle( new Point( 0, 5 ), 5);
	assertFalse( circle.equals( circle2 ) );
}
```

<p>If you did not use code-completion, add the import for org.flexunit.asserts.assertFalse at this point.</p>
	</li>
	<li>
		<p>Add a variable named <code>circle2</code> of type <code>Circle</code> to the <code>shouldReturnFalseForUnequalRadius()</code> method. Instantiate <code>circle2</code> with an origin of <code>(0, 0)</code> and a radius of <code>7</code>.</p>
		```
		[Test]
public function shouldReturnFalseForUnequalRadius():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var circle2:Circle = new Circle( new Point( 0, 0 ), 7);
}
```

</li>
	<li>
		<p>Add a call to the <code>assertFalse()</code> method. The expression <code>circle.equals( circle2 )</code> should be passed in as its argument.</p>
		```
		[Test]
public function shouldReturnFalseForUnequalRadius():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var circle2:Circle = new Circle( new Point( 0, 0 ), 7);
	assertFalse( circle.equals( circle2 ) );
}
```

</li>
	<li>
		<p>Save the BasicCircleTest.as file.</p>
	</li>
	<li>
		<p>Re-Open the FlexUnit4Training.mxml file. Click the run button in the upper toolbar.</p>		<p>If FlexUnit4Training.mxml ran successfully you should see the following output in your browser window:</p>
		<img alt='UnitsPassed' id='shift' src='../images/unit4/image1.png' />
		<p class='caption' id='shift'>Figure 1: FlexUnit tests passed.</p>
	</li>
</ol>

<h2>Creating tests</h2>

<h3>Examining a passing test</h3>

<p>In the section above you used assertions to establish several passing tests. Many took the same form as this simple math example:</p>

```
[Test]
public function testAddition():void {
	var num1:Number = 5;
	var num2:Number = 3;
	var total:Number = 8;
	assertEquals( total, num1 + num2 );
}
```

<ul>
	<li>5 and 3 are expected to add up to 8.</li>
	<li>The tests <b>assert</b> this expectation using the <code>assertEquals()</code> method</li>
	<li>Since 5 + 3 == 8, this test will pass.</li>
</ul>

<h3>Performing basic assertions</h3>

<p>Assertions allow us to specify expected conditions that help determine whether a test passes or fails. They are statements that evaluate to true or false. If you make an assertion that is false under the conditions you have set up, the test fails. If all of the assertions in the test are true, the test passes.</p>
<p>FlexUnit provides many assertion methods to use in your test methods. You might use an assertion to state that you expect a value to be true or false, null or not null, equal to another variable, or simply not in a list of acceptable values.</p>
<p>The three most common assertions in FlexUnit 4 are:</p>
<ul>
	<li><code>assertTrue( condition:Boolean )</code> - Asserts that a condition is true. This passes if the condition is true.</li>
	<li><code>assertFalse( condition:Boolean )</code> - Asserts that a condition is false. This passes if the condition is false.</li>
	<li><code>assertEquals( value1:Obj, value2:Obj )</code> - Asserts that two objects are equal. This passes if value1 == value2.</li>
</ul>
<p>If all of the assertion in a test evaluate correctly, that test is added to the list of passed tests. If any assertion fails, it throws an <code>AssertionError</code>. FlexUnit handles the error and adds the test to a list of failed tests.</p>
 
<h3>Examining a failed test</h3>

<p>Consider this slightly modified <code>testAddition()</code> method:</p>

```
[Test]
public function testAddition():void {
	var num1:Number = 5;
	var num2:Number = 3;
	var total:Number = 1000;
	assertEquals( total, num1 + num2 );
}
```

<ul>
	<li>The test asserts that 5 + 3 == 1000.</li>
	<li>This test will fail as your assertion is false.</li>
</ul>
<p>FlexUnit 4.x catches the <code>AssertionError</code> that is thrown when this test fails, and the following result is displayed within the FlexUnit Results window in Flash Builder:</p>

```
"expected: <1000> but was: <8>"

```

<p>A test will be registered as a failure after the <i>first</i> instance of a failed assertion.  FlexUnit will not continue to evaluate the other assertions in the method. This means that if multiple assertions are written in a single test you will only receive notification of the first failure and the others will be in an unknown state.</p>

```
[Test]
public function testAddition():void {
		var num1:Number = 5;
		var num2:Number = 3;
		var total:Number = 1000;
		assertEquals( total, num1 + num2 );
		assertEquals( num1 + num2, total );
}
```

<p>For example, both of these assertions would fail given the opportunity.  However, as soon as FlexUnit catches the first failed assertion it stops the test.  The second assert will never be called.  The error message would read:</p>

```
"expected: <1000> but was: <8>"

```

<p>With multiple assertions in a test, a test can fail for one of several reasons. In diagnosis, it is not immediately clear what caused the test to fail.</p>
 
<h2>Walkthrough 2: Failing and Passing Assertions</h2>
<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Add multiple assertions to a test method.</li>
	<li>Run the test case.</li>
</ul>

<h3>Steps</h3>

<ol>
	<li>
		<p>Open the BasicCircleTest.as file from the previous exercise.</p>
		<p>Alternatively, if you didn't complete the previous lesson or your code is not functioning properly, you can import the FlexUnit4Training_wt2.fxp project from the Unit 4/Start folder. Please refer to Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
		<h3>Write multiple assertions for a test method</h3>
	</li>
	<li>
		<p>In the <code>shouldGetPointsOnCircle()</code> method, create a local variable named <code>circle</code> of type <code>Circle</code>. This Circle should be instantiated with an origin of <code>(0, 0)</code> and a radius of <code>5</code>.</p>

```
[Test]
public function shouldGetPointsOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
}
```

</li>
	<li>
		<p>Declare a variable named <code>point</code> of type <code>Point</code>.</p>

```
[Test]
public function shouldGetPointsOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point;
}
```

</li>
	<li>
		<p>Set the point variable equal to the return value of <code>circle.getPointOnCircle( 0 )</code>. Above that line, it is useful to add a comment line specifying that this is the top-most point of circle.</p>

```
[Test]
public function shouldGetPointsOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point;
	//top-most point of circle
	point = circle.getPointOnCircle( 0 );
}
```

<p>The <code>getPointOnCircle( t:Number )</code> returns a Circle object's point that corresponds to the radians passed in as the <code>t</code> parameter.</p>
	</li>
	<li>
		<p>Add two new lines, each with a call to the <code>assertEquals()</code> method. The first takes the arguments <code>5</code> and <code>point.x</code>. The second takes the arguments <code>0</code> and <code>point.y</code>.</p>

```
[Test]
public function shouldGetPointsOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point;
	//top-most point of circle
	point = circle.getPointOnCircle( 0 );
	assertEquals( 5, point.x );
	assertEquals( 0, point.y );
}
```

</li>
	<li>
		<p>Copy these four new lines of code and paste them into the bottom of the function.</p> 
		<p>Alter the comment in this section to read <code>//bottom-most point on circle</code>.</p>
	</li>
	<li>
		<p>Replace the <code>0</code> in <code>circle.getPointOnCircle( 0 )</code> with <code>Math.PI</code>.</p>
	</li>
	<li>
		<p>Update the <code>assertEquals()</code> statements to <code>assertEquals( -5, point.x )</code> and <code>assertEquals( 0, point.y )</code>.</p>
		<p>This should create a duplicate set of assertions for the bottom-most point on the circle.</p>

```
[Test]
public function shouldGetPointsOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point;
	//top-most point of circle
	point = circle.getPointOnCircle( 0 );
	assertEquals( 5, point.x );
	assertEquals( 0, point.y );
	//bottom-most point of circle
	point = circle.getPointOnCircle( Math.PI );
	assertEquals( -5, point.x );
	assertEquals( 0, point.y );
}
```

</li>
	<li>
		<p>Save BasicCircleTest.as.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
		<p>If FlexUnit4Training.mxml ran successfully you should see the following output in your browser window:</p>
		<img alt='SingleTestFailure' id='shift' src='../images/unit4/image2.png' />
		<p class='caption' id='shift'>Figure 1: A single test failure</p>
		<p>One of the tests in the case has failed. Further evaluation within the FlexUnit results tab will show that the newly populated <code>shouldGetPointsOnCircle()</code> method failed.</p>
		<p>While you know that this test failed, you do not know the failure. Fewer assertions in a method provide you better context to understand the root cause quickly.</p>
		<p>If you examine the failure in Flash Builder, you will see that the root cause actually has to do with the imprecision of floating point calculations. You will learn to adapt your code to these issues in future lessons.</p>
		<img alt='FailureStackTrace' id='shift' src='../images/unit4/image3.png' />
		<p class='caption' id='shift'>Figure 2: The failure stack trace </p>
	</li>
</ol>

<h2>Understanding multiple assertions</h2>

<p>As you have previously learned, FlexUnit 4.x marks a test as a failure after the first failed assertion. Therefore a test with many assertions provides a limited amount of useful information as to the root cause of the failure.</p>
<p>The <code>shouldGetPointsOnCircle()</code> method already has too many assertions to be useful. Furthermore, to test it effectively you still need additional data points along the circle.</p>
<p>The way to resolve this issue is to refactor these test cases into multiple small cases which provide useful context during a failure. Each test will make a single assertion. Subsequently, if a single test fails, a single assertion has failed.</p>

<h2>Walkthrough 3: Factoring into Multiple Test Methods</h2>

<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Refactor a test method with multiple assertions.</li>
</ul>

<h3>Steps</h3>

<ol>
	<li>
		<p>Open the BasicCircleTest.as file from the previous exercise.</p>
		<p>Alternatively, if you didn't complete the previous lesson or your code is not functioning properly, you can import the FlexUnit4Training_wt3.fxp project from the Unit5/Start folder. Please refer to Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
		<h3>Refactor point tests</h3>
	</li>
	<li>
		<p>Replace the <code>shouldGetPointsOnCircle()</code> function with two new public functions in the <code>BasicCircleTest</code> class. These functions will be named so that each clearly represents the point on the circle being tested.</p>

```
[Test]
public function shouldGetTopPointOnCircle():void {
}
[Test]
public function shouldGetBottomPointOnCircle():void {
}
```

<p>Although the original <code>shouldGetPointsOnCircle()</code> method contained tests for only the top and bottom points of the Circle, in this Walkthrough you will add tests for the left and right points as well.</p>
	</li>
	<li>
		<p>Add two new methods named <code>shouldGetRightPointOnCircle()</code> and <code>shouldGetLeftPointOnCircle()</code>.</p>
	</li>
	<li>
		<p>Copy the instantiation of the circle, and declaration of the point from the <code>shouldgetPointsOnCircle()</code> method into each of the four new methods.</p>

```
[Test]
public function shouldGetTopPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point;
}
[Test]
public function shouldGetBottomPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point;
}
[Test]
public function shouldGetRightPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point;
}
[Test]
public function shouldGetLeftPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point;
}
```

</li>
	<li>
		<p>Copy the block of code following <code>//top-most point of circle</code> comment into the <code>shouldGetTopPointOnCircle()</code> method.  Copy the block following the <code>//bottom-most point of circle</code> comment into the <code>shouldGetBottomPointOnCircle()</code> method.</p>

```
[Test]
public function shouldGetTopPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point;
	//top-most point of circle 
	point = circle.getPointOnCircle( 0 );
	assertEquals( 5, point.x );
	assertEquals( 0, point.y );
}
[Test]
public function shouldGetBottomPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point;
	//bottom-most point of circle
	point = circle.getPointOnCircle( Math.PI );
	assertEquals( -5, point.x );
	assertEquals( 0, point.y );
}
```

<p>Because the methods each test specific points, the name is clear enough to distinguish one test from another. Remove the comment fields in each of the tests accordingly.</p>
	</li>
	<li>
		<p>Modify the <code>shouldGetTopPointOnCircle()</code> and <code>shouldGetBottomPointOnCircle()</code> methods to save space by instantiating the point variable as it is declared in each function, much like the circle variable. The comments can be removed as well.</p>

```
[Test]
public function shouldGetTopPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point = circle.getPointOnCircle( 0 );
	assertEquals( 5, point.x );
	assertEquals( 0, point.y );
}

[Test]
public function shouldGetBottomPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point = circle.getPointOnCircle( Math.PI );
	assertEquals( -5, point.x );
	assertEquals( 0, point.y );
}
```

</li>
	<li>
		<p>In the <code>shouldGetRightPointOnCircle()</code> method, set the <code>point</code> variable to <code>circle.getPointOnCircle( Math.PI/2 )</code>.</p>

```
[Test]
public function shouldGetRightPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point = circle.getPointOnCircle( Math.PI/2 );
}
```

</li>
	<li>
		<p>In the <code>shouldGetLeftPointOnCircle()</code> method, set the <code>point</code> variable to <code>circle.getPointOnCircle( (3*Math.PI)/2 )</code>.</p>

```
[Test]
public function shouldGetLeftPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point = circle.getPointOnCircle( (3*Math.PI)/2 );
}
```

</li>
	<li>
		<p>Add two calls to the <code>assertEquals()</code> method to <code>shouldGetRightPointOnCircle()</code>. One should assert that <code>point.x</code> is equal to <code>5</code>, and <code>point.y</code> is equal to <code>0</code>.</p>


```
[Test]
public function shouldGetRightPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point = circle.getPointOnCircle( Math.PI/2 );
	assertEquals( 0, point.x );
	assertEquals( 5, point.y );
}
```

</li>
	<li>
		<p>Add two calls to the <code>assertEquals()</code> method to <code>shouldGetLeftPointOnCircle()</code>. One should assert that <code>point.x</code> is equal to <code>0</code>, and <code>point.y</code> is equal to <code>-5</code>.</p>

```
[Test]
public function shouldGetLeftPointOnCircle():void {
	var circle:Circle = new Circle( new Point( 0, 0 ), 5 );
	var point:Point = circle.getPointOnCircle( (3*Math.PI)/2 );
	assertEquals( 0, point.x );
	assertEquals( -5, point.y );
}
```

</li>
	<li>
		<p>Delete the previously used <code>shouldGetPointsOnCircle()</code> method, because its tests have been replicated.</p>
	</li>
	<li>
		<p>Save the BasicCircleTest.as file.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
		<p>If FlexUnit4Training.mxml ran successfully you should see the following output in your browser window:</p>
		<img alt='ThreeTestsFailed' id='shift' src='../images/unit4/image4.png' />
		<p class='caption' id='shift'>Figure 1: Three tests failed.</p>
		<p>Previously, when all the point assertions were bundled into a single method, it was impossible to determine which assertions caused the failure. Refactoring these assertions into four different test methods makes it clear that three of the four conditions are failures.</p> 
		<p>Further inspection will reveal that the <code>shouldGetTopPointOnCircle()</code> is the only of the four tests that is passing. It is also the only test with an integer value of 0 passed in for its radians argument. The root cause of the situation is the way in which Flash Player handles fractions and the precision of those comparisons. This will be explored further and a solution demonstrated later in this text.</p>
	</li>
</ol>

<h2>Summary</h2>
<ul>
	<li>A unit is the smallest piece of code that can be isolated and tested independently</li>
	<li>Code with seams is testable. Code without seams is impossible to truly unit test.</li>
	<li>You can create seams in your code by:</li>
	<ul>
		<li>Passing dependencies as arguments</li>
		<li>Limiting the use of static classes or global state</li>
	</ul>
	<li>Test cases are collections of test methods</li> 
	<li>A test without a failing assertion is a passing test</li>
	<ul>
		<li>This means an empty tests is a passing test</li>
	</ul>
	<li>Common assertions in FlexUnit include:</li>
	<ul>
		<li><code>assertEquals()</code></li>
		<li><code>assertTrue()</code></li>
		<li><code>assertFalse()</code></li>
	</ul>
	<li>Any failing assertion within a test causes the test to cease execution and fail</li>
	<li>When a test with multiple assertions fails, it is difficult to narrow down which assertion failed.</li>
</ul>
        
<h2>Navigation</h2>
<ul>
    <li><a href="Unit-3.html">Unit 3 - FlexUnit Capabilities</a></li>
    <li><a href="Unit-5.html">Unit 5 - Developing Static Tests</a></li>
    <li><a href="../index.html">Table of Contents / Introduction</a></li>
</ul>
