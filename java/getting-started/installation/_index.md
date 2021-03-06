---
title: Installation
description: "Install Aspose.Words for Java from Maven repository. Define the Aspose.Words for Java dependency in your pom.xml"
type: docs
weight: 110
url: /java/installation/
---

Make sure your machine meets the [system requirements](https://docs.aspose.com/words/java/system-requirements/) before you begin.

This article explains how to instal Aspose.Words for Java on your computer.

## Install Aspose.Words for Java from Maven Repository

Aspose hosts all Java APIs in [Maven repository](https://repository.aspose.com/webapp/#/artifacts/browse/tree/General/repo/com/aspose/aspose-words). You can easily use Aspose.Words for Java API directly in your Maven Projects with simple configurations:

1. First, you need to specify Aspose Maven Repository configuration/location in your Maven pom.xml as shown below:
	{{< highlight csharp >}}
	<repositories>
		<repository>
			<id>AsposeJavaAPI</id>
			<name>Aspose Java API</name>
			<url>https://repository.aspose.com/repo/</url>
		</repository>
	</repositories>
	{{< /highlight >}}
2. Then, define the Aspose.Words for Java API dependency in your pom.xml as follows:
	{{< highlight csharp >}}
	<dependencies>
		<dependency>
			<groupId>com.aspose</groupId>
			<artifactId>aspose-words</artifactId>
			<version>20.11</version>
			<classifier>jdk17</classifier>
		</dependency>
		<dependency>
			<groupId>com.aspose</groupId>
			<artifactId>aspose-words</artifactId>
			<version>20.11</version>
			<classifier>javadoc</classifier>
		</dependency>
	</dependencies>
	{{< /highlight >}}
3. Congratulations! You have successfully defined the Aspose.Words for Java dependency in your Maven project.

## See Also

* [Download Aspose.Words from Maven](https://downloads.aspose.com/words/java)