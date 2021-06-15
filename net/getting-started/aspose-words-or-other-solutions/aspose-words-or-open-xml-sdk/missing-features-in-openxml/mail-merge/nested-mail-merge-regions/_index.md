---
title: Nested Mail Merge Regions
type: docs
weight: 30
url: /net/nested-mail-merge-regions/
---

Most data in relational databases or XML files is hierarchical (e.g. with parent-child relationships). The most common example is an invoice or an order containing multiple items.

**Aspose.Words** allows nesting mail merge regions inside each other in a document to reflect the way the data is nested and this allows you to easily populate a document with hierarchical data.

This article details the steps of how to set up a working nested mail merge application to generate a collection of invoices where each contain multiple items. An example project with complete source code and files can be downloaded. The process and code will be explained step by step and common issues addressed at the end of the article.

## What are Nested Mail Merge Regions and When Would I use Them?

Nested mail merge regions are at least two regions in which one is defined entirely inside the other, so they are “nested” in one another. In a document it looks like this:

![todo:image_alt_text](/download/thumbnails/2588826/1120741448)

Just as in standard mail merge each region contains the data from one table. What’s different in nested mail merge is that the Order region has the Item region nested inside it. This makes the Order region the parent and the Item region the child. This means that when the data is merged from the data source, the regions will act just like a parent-child relationship where data coming from the Order table is linked to the Item table.

The example below shows the data being passed to the nested merge regions and the output that is generated by the merge.

![todo:image_alt_text](/download/thumbnails/2588826/836613697)

As you can see, each order from the Order table is inserted followed by each item from the Item table that is related to that order. Then the next order will be inserted along with their items until all the orders and items are listed.

### Step 1 – Create the Template

This is the same process as creating a standard mail merge document with regions. Remember that with mail merge regions we can have the same field name in different regions so there is no need to change any column names. Here is what our Word template looks like:

![todo:image_alt_text](/download/thumbnails/2588826/424832615)

There are a few things you need to consider when preparing nested mail merge regions and merge regions in general.

- The mail merge region opening and closing tag (e.g. TableStart:Order, TableEnd:Order) both need to appear in the same row or cell. For example, if you start a merge region in a cell of a table, you must end the merge region in the same row as the first cell.
- The names of the columns in the DataTable must match the merge field name. Unless you have specified mapped fields the merge will not be successful for those fields whose names are different.
- The opening and closing table tags need to be well formed. This means that the StartTable and EndTable table tags must match. An incorrectly formed region will cause all nested mail merge regions to stop displaying anything at all.
  If one of these rules is broken the program may produce unexpected results or an exception may be thrown.

### Step 2 – Create the Data Source

The data to be merged into the template can come from a variety of sources, mainly relational databases or XML documents. In our example we are going to use an XML file to store our data and load it straight into a DataSet using the inbuilt .NET functionality.

In our example, XML is used to store the data. There is a schema file found in the Data folder called **OrdersSchema.xsd** to ensure data integrity. Here are the contents of the files:

#### CustomerData.xml

**XML**

{{< highlight csharp >}}
<?xml version="1.0" encoding="utf-8" ?>
<Orders>
 <Order>
 <Number>23</Number>
 <Address>Nelson Street</Address>
 <Suburb>Howick</Suburb>
 <City>Auckland</City>
 <Phonenumber>543 1234</Phonenumber>
 <Date>03/01/2010</Date>
 <Total>14.00</Total>
  <Item>
  <Name>BBQ Chicken Pizza</Name>
  <Price>6.00</Price>
  <Quantity>1</Quantity>
  <ItemTotal>6.00</ItemTotal>
  </Item>
  <Item>
  <Name>1.5 Litre Coke</Name>
  <Price>4.00</Price>
  <Quantity>2</Quantity>
  <ItemTotal>8.00</ItemTotal>
  </Item>
 </Order>
...
</Orders>
{{< /highlight >}}

#### PizzaSchema.xsd

**XML**

{{< highlight csharp >}}
<?xml version="1.0" encoding ="utf-8"?>
<xs:schema id="OrdersSchema"  xmlns:xs="http://www.w3.org/2001/XMLSchema">
 <xs:element name="Orders">
 <xs:complexType>
 <xs:sequence>
  <xs:element name="Order">
  <xs:complexType>
  <xs:sequence>
  <xs:element name="Number"/>
  <xs:element name="Address"/>
  <xs:element name="Suburb"/>
  <xs:element name="City"/>
  <xs:element name="Phonenumber">
  <xs:element name="Date"/>
  <xs:element name="Total"/>
  <xs:element name="Item">
   <xs:complexType>
   <xs:sequence>
   <xs:element name="Name"/>
   <xs:element name="Price"/>
   <xs:element name="Quantity"/>
   <xs:element name="ItemTotal"/>
   </xs:sequence>
  </xs:complexType>
  </xs:element>
  </xs:sequence>
  </xs:complexType>
 </xs:element>
 </xs:sequence>
 </xs:complexType>
 </xs:element>
</xs:schema>
{{< /highlight >}}

These files should be included in our project folder:

![todo:image_alt_text](/download/thumbnails/2588826/1957624239)

### Step 3 – Ensure Correct Table Names and Relationships Exist Between Tables

For Aspose.Words to perform nested mail merge correctly, the following requirements must be met:

1. The names of the mail merge regions in the document must match the names of the DataTables populated from the data source.
1. The nesting order of mail merge regions in the document must match the data relationships between the tables in the data source.

Aspose.Words uses the data relationships defined in the DataSet to navigate to the correct data and process the nested mail merge regions, so having them set up is crucial to the merge working correctly.

In our example, we simply let .NET automatically load the data into tables and create the relationship for us through the nested structure of XML. When the DataSet object loads XML it either uses a provided schema or infers one from the structure of the XML itself to achieve this. From this structure it creates relationships between the tables where needed.

For more information about creating or inferring schemas using XML see the MSDN article here. Let’s take a look at the data loaded into our project. We can view the contents of the loaded data by using the Dataset Visualizer in the Visual Studio environment. To access the Visualizer:

1. Set a break point somewhere in the code.
1. Run the program in debug mode.
1. Hover the mouse over the DataSet and click on the magnifying glass icon.

![todo:image_alt_text](/download/thumbnails/2588826/506157511)

### Step 4 – Prepare the Code

The code for setting up nested mail merge is simple to implement with Aspose.Words. Remember when setting up your project:

To include the reference to **Aspose.Words**.

- Load each table from the data source into appropriate tables.
- Load the data into a DataSet object.
- We create a Document object which loads our invoice template. Then Aspose.Words merges the data from our DataSet and fills the document with data. Then we save the results into a document in the desired format. Here is the complete code for our project:

{{< highlight csharp >}}

    // Sample infrastructure.
    string exeDir = Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location) + Path.DirectorySeparatorChar;
    string dataDir = new Uri(new Uri(exeDir), @"../../Data/").LocalPath;

    // Create the Dataset and read the XML.
    DataSet pizzaDs = new DataSet();

    // Note: The Datatable.TableNames and the DataSet.Relations are defined implicitly by .NET through ReadXml.
    // To see examples of how to set up relations manually check the corresponding documentation of this sample
    pizzaDs.ReadXml(dataDir + "CustomerData.xml");

    // Open the template document.
    Document doc = new Document(dataDir + "Invoice Template.doc");

    // Execute the nested mail merge with regions
    doc.MailMerge.ExecuteWithRegions(pizzaDs);

    // Save the output to file
    doc.Save(dataDir + "Invoice Out.doc");
    Debug.Assert(doc.MailMerge.GetFieldNames().Length == 0, "There was a problem with mail merge");
public class DataRelationExample
{
public static void CreateRelationship()
{
    DataSet dataSet = new DataSet();
    DataTable orderTable = new DataTable();
    DataTable itemTable = new DataTable();
    //ExStart
    //ExId:NestedMailMergeCreateRelationship
    //ExSummary:Shows how to create a simple DataRelation for use in nested mail merge.
    dataSet.Relations.Add(new DataRelation("OrderToItem", orderTable.Columns["Order_Id"], itemTable.Columns["Order_Id"]));
    //ExEnd
}
public static void DisableForeignKeyConstraints()
{
    DataSet dataSet = new DataSet();
    DataTable orderTable = new DataTable();
    DataTable itemTable = new DataTable();
    //ExStart
    //ExId:NestedMailMergeDisableConstraints
    //ExSummary:Shows how to disable foreign key constraints when creating a DataRelation for use in nested mail merge.
    dataSet.Relations.Add(new DataRelation("OrderToItem", orderTable.Columns["Order_Id"], itemTable.Columns["Order_Id"], false));
    //ExEnd
}
{{< /highlight >}}

## Download Sample Code

- [Codeplex](https://asposeopenxml.codeplex.com/releases/view/617779)
- [Github](https://github.com/aspose-words/Aspose.Words-for-.NET/releases/tag/MissingFeaturesofOpenXMLWordsv1.1)
- [Code.MSDN](https://code.msdn.microsoft.com/Missing-Features-in-6a2c882b)