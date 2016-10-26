# TBXMLReader

A light-weight XML reader written in C# with an eye towards providing the fastest possible XML parsing while utilizing the minimum amound of resources. Written in C# and performance tested in Unity, TBXMLReader compared very favorably to the stock XmlReader class included in Unity 5.4.1.

## How to use

The most efficient interation with TBXMLReader is simple. Instantiate a new class, pass it a byte[] of the contents of the XML to parse, and provide two callback delegates; one to process the opening of XML elements, and one to process the closing of XML elements. Each of these delegates is passed a TBXMLElement, which allows access to the name and attributes of the current element.

````
new TBXMLReader (xmlBytes, (element) => {
	// process opening XML elements here
	string elementName = element.GetName ();
	string colorAttribute = element.GetAttribute ("color");
},(element) => {
	// process closing XML elements here
});
````


## Performance

All performance testing was conducted on an iPad 3 using IL2CPP builds of a simple test application in Unity 5.4.2f1; while we have other testing rigs in place (for example, a straight mono compile and test on random desktop environment), since our particular focus is on mobile performance we've included the numbers of those runs.

The test app loads and processes a 12 MB XML file using the following code:

````
void Start () {
	TextAsset testXML = Resources.Load<TextAsset> ("performance.xml");

	BeginTest ();
	new TBXMLReader (testXML.bytes, (element) => {
	}, (element) => {
	});
	EndTest ("TBXMLReader");

	BeginTest ();
	XmlReader reader = XmlReader.Create (new System.IO.StringReader (testXML.text));
	// Parse the file and display each of the nodes.
	while (reader.Read()){}
	EndTest ("XmlReader");
}
````

And finally, the results of the test:

```
[TBXMLReader] Memory usage during XML parse: 11.50391 MB
[TBXMLReader] Time to XML parse: 955 ms
[XmlReader] Memory usage during XML parse: 23.00781 MB
[XmlReader] Time to XML parse: 4622 ms
```

Since TBXMLReader is built to process on a byte[], we can avoid an additional conversion of the raw data. TBXMLReader is nearly 5x faster than the System.Xml.XmlReader (mono 2.x reader included in Unity 5.4.2f1).