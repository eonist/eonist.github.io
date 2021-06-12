Basic XML parsing in swift (Mac): <!--more-->

```swift
import Foundation

let xmlStr: String = "<media>" +
                        "<book>The shining</book>" +
                        "<music type=\"digital\"></music>" +
                        "<movie title=\"Psycho\"/>" +
                    "</media>"
func testing(){
    let xmlDoc: NSXMLDocument = try! NSXMLDocument(XMLString: xmlStr, options: 0)
    let rootElement:NSXMLElement = xmlDoc.rootElement()!
    let children:NSArray = rootElement.children!
    let count = children.count//or use rootElement.childCount
    let a = XMLParser.childAt(children, 0)
    XMLParser.value(a!)
    for (var i = 0; i < count; i++) {
        let child:NSXMLElement = XMLParser.childAt(children, i)!
        Swift.print("child.localName: " + "\(child.localName)")
        if let type:String = XMLParser.attribute(child, "type") { print("type: " + type) }
        //print(XMLParser.attribute(child, "type"))
        let attributes:[Dictionary<String,String>] = XMLParser.attributes(child)
        if(attributes.count > 0) {  print("attr.value: "+"\(attributes[0]["value"])") }

        print("value: " + XMLParser.value(child))

        //
        print("hasAttribute: " + "\(XMLAsserter.hasAttribute(child,"type"))")
        //XPath expressions
    }
}

testing()

```

NOTE: the above example works in xcode playground. (plug and play)


Basics of parsing XML in ios :


```swift
//parserDidStartDocument: This one is called when the parsing actually starts. It’s obvious that is called just once per XML document.
//parserDidEndDocument: This one is the complement of the first one, and is called when the parser reaches the end of the XML data.
//parser:parseErrorOccurred: This delegate method is called when an error occurs during the parsing. The method contains an error object, which you can use to define the actual error.
//parser:didStartElement:namespaceURI:qualifiedName:attributes: This one is called when the opening tag of an element is found.
//parser:didEndElement:namespaceURI:qName: On the contrary to the above method, this is called when the closing tag of an element is found.
//parser:foundCharacters: This method is called during the parsing of the contents of an element. Its second argument is a string value containing the character that was just parsed.

//EXAMPLE:

import UIKit

class TableViewController: UITableViewController, XMLParserDelegate {
    var books: [Book] = []
    var elementName: String = ""//name of each element name at variouse stages in the xml hierarchy
    var bookTitle = ""
    var bookAuthor = ""

    override func viewDidLoad() {
        super.viewDidLoad()
        if let path = Bundle.main.url(forResource: "books", withExtension: "xml") {
            if let parser = XMLParser(contentsOf: path) {//gets the content and creates the Parser instance
                parser.delegate = self//set this class as the Parsers delegate
                parser.parse()//initiates the parsing
            }
        }
    }
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    // MARK: - Table view data source
    override func numberOfSections(in tableView: UITableView) -> Int {
         return 1
    }
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return books.count
    }
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath as IndexPath)
        let book = books[indexPath.row]
        cell.textLabel?.text = book.bookTitle
        cell.detailTextLabel?.text = book.bookAuthor
        return cell
    }
    /**
     * At the begining of an element
     */
    func parser(_ parser: XMLParser, didStartElement elementName: String, namespaceURI: String?, qualifiedName qName: String?, attributes attributeDict: [String : String]) {
        self.elementName = elementName
        if elementName == "book" {
            if attributeDict["id"] == "2" {
                Swift.print("found it 👌")
            }
            bookTitle = String()
            bookAuthor = String()
        }
    }
    /**
     * At the end of an element
     */
    func parser(_ parser: XMLParser, didEndElement elementName: String, namespaceURI: String?, qualifiedName qName: String?) {
        if elementName == "book" {//when a book element completes parsing. Use the temp data and populate the Book struct, then append that book struct
            let book = Book.init(bookTitle: bookTitle, bookAuthor: bookTitle)
            books.append(book)
        }
    }
    /**
     * ON every character in the element
     */
    func parser(_ parser: XMLParser, foundCharacters string: String) {
        let data = string.trimmingCharacters(in: NSCharacterSet.whitespacesAndNewlines)

        if !data.isEmpty {//make sure its not empty data!?!?
            if elementName == "title" {//when elementName is title then append the char to the temp data
                bookTitle += data
            } else if elementName == "author" {//when elementName is title then append the char to the temp data
                bookAuthor += data
            }
        }
    }

}

```


```xml
<?xml version="1.0"?>
<catalog>
    <book id="1">
        <title>To Kill a Mockingbird</title>
        <author>Harper Lee</author>
    </book>
    <book id="2">
        <title>1984</title>
        <author>George Orwell</author>
    </book>
    <book id="b3">
        <title>The Lord of the Rings</title>
        <author>J.R.R Tolkien</author>
    </book>
    <book id="4">
        <title>The Catcher in the Rye</title>
        <author>J.D. Salinger</author>
    </book>
    <book id="5">
        <title>The Great Gatsby</title>
        <author>F. Scott Fitzgerald</author>
    </book>
</catalog>

```
