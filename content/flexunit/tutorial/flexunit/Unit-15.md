Title:  Unit 15 - Creating Testable Code

<a href="../code/Unit15.zip"><img src="../images/DownloadIcon.png" alt="Download" /> Download Unit Project Files</a>

<p>The ability to thoroughly test a class or component is an indication of good design. In situations where a component has unexposed functionality, it can be very difficult to write tests that ensure the component functions as expected. Refactoring a class for better unit testing is therefore great for quality assurance as well as coding practice.</p>

<h3>Objectives:</h3>

<p>After completing this lesson, you should be able to:</p>
<ul>
	<li>Identify characteristics in your code that may necessitate refactoring for better unit testing</li>
</ul>

<h3>Topics</h3>

<p>In this unit, you will learn about the following topics:</p>
<ul>
	<li>Why highly cohesive code tends to test more easily</li>
	<li>Why loosely coupled code can be tested less painfully</li>
	<li>Refactoring an un-testable piece of code to something testable</li>
	<li>Creating tests for legacy code</li>
</ul>

<h2>Why highly cohesive code tends to test more easily</h2>

<p>Highly cohesive code creates strong test fixtures and short, simple tests. A class with low cohesion will frequently have us begging the question, "does this belong in the fixture?" If there is a section of code that <i>should</i> be in the fixture but doing so will create unnecessary overhead for some of the tests we are likely looking at a case of low cohesion. Always remember that a class with high cohesion tends to create tests with high cohesion.</p>  
<p>Consider the following database example:</p>

```
public class ContactDatabaseConnection {
	public function ContactDatabaseConnection () {
	}
	public function connect():void {
		//code to connect
	}
	public function getContact( itemName:String ):Contact {
		//code to retrieve contact
	}
	public function formatContact( contact:Contact ):FormattedContact {
		//code to format contact
	}
}
```

<p>Try to imagine the fixture for this interface. The test fixture would need to create a new database connection. It would also need to create a new contact for testing.</p> 

```
private var testContact:Contact;
private var connection:ContactDatabaseConnection;
	[Before]
	public function setup():void {
		connection = new ContactDatabaseConnection();
		connection.connect();
		testContact = new Contact();
}
```

<p>However, the <code>formatContact()</code> method does not need a database connection. In fact, what does formatting a contact have to do with a <code>ContactDatabaseConnection</code>? These methods do not have a unified test fixture. In one section we would be testing the connect and retrieval functions. The other requires a contact be ready made. Clearly, <code>formatContact()</code> does not belong.</p>
<p>If you find yourself needing a unique fixture for each test in a class it is highly likely you are dealing with a class that contains low cohesion or only temporal cohesion at best. In this situation, you might want to return to the code and find a way to split the class to increase the cohesion.</p> 
<p>Here is an improved version of the <code>ContactDatabaseConnection</code> class that should be much easier to test and maintain:</p>

```
public class ContactDatabaseConnection {
	public function ContactDatabaseConnection () {
	}
	public function connect():void {
		//code to connect
	}
	public function getContact( itemName:String ):IContact {
		//code to retrieve contact
	}
}
```

<p>And moving the <code>formatContact</code> method into the specific <code>Contact</code> implementation:</p>

```
public class Contact implements IContact {
	//contact code
	public function formatContact():void {
		//format code
	}
}
```

<h2>Why loosely coupled code can be tested less painfully</h2>

<p>By creating loosely coupled code, we create classes that can more easily be tested in isolation. Tightly coupled code creates a dependency on outside resources that frequently do not belong in the class with which they are coupled.</p>
<p>Consider the following:</p>

```
public class BoxOfCrayons() {
	private var crayons:Array = new Array();
	public function addCrayon( color:String ):void {
		var crayon = new WaxCrayon();
		crayon.color = color;
		crayon.wrapper.color = color;
	}
}
```

<p>Think of the tests you would need to write for the <code>addCrayon()</code> method:</p>
<ul>
	<li>Test that a crayon is added to the box of crayons</li>
	<li>Test that the crayon is the color you are trying to add</li>
	<li>Test that the wrapper has the correct color</li>
</ul>
<p>This section of code is tightly coupled for a few reasons. First, BoxOfCrayons is making its own crayons. What if BoxOfCrayons can also contain ShortCrayon or ClayCrayon? Even worse, why is BoxOfCrayons modifying crayon wrappers at all? <code>addCrayon()</code> is now tightly coupled to this exact implementation of WaxCrayon. What if we decide that WaxCrayons do not have wrappers? You would also need to change the implementation of <code>addCrayon()</code>.</p>
<p>Tightly coupled code:</p>
<ul>
	<li>Creates a dependency on a specific implementation.</li>
	<li>Requires pulling potentially untested code into the test.</li>
	<li>Has more room for errors. When a test fails it is difficult to definitively tell which portion of the code failed.</li>
	<li>Requires the unit tests to be rewritten each time a coupled component is changed.</li>
	<li>Defeats the basic purpose of unit testing. If you have to rewrite the unit tests each time you change an unrelated class the unit tests are not effectively doing their job.</li>
</ul>

<h2>Refactoring an un-testable piece of code to something testable</h2>

<p>There is no simple answer on the best way to refactor an untestable object. For some, it may be as simple as using a getter to expose a previously hidden property. For others, where objects are tightly coupled, may require a complete rewrite. The testability of a piece of code depends entirely on its available seams.  In code without seams, one must rely on any "magic" performed behind the scenes. If what you put in does not yield what you expected you have no way of knowing where everything went wrong.</p>
<p>When refactoring, ask yourself these questions:</p>
<ul>
	<li>Is my code loosely coupled?</li> 
	<li>Is my code cohesive?</li> 
	<li>Are my properties inspectable?</li>
	<li>Do my methods return values?</li>
	<li>Am I using dependency injection where appropriate?</li>
</ul>
<p>If you answer "no" to any of these questions, that might be a good place to start refactoring</p>

<h2>Creating tests for legacy code</h2>

<p>Legacy code tests tend to be the most difficult to test. Frequently the original code is not written with any testing in mind and may require significant refactoring. Refactoring the code to support functionality can potentially break other sections of code creating a veritable cornucopia of bugs.</p>
<p>When refactoring legacy code, keep the following in mind:</p>
<ul>
	<li><p>Refactor slowly. Make changes, create and run tests, then move on to the next change.</p></li>
	<li><p>Never refactor without tests to back up the refactor.</p></li>
	<li><p>Watch for constructors doing a lot of work. Use dependency injection to replace that work. This has the added benefit of creating a seam for testing in addition to reducing the coupling of the code.</p></li>
	<li><p>Focus on test coverage. It's not always possible to test certain code areas without significant alterations of the code base. Always question if refactoring is worth the possibility of completely breaking the rest of the build.</p></li>
	<li><p>Focus on critical functionality. Ensure that the most important parts work. Writing tests for code built on untested code is no guarantee since we do not even know if the core functions as expected.</p></li>
</ul>

<h2>Walkthrough 1: Refactoring an object to become testable</h2>

<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Examine a class with missing seams</li>
	<li>Refactor the class to allow for isolated testing</li>
</ul>

<h3>Steps</h3>

<ol>
	<li>
		<p>Import the FlexUnit4Training_wt1.fxp project from the Unit 15/Start folder. Please refer to Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
	</li>
	<li>
		<p>Open CircleLayout.as in the net.digitalprimates.components.layout package.</p>
	</li>
	<li>
		<p>Examine the <code>updateDisplayList()</code> method in this class.</p>
```
override public function updateDisplayList( contentWidth:Number, contentHeight:Number ):void {
	...							
	super.updateDisplayList( contentWidth, contentHeight );
	var circle:Circle = new Circle( new Point( contentWidth/2, contentHeight/2 ),
	 getLayoutRadius( contentWidth, contentHeight ) );
	for ( i = 0; i < target.numElements; i++ ) {
		element = target.getElementAt(i);
		elementLayoutPoint = circle.getPointOnCircle( elementAngleInRadians );
		...			
		//Used to be move()
		element.setLayoutBoundsPosition( elementLayoutPoint.x-elementWidth,
		 elementLayoutPoint.y-elementHeight );
	}
}
```
		<p>This method creates a <code>circle</code> object of type <code>Circle</code> with dimensions based on the <code>contentWidth</code> and <code>contentHeight</code> arguments. The elements of the layout are positioned to points on the circle.</p>
		<p>Suppose you wanted to test the functionality of this method. It would be impossible to isolate because the method is dependent on the <code>Circle</code> class.</p>
		<p>The circle object could be mocked if the <code>CircleLayout</code> class had its own <code>circle</code> property instead.</p>
	</li>
	<li>
		<p>At the top of the class, create a private circle property.</p>

```
private var circle:Circle;
```

</li>
	<li>
		<p>Generate a public getter and setter for the property. Right-click the property, select Source &#62; Generate Getter/Setter, and click OK.</p>

```
public function get circle():Circle {
	return _circle;
}
public function set circle(value:Circle):void {
	_circle = value;
}
```

</li>
	<li>
		<p>Add a call to the <code>invalidateDisplayList()</code> method on the <code>target</code> property within the setter of the <code>circle</code>.</p>

```
public function set circle(value:Circle):void {
	circle = value;
	target.invalidateDisplayList();
}
```

</li>
	<li>
		<p>Remove the line that instantiates the <code>circle</code> object within the <code>updateDisplayList()</code> method.</p>
		<code>var circle:Circle = new Circle( new Point( contentWidth/2, contentHeight/2 ), getLayoutRadius( contentWidth, contentHeight ) );</code>
	</li>
	<li>
		<p>Remove the <code>getLayoutRadius()</code> method from the class.</p>
	</li>
	<li>
		<p>Perform a null check for the <code>circle</code> within <code>updateDisplayList()</code> method.</p>

```
super.updateDisplayList( contentWidth, contentHeight );
if(circle) {
	for ( i = 0; i < target.numElements; i++ ) {
		...
	}
}
```

<p>The <code>CircleLayout</code> class can now be better tested as it is not dependent on another class.</p>
	</li>
</ol>

<h2>Walkthrough 2: Refactoring an object to become testable</h2>

<p>In this walkthrough you will perform the following tasks:</p>
<ul>
	<li>Add a new method named <code>distanceFrom()</code> to the <code>Circle</code> class.</li> 
	<li>Modify the <code>Circle</code> classes' <code>equals()</code> method to apply the <code>distanceFrom()</code> method.</li> 
</ul>

<h3>Steps</h3>

<ol>
	<li>
		<p>Open the Circle.as file from the net.digitalprimates.math package.</p>
		<p>Alternatively, if you didn't complete the previous lesson or your code is not functioning properly, you can import the FlexUnit4Training_wt2.fxp project from the Unit 15/Start folder. Please refer to Unit 2: Walkthrough 1 for instructions on importing a Flash Builder project.</p>
		<h3><br />Add the distanceFrom() method</h3>
	</li>
	<li>
		<p>Just below the <code>equals()</code> method, add a new public method named <code>distanceFrom()</code>. It should return a value of data type <code>Number</code>. It should take a parameter named <code>circle</code> of type <code>Circle</code>.</p>

```
public function distanceFrom( circle:Circle ):Number {
}
```

</li>
	<li>
		<p>The new method should return <code>Point.distance( this.origin, circle.origin )</code>, which calculates the distance between the <code>Circles</code>' origin points.</p>

```
public function distanceFrom( circle:Circle ):Number {
	return Point.distance( this.origin, circle.origin );
}
```

<h3><br />Modify the equals() method</h3>

<p>The <code>equals()</code> method in <code>Circle</code> may appear simple enough, but can be difficult to test. This comparison is based on radius and origin. Although the radius comparison is about as simple as it could be, the point comparison is based on the accessing the <code>origin.x</code> and <code>origin.y</code> and asserting their equality separately. If you were to isolate and mock the circle for testing, you would have to mock the <code>Point</code> object as well.</p> 

```
public function equals( circle:Circle ):Boolean {
	var equal:Boolean = false;
	if ( ( circle ) && ( this.radius == circle.radius ) && ( this.origin ) &&
	 ( circle.origin ) ) {
		if ( ( this.origin.x == circle.origin.x ) && ( this.origin.y == circle.origin.y ) ) {
			equal = true;
		}
	}
	return equal;
}
```

<p>Creating an isolated object for testing will ultimately reduce these interdependencies. Mocks should be able to work without the required input of additional mocks. Creating code of this kind will add seams, loosen coupling, and improve quality over all.</p>
	</li>
	<li>
		<p>In the <code>equals()</code> method, replace the line of the second if statement, which checks the equality of this and the comparison circle's origin's x and y values, with a line that checks that <code>this.distanceFrom( circle )</code> is equal to <code>0</code>.</p> 

```
if ( ( this.origin.x == circle.origin.x ) &&
( this.origin.y == circle.origin.y ) ) {
	equal = true;
}
```

<p>Becomes:</p>

```
if ( this.distanceFrom( circle ) == 0 ) {
	equal = true;
}
```

<p>The <code>Circle</code> class's new <code>equals()</code> method should read as follows:</p>

```
public function equals( circle:Circle ):Boolean {
	var equal:Boolean = false;
	if ( ( circle ) && ( this.radius == circle.radius ) &&
	( this.origin ) && ( circle.origin ) ) 	{
		if ( this.distanceFrom( circle ) == 0 ) {
			equal = true;
		}
	}
	return equal;
}
```

<p>At this point, if you were to mock a <code>Circle</code>, you would only need to mock and create expectations for a <code>circle</code> object. A mock for the <code>Point</code> class is not needed here.</p>
	</li>
	<li>
		<p>Save the Circle.as file</p>
		<h3><br />Run the unit tests</h3>
		<p>This is a great example of why automated unit testing is so beneficial in the development life cycle. In your modifications of the Circle.as class, it's very possible that you could have broken some functionality in the class and elsewhere. Fortunately, there are a wealth of unit test cases, many of them dealing with the <code>Circle</code> class and even the <code>equals()</code> method.</p>
	</li>
	<li>
		<p>Open the FlexUnit4Training.mxml file.</p> 
	</li>
	<li>
		<p>Run the FlexUnit4Training.mxml file.</p>
	</li>
</ol>

<h2>Summary</h2>

<ul>
	<li><p>Code that tests easily is</p></li>
	<ul>
		<li><p>Highly cohesive</p></li>
		<li><p>Loosely coupled</p></li>
	</ul>
	<li><p>Refactoring code to become testable</p></li>
	<ul>
		<li><p>Generally, creates better code</p></li>
		<li><p>Adds necessary seams in the code</p></li>
		<li><p>Favors simplicity</p></li>
	</ul>
	<li><p>Testing legacy code should be approached with caution.</p></li>
	<ul>
		<li><p>Do not refactor without tests in place to backup</p></li>
	</ul>
	<li><p>Black box classes need to make their properties and methods available for testing.</p></li>
	<ul>
		<li><p>Minimal inter method/class/property dependencies</p></li>
		<li><p>Public getter and setter functions</p></li>
	</ul>
</ul>

<h2>Navigation</h2>
<ul>
    <li><a href="Unit-14.html">Unit 14 - UIComponents</a></li>
    <li><a href="Unit-16.html">Unit 16 - Allowing Your Tests to Function with Continuous Integration</a></li>
    <li><a href="../index.html">Table of Contents / Introduction</a></li>
</ul>