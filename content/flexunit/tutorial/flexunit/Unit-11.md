Title:  Unit 11 - Parameterized Tests

<a href="../code/Unit11.zip"><img src="../images/DownloadIcon.png" alt="Download" /> Download Unit Project Files</a>

<p>Much like theories, parameterized tests take a number of possible values and apply them to methods in the test case. Unlike theories, however, parameterized tests pass or fail individually, meaning that one specific data point can fail while the others pass. While Theories are best used for reversible or mathematical concepts, parameterized testing allows for passing a series of arguments and expected results to every test in a case, some tests in a case, or each to a unique test in the case.</p>
 
<h3>Objectives:</h3>

<p>After completing this lesson, you should be able to:</p>
<ul>
	<li>Create TestNG style parameterized tests</li>
	<li>Create JUnit style parameterized tests</li>
	<li>Use complex objects in parameterized tests</li>
	<li>Use external data to load simple and complex objects for parameterized testing</li>
</ul>

<h3>Topics</h3>

<p>In this unit, you will learn about the following topics:</p>
<ul>
	<li>Use case differences from theories</li>
	<li>Declaring simple data for parameterization</li>
	<li>Types of Parameterized Tests</li>
	<li>Working with complex objects</li>
	<li>Using external data</li>
</ul>

<h2>Use case differences from theories</h2>

<p>Theories are best used for reversible operations that must succeed in every single situation. For example, a great use of a Theory might be testing a date conversion utility. Perhaps you have a class that converts dates from Gregorian calendar dates to dates on the Buddhist calendar.</p>
<p>Using a theory, you would supply hundreds or thousands of Gregorian dates as data points. For each data point, you might convert the date to the equivalent Buddhist date. You would then convert each Buddhist date back to the Gregorian date and ensure it matched the original.</p>
<p>With the following pseudocode:</p>

```
[DataPoints]
[ArrayElementType("Date")]
public static var dates:Array = [ 1/1/2009, 
			2/2/2010, 
			3/3/2011, 
			1/8/1970,
			11/12/2090 ];
```

<p>Tested under this situation</p> 

```
[Theory]
public function shouldConvertSuccessfully( date:Date ):void {
	var convert:Converter = new Converter();
	var bDate:BDate = converter.convertToBuddhistDate( date );
	var gDate:Date = converter.convertToGregorianDate( bDate );
	assertEquals( date, gDate );
}
```

<p>This type of reversibility is the hallmark of a good theory case.</p>
<p>Also, remember that Theories are about using combinations of data. Given the following pseudocode:</p>

```
[DataPoints]
[ArrayElementType("String")]
public static var dates:Array = [ "Trevor", "Joe", "Rob", "Steve" ];
[Theory]
public function traceNames( name1:String, name2:String ):void {
  trace( name1 + " - " + name2 );
}
```

<p>The expected output is:</p>

```
Trevor - Trevor
Trevor - Joe
Trevor - Rob
Trevor - Steve
Joe - Trevor
Joe - Joe
Joe - Rob
Joe - Steve
Rob - Trevor
Rob - Joe
Rob - Rob
Rob - Steve
Steve - Trevor
Steve - Joe
Steve - Rob
Steve - Steve
```

<p>This use of data point combinations starkly contrasts the use case for Parameterized testing that you will learn in the remainder of the lesson.</p>

<h2>Declaring simple data for parameterization</h2>

<p>Parameterized test cases, like Theories, have methods that accept parameters. However, that is where the comparison ends. Parameterized testing gives us a way to create tests that will use a multitude of known parameters and results.  The intention of the Parameterized test is to eliminate the need to copy and paste the same test multiple times, simply changing the input values.</p>
<p>Parameterized tests require the Parameterized runner included with FlexUnit 4.1 and later.  To use the parameterized test runner, add the RunWith metadata to the class definition.</p>

```
[RunWith("org.flexunit.runners.Parameterized")]
```

<p>Parameter data is provided as an array. Inside of the array, there is an array for each argument set to be passed for each test case.  For example:</p>

```
public static var names:Array = [ 
	[ "Stevens", "Mary", "Mary Stevens" ], 
	[ "Doe", "John", "John Doe" ] ];
[Test]
public function nameCombinerTest( lastName:String, firstName:String, 
					expectedResult:String ):void {
	assertEquals( expectedResult, ( firstName + " " + lastName ) );
}
```

<p>There are actually two types of Parameterized tests supported by FlexUnit 4.x, which you will learn to use next.</p>

<h2>Types of parameterized test</h2>

<p>To use any form of parameterized tests requires a custom runner.  FlexUnit 4.1 includes the ParameterizedRunner which can run TestNG and JUnit style parameterized tests.</p>

<h3>TestNG</h3>

<ul>
	<li>Allows specification of different data for each test method</li>
	<li>Uses the dataProvider annotation to specify the data source</li>
	<li>Tests have expected parameters specified as part of the test</li>
	<li>Test methods will follow the order of the data provider array</li>
	<li>Each test can take only one data provider</li>
</ul>
<p>Example</p>

```
public static var data:Array = [ [ 0, 0 ], [ 1, 2 ], [ 2, 4 ] ];
[Test( dataProvider="data")]
public function doubleTest( value1:int, value2:int ):void {
	assertEquals( value1, value2 * 2 );
}
```

<h3>JUnit</h3>

<ul>
	<li>Best for data that is needed by multiple test methods</li>
	<li>All tests will use the same data</li>
	<li>Data must be decorated with [Parameters] metadata</li>
	<li>Values are assigned in the constructor</li>
	<li>Instance variables are used to store each value</li>
	<li>Test methods take no arguments</li>
</ul>
<p>Example</p>

```
[Parameters]
public static var data:Array = [ [ 0, 0 ], [ 1, 2 ], [ 2, 4 ] ];
[Test]
public function doubleTest():void {
	assertEquals( value1, value2 * 2 );
}
private var _value1:int;
private var _value2:int;
public function JUnitStyleTest( value1:int, value2:int ) {
	_value1 = value1;
	_value2 = value2;
}
```

<h2>Working with complex objects</h2>

<p>Any type of data can be used in parameterized testing. In fact, some of the most effective testing can occur when you begin using entire objects as the parameters to methods. The following examples are all valid and intended to make you consider how polymorphism and complex types can enhance your testing capabilities.</p>

```
public static var data:Array = [ [ new DataGrid(), 50, 70 ],
				[ new Label(), 40, 30 ],
				[ new Button(), 80, 50 ] ];
[Test(dataProvider="data")]
public function positionTest( component:UIComponent, x:Number, y:Number ):void {
	position( component );
	assertEquals( x, component.x );
	assertEquals( y, component.y );
}
public static var data:Array = [ [ new DollarFormatter(), 50, "$50" ],
			[ new EuroFormatter(), 50, "&euro;50" ],
			[ new PoundFormatter(), 50, "&pound;50" ] ];
[Test(dataProvider="data")]
public function positionTest( formatter:IFormatter, value:Number, result:String ):void {
	assertEquals( result, formatter.format( value ) );
}
```

<h2>Walkthrough 1: Creating a TestNG Style Parameterized Test</h2>

<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Create a new class to run tests with parameterized data.</li>
	<li>Apply the TestNG style of parameterized testing.</li>
</ul>

<h3>Steps</h3>

<ol>
	<li>
		<p>Import the FlexUnit4Training_wt1.fxp project from the Unit 11/Start folder. Please refer to Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
	</li>
	<li>
		<p>Create a new ActionScript class named GetPointsTest.as in the math.testcases package within the tests folder.</p>
		<h3><br />Parameterized Testing Setup</h3>
	</li>
	<li>
		<p>Mark the GetPointsTest class definition with <code>[RunWith("org.flexunit.runners.Parameterized")]</code> metadata.</p>

```
[RunWith("org.flexunit.runners.Parameterized")]
public class GetPointsTest {
...
}
```

</li>
	<li>
		<p>Add a private static constant named <code>TOLERANCE</code> of data type <code>Number</code> to the class. Set <code>TOLERANCE</code> to <code>.0001</code>.</p>

```
private static const TOLERANCE:Number = .0001;
```

</li>
	<li>
		<p>Declare a public static array named data within the <code>GetPointsTest</code> class. The array should contain a sub array with point values as shown.</p>

```
public static var data:Array = [
		[ new Circle( new Point( 0, 0 ), 5 ), new Point( 5, 0 ), 0 ],
		[ new Circle( new Point( 0, 0 ), 5 ), new Point( -5, 0 ), Math.PI ] 
];
```

<p>If you did not use code-completion, add the import for net.digitalprimates.math.Circle and flash.geom.Point at this time.</p>
<p>The data array contains two arrays. Each is passed as a set of parameters, and each represents one run cycle for the tests that take the data. Soon, you will write a method which takes 3 parameters, a circle, a point and a number of radians.  That test will run twice, one run using the first array and another using the second.</p>
	</li>
	<li>
		<p>Add a public method named <code>shouldGetPointsOnCircle()</code> to the class. Mark the method with the <code>[Test(dataProvider="data")]</code> metadata.</p>

```
[Test(dataProvider="data")]
public function shouldGetPointsOnCircle():void {
}
```

<p>Add a <code>circle</code> parameter of type <code>Circle</code>, a point parameter of type <code>Point</code>, and a <code>radians</code> parameter of type <code>Number</code> to the <code>shouldGetPointsOnCircle()</code> method.</p>
		<p>The <code>[Test(dataProvider="data")]</code> metadata over the test method will ensure that the data array's values get passed in as the test method's arguments. This is known as TestNG style parameterized testing, because the data is passed into specific test methods via the metadata. TestNG is a testing framework for Java, it pioneered this annotations-based approach.</p>

```
[Test(dataProvider="data")]
public function shouldGetPointsOnCircle( circle:Circle, point:Point, radians:Number ):void {
}
```

</li>
	<li>
		<p>Add a call to the <code>assertThat()</code> method. Pass <code>circle.getPointOnCircle( radians )</code> and <code>new CloseToPointMatcher( point, TOLERANCE )</code> as its arguments.</p>

```
[Test(dataProvider="data")]
public function shouldGetPointsOnCircle( circle:Circle, point:Point, radians:Number ):void {
	assertThat( circle.getPointOnCircle( radians ), new CloseToPointMatcher( point, TOLERANCE ) );
}
```

<p>If you did not use code-completion, add the imports for org.flexunit.assertThat and matcher.CloseToPointMatcher at this time.</p>
		<p>The <code>shouldGetPointsOnCircle()</code> method uses the <code>assertThat()</code> statement with the <code>CloseToPointMatcher</code> to create the same kind of test coverage presented in BasicCircleTest class through the <code>shouldGetTopPointOnCircle()</code> and <code>shouldGetBottomPointOnCircle()</code> methods.</p>
	</li>
	<li>
		<p>Save the GetPointsTest.as file.</p>
		<h3><br />Add the new case to the CircleSuite</h3>
	</li>
	<li>
		<p>Open the CircleSuite.as file in the math.testcases package.</p>
	</li>
	<li>
		<p>Add a public variable named <code>test6</code> of type <code>GetPointsTest</code>.</p>

```
[Suite]
[RunWith("org.flexunit.runners.Suite")]
public class CircleSuite{
	public var test1:BasicCircleTest;
	public var test2:CircleConstructorTest;
	public var test3:CircleTheory;
	public var test4:CircleMockTest;
	public var test5:DistanceTest;
	public var test6:GetPointsTest;
}
```

</li>
	<li>
		<p>Save CircleSuite.as.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
		<p>If FlexUnit4Training.mxml ran successfully you should see the following output in your browser window:</p>
		<img alt='TestsPassed' id='shift' src='../images/unit11/image1.png' />
		<p class='caption' id='shift'>Figure 1: FlexUnit tests passed</p>
	</li>
</ol>

<h2>Walkthrough 2: Creating a JUnit Style Parameterized Test</h2>

<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Add another parameterized test to the GetPointsTest class.</li>
	<li>Apply the JUnit style of parameterized testing.</li>
</ul>

<h3>Steps</h3>

<ol>
	<li>
		<p>Open the GetPointsTest.as file located in the math.testcases package within the tests folder.</p>
		<p>Alternatively, if you didn't complete the previous lesson or your code is not functioning properly, you can import the FlexUnit4Training_wt2.fxp project from the Unit11/ start folder. Please refer to Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
		<h3><br />Add Instance variables</h3>
	</li>
	<li>
		<p>Add three private variables to the <code>GetPointsTest</code> class. One named <code>circle</code> of type <code>Circle</code>, another named <code>point</code> of type <code>Point</code>, and another named <code>radians</code> of type <code>Number</code>.</p>

```
private var circle:Circle;
private var point:Point;
private var radians:Number;
```

</li>
	<li>
		<p>Remove the <code>(dataProvider="data")</code> portion of the metadata from the <code>shouldGetPointsOnCircle()</code> test method. Also remove the parameters, as they will be replaced by the instance variables just created.</p> 

```
[Test(dataProvider="data")]
public function shouldGetPointsOnCircle( circle:Circle, point:Point, radians:Number ):void {
...
}
```

<p>Becomes</p>

```
[Test]
public function shouldGetPointsOnCircle():void {
...
}
```

<h3><br />Add a method to test point distance</h3>
	</li>
	<li>
		<p>Add a public function named <code>shouldReturnDistanceEqualToRadius()</code> to the class.</p>

```
[Test]
public function shouldReturnDistanceEqualToRadius():void {
}
```

</li>
	<li>
		<p>Within the new method, declare a variable named <code>distance</code> of data type <code>Number</code>. Set it equal to <code>Point.distance( circle.getPointOnCircle( radians ), circle.origin )</code>.</p>

```
[Test]
public function shouldReturnDistanceEqualToRadius():void {
	var distance:Number = Point.distance( circle.getPointOnCircle( radians ), circle.origin );
}
```

</li>
	<li>
		<p>Add a call to the <code>assertThat()</code> method. Pass in <code>distance</code> and <code>equalTo( circle.radius )</code> as its arguments.</p>

```
[Test]
public function shouldReturnDistanceEqualToRadius():void {
	var distance:Number = Point.distance( circle.getPointOnCircle( radians ), circle.origin );

	assertThat( distance, equalTo( circle.radius ) );
}
```

<p>If you did not use code-completion, add the import for org.hamcrest.object.equalTo at this time.</p>
	</li>
	<li>
		<p>Now you will need to create a class constructor function that assigns the parameters to the variables declared above. It will take in a circle , point, and radians parameters and assign it to the instance variable for each.</p>

```
public function GetPointsTest( circle:Circle, point:Point, radians:Number ) {
	this.circle = circle;
	this.point = point;
	this.radians = radians;
}
```

</li>
	<li>
		<p>Mark the data array with <code>[Parameters]</code> metadata.</p>

```
[Parameters]
public static var data:Array = 
[ [ new Circle( new Point( 0, 0 ), 5 ), new Point( 5, 0 ), 0 ],
  [ new Circle( new Point( 0, 0 ), 5 ), new Point( -5, 0 ), Math.PI ] ];
```

<p>In the previous walkthrough, parameters were passed in using the <code>dataProvider="data"</code> metadata. In this case, parameters are marked with <code>[Parameters]</code> metadata, which passes them into the constructor before each test method is run. This is the parameterized testing approach used in JUnit, a popular Java Unit testing framework on which FlexUnit bases much of its syntax.</p>
	</li>
	<li>
		<p>Save GetPointsTest.as.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
		<p>If FlexUnit4Training.mxml ran successfully you should see the following output in your browser window:</p>
		<img alt='TestsPassed' id='shift' src='../images/unit11/image2.png' /> 
		<p class='caption' id='shift'>Figure 1: FlexUnit tests passed</p>
	</li>
</ol>

<h2>Using external data</h2>

<p>External data for parameterized testing is similar to external data in theories. All external data sources use a dataloader, which must implement the IExternalDependancyLoader interface.</p>

```
public static var dataRetriever:IExternalDependencyLoader = new ParamDataHelper( "myUrl" );
```

<p>Depending on the style, the loading method for data is different:</p>

<h3>TestNG</h3>

<ul>
	<li>Similar to theories</li>
	<li>Set a DataPoints variable and annotate it with the loader property</li>
</ul>

```
[DataPoints(loader="dataRetriever")]
public static var data:Array;
```

<h3>JUnit</h3>

<ul>
	<li>Loader is added as an annotation to the [Parameters]</li>
</ul>

```
[Parameters(loader="dataRetriever")]
public static var data:Array;
```

<p>For more info on loading external data, see Unit 9: External Data.</p>

<h2>Walkthrough 3: Loading External Parameters</h2>

<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Load parameterized data from an external XML file.</li>
	<li>Apply externally loaded parameters for greater test coverage.</li>
</ul>

<h3>Steps</h3>

<ol>
	<li>
		<p>Open the GetPointsTest.as file you created in the previous exercise.</p> 
		<p>Alternatively, if you didn't complete the previous lesson or your code is not functioning properly, you can import the FlexUnit4Training_wt3.fxp project from the Unit 11/Start folder. Please refer to Unit 2: Walkthrough 1Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
		<h3><br />Add the external data loader</h3>
	</li>
	<li>
		<p>Add a public static variable named <code>getDataPointsLoader</code> of type <code>GetPointsDataHelper</code> to the <code>GetPointsTest</code> class. Instantiate the <code>GetPointsDataHelper</code> passing it a the argument <code>"xml/circlePoints.xml"</code></p>

```
public static var getDataPointsLoader:GetPointsDataHelper =
 new GetPointsDataHelper( "xml/circlePoints.xml" );
```

<p>If you did not use code-completion, add the import for helper.GetPointsDataHelper at this time.</p>
		<p>The GetPointsDataHelper class implements IExternalDependencyLoader and IResponder interfaces. It is able to send and receive a data request in test case preparation. The loader itself references a data provider. In a parameterized test case, a static array uses these test parameters.</p>
	</li>
	<li>
		<p>Remove the static values from the data Array and modify the <code>[Parameters]</code> metadata to include a loader <code>[Parameters(loader="getDataPointsLoader")]</code> metadata. It should read as follows:</p>

```
[Parameters(loader="getDataPointsLoader")]
public static var data:Array
```

</li>
	<li>
		<p>Save the GetPointsTest.as file.</p>
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
		<p>If FlexUnit4Training.mxml ran successfully you should see the following output in your browser window:</p>
		<img alt='TestsPassed' id='shift' src='../images/unit11/image3.png' /> 
		<p class='caption' id='shift'>Figure 1: FlexUnit tests passed</p>
		<p>The reason that the number of tests rose significantly in this Walkthrough is because FlexUnit logs a test for each parameter set being tested. As a result, four tests have been added to the reporting window, and each represents a parameter in the circlePoints.xml file</p>
	</li>
</ol>

<h2>Summary</h2>
<ul>
	<li><p>The use case of Parameterized testing and Theories is different.</p></li>
	<li><p>Parameterized tests pass entire sets of arguments to a method. Theories define possible combinations.</p></li>
	<li><p>Parameterized require the RunWith metadata <code>[RunWith("org.flexunit.runners.Parameterized")]</code>.</p></li>
	<li><p>Public static arrays containing arrays of arguments feed data into test methods and constructors depending on the style.</p></li>
	<li><p>Two types of Parameterized testing: TestNG and JUnit.</p></li>
	<li><p>TestNG Style - Declares a dataProvider per test method, therefore allowing different tests to use different data.</p></li>
	<li><p>JUnit Style - Instantiates parameterized data to instance variables within the class constructor.</p></li>
	<li><p>Complex objects can be passed as parameterized data. </p></li>
	<li><p>External data can be loaded for parameterized testing with a custom loader.</p></li>
	<ul> 
		<li><p>The loader classes used for parameterized testing are created in exact same way as those used for theories.</p></li>
	</ul>
</ul>

<h2>Navigation</h2>
<ul>
    <li><a href="Unit-10.html">Unit 10 - Mock Classes</a></li>
    <li><a href="Unit-12.html">Unit 12 - Running Tests from Different Versions</a></li>
    <li><a href="../index.html">Table of Contents / Introduction</a></li>
</ul>
